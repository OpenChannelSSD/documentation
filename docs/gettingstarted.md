# How to use

Open-Channel SSDs require support in the kernel. The code is currently in the
process of being upstreamed ([lkml](https://lkml.org/lkml/2015/7/22/541)), until
it is upstream, the source provided at the github site must be used.

To use the framework. The following must be met:

1. A compatible device driver that is registered with LightNVM. For example null_nvm, QEMU NVMe or Open-Channel SSD compatible hardware.
2. Initialized block manager on top of the device driver. The block manager maintains a list of free/in-use/bad  blocks on media and exposes a get/put block API that upper layers can use to allocate into the SSD address space.
3. A initialized target on top of the block manager that exposes the SSD address space. The target can expose a traditional block I/O interface, or more esoteric interfaces such as Key-value stores, object-stores and so forth.

# Compile Custom Kernel

Check out the Linux kernel at

   `git clone https://github.com/OpenChannelSSD/linux.git`

Make sure that the .config file at least includes:

    CONFIG_NVM=y
    # Hybrid target support (required to expose a block device)
    CONFIG_NVM_RRPC=y
    # Hybrid block manager support (required)
    CONFIG_NVM_BM_HB=y
    # For null_nvm support
    CONFIG_NVM_NULL_NVM=y
    # For NVMe support
    CONFIG_BLK_DEV_NVME=y

Compile the kernel and install using the guide for your distribution.

# Initialize using null_nvm driver
Instantiate the module with the following parameters

`queue_mode=2 gb=4 nr_devices=1 nvm_enable=1 nvm_num_channels=1`

That will instantiate the LightNVM driver with a 4GB SSD, with a single channel. You can see that it was instantiated by checking the kernel log. 

`dmesg |grep nvm`

where the output should be similar to

    [    2.120740] nvm: registered nulln0 with luns: 1 blocks: 2048 sector size: 4096
    [    2.325021] nvm: registered nvme0n1 with luns: 1 blocks: 2044 sector size: 4096
    [  188.228560] nvm: rrpc initialized with 1 luns and 261632 pages.

# Instantiate NVMe driver using QEMU

If you have a LightNVM-compatible device, simply plug it in and it should be found. If you don't, then you can use the LightNVM-enabled QEMU branch to prototype with.

It's based on top of Keith Busch's qemu-nvme branch that implements an NVMe compatible device.

## QEMU Installation

Clone the qemu source from

    git clone https://github.com/OpenChannelSSD/qemu-nvme.git

and configure the QEMU source with

    ./configure --enable-linux-aio --target-list=x86_64-softmmu --enable-kvm

then make and install

## Configure QEMU

Create an empty file to hold your NVMe device.

    dd if=/dev/zero of=blknvme bs=1M count=1024

this creates a zeroed 1GB file called "blknvme". From there, you can boot your favorite Linux image with 

    qemu-system-x86_64 -m 4G -smp 1,cores=4 --enable-kvm 
    -hda YourLinuxVMFile -append "root=/dev/sda1"
    -kernel "/home/foobar/git/linux/arch/x86_64/boot/bzImage"
    -drive file=blknvme,if=none,id=mynvme
    -device nvme,drive=mynvme,serial=deadbeef,namespaces=1,lver=1,lchannels=1,nlbaf=5,lba_index=3,mdts=10

Where you replace YourLinuxVMFile with your preinstalled Linux virtual machine. LightNVM is enabled with lver=1. The number of LightNVM channels is set to one, and the last part defines the page size to be 4K.

QEMU support the following LightNVM-specific parameters:

    - lver=<int>       : version of the LightNVM standard to use, Default:1
    - ltype=<nvmtype>  : Whether device is block- or byte addressable, Default:0 (block)
    - lchannels=<int>    : Number of channels per namespace, Default: 4
    - lreadl2ptbl=<int>  : Load logical to physical table. 1: yes, 0: no. Default: 1
    - lbbtable=<file>    : Load bad block table from file destination (Provide path to file. If no file is provided a bad block table will be generation. Look at lbbfrequency. Default: Null (no file).
    - lbbfrequency:<int> : Bad block frequency for generating bad block table. If no frequency is provided LNVM_DEFAULT_BB_FREQ will be used.

The list of LightNVM parameters in QEMU can be found in `$QUEMU_DIR/hw/block/nvme.c` under the _Advanced optional options_ comment.

# Add target on top of device

After the modified kernel is booted and the null_nvm module is
loaded, a target is ready to be initialized on top. Use the following command to
initialize a target (FTL):

    echo "a nulln0 test rrpc 0:0" > /sys/module/lnvm/parameters/configure_debug

The parameters is as following:

 1.  a -> Adds a target to a backend device
 2.  nulln0 -> Backend device (use cat /sys/module/lnvm/parameters/configure_debug to see available devices).
 3.  test -> Name of target to be exposed at /dev/test.
 4.  rrpc -> Name of the target engine. Our case rrpc.
 5.  0:0 is start channel : end channel. This is a range of how many channels of the attached open-channel SSD device that should be allocated to the target. 

After successfully registering the target. You may issue reads and writes to
/dev/test

# Common Problems

## Kernel panic on boot using NVMe

 1. Zero out your nvme backend file.

    dd if=/dev/zero of=backend_file bs=1M count=X

 2. Remember to upgrade the qemu-nvme branch as well. The linux and qemu-nvme repos follow each other.)
