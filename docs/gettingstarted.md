# How to use

Open-Channel SSDs require support in the kernel. LightNVM, the kernel subsystem
enabling such support is part of the vanilla Linux Kernel from 4.4+. The latests
source code can be found in the Open-Channel SSD project in Github (see below).

After booting the kernel. To enable LightNVM, the following must be met:

1. A compatible device driver that is registered with LightNVM. For example
null_blk, QEMU NVMe or an actual Open-Channel SSD compatible hardware.
2. An initialized media manager on top of the device driver. Note that LightNVM
provides a generic media manager. The media manager maintains a list of
free/in-use/bad blocks on media and exposes a get/put block API that upper
layers can use to allocate into the SSD address space.
3. An initialized target on top of the block manager that exposes the SSD address
space. The target can expose a traditional block I/O interface, or more esoteric
interfaces such as key-value stores, object-stores, etc.

# Compile Latest Kernel

Download latest Linux kernel (4.4+) or check out the latest Linux kernel at

   `git clone https://github.com/OpenChannelSSD/linux.git`

Make sure that the .config file at least includes:

    CONFIG_NVM=y
    # Expose the /sys/module/lnvm/parameters/configure_debug interface
    CONFIG_NVM_DEBUG=y
    # Hybrid target support (required to expose a block device)
    CONFIG_NVM_RRPC=y
    # Hybrid block manager support (required)
    CONFIG_NVM_GENNVM=y
    # For null_blk support
    CONFIG_BLK_DEV_NULL=y
    # For NVMe support
    CONFIG_BLK_DEV_NVME=y

Compile the kernel and install using the guide for your distribution.

# Compile management interface (lnvm)

Check out the management tool source at:

   `git clone https://github.com/OpenChannelSSD/lightnvm-adm.git`

The tool can then be installed (into /usr/local/bin) by

    make; sudo make install

After which it can be used to manage LightNVM devices.

# Initialize using null_blk driver

Instantiate the module with the following parameters

`use_lightnvm=1 gb=4`

That will instantiate the LightNVM driver with a 4GB SSD. You can see that it
was instantiated by checking the kernel log.

`dmesg |grep nvm`

where the output should be similar to

    [    2.120740] nvm: registered nulln0 with luns: 1 blocks: 2048 sector size: 4096
    [    2.325021] nvm: registered nvme0n1 with luns: 1 blocks: 2044 sector size: 4096

# Instantiate NVMe driver using QEMU

If you count on a LightNVM-compatible device, simply plug it in and it should
be found. If you don't, then you can use the LightNVM-enabled QEMU branch to
prototype with (see next section).

## QEMU Installation

QEMU support for Open-Channel SSDs is based on top of Keith Busch's qemu-nvme
branch, which implements an NVMe compatible device.

Clone the qemu source from

    git clone https://github.com/OpenChannelSSD/qemu-nvme.git

and configure the QEMU source with

    ./configure --enable-linux-aio --target-list=x86_64-softmmu --enable-kvm

then install by
	make and install

## Configure QEMU

Create an empty file to hold your NVMe device.

    dd if=/dev/zero of=blknvme bs=1M count=1024

this creates a zeroed 1GB file called "blknvme". You can boot your favorite
Linux image with

    qemu-system-x86_64 -m 4G -smp 1,cores=4 --enable-kvm
    -hda $LINUXVMFILE -append "root=/dev/sda1"
    -kernel "/home/foobar/git/linux/arch/x86_64/boot/bzImage"
    -drive file=blknvme,if=none,id=mynvme
    -device nvme,drive=mynvme,serial=deadbeef,namespaces=1,lver=1,nlbaf=5,lba_index=3,mdts=10

Here, replace $LINUXVMFILE with your pre-installed Linux virtual machine.
LightNVM is enabled with lver=1. The number of LightNVM channels is set to one,
and the last part defines the page size to be 4K.

QEMU support the following LightNVM-specific parameters:

    - lver=<int>       : version of the LightNVM standard to use, Default:1
    - lreadl2ptbl=<int>  : Load logical to physical table. 1: yes, 0: no. Default: 1
    - lbbtable=<file>    : Load bad block table from file destination (Provide path to file. If no file is provided a bad block table will be generation. Look at lbbfrequency. Default: Null (no file).
    - lbbfrequency:<int> : Bad block frequency for generating bad block table. If no frequency is provided LNVM_DEFAULT_BB_FREQ will be used.

The list of LightNVM parameters in QEMU can be found in `$QUEMU_DIR/hw/block/nvme.c` under the _Advanced optional options_ comment.

# Add target on top of device

After the modified kernel is booted and the Open-Channel compatible backend is
loaded, a target is ready to be initialized on top. Use the following command to
initialize a target (FTL).

    echo "a $DEVICE $TARGET_NAME $TARGET_TYPE %LUN_BEGIN:%LUN_END" > /sys/module/lnvm/parameters/configure_debug

1. $DEVICE: Backend device. Use cat /sys/module/lnvm/parameter/configure_debug to
see the available devices.
2. $TARGET_NAME: Name of the target to be exposed -> /dev/$TARGET_NAME
3. $TARGET_TYPE: Target type. Targets need to be compiled individually before they
can be instantiated at run-time. For now, rrpc is the only available target.
4. $LUN_BEGIN: Lower bound of the LUN range allocated to the target.
5. $LUN_END: Higher bound of the LUN range allocated to the target.

After successfully registering the target. You may issue reads and writes to
/dev/$TARGET_NAME

For example, to allocate LUN 0 to target tests using rrpc in the NVMe device
nvme0n1:

	echo "a nvme0n1 test rrpc 0:0 > /sys/module/lnvm/parameters/configure_debug

Note that "a" corresponds to the command "add".

For a complete list of available commands and their outputs,
see User Documentation > Commands. For a description of LightNVM's debug
interface see User Documentation > Debug Interface.

# Common Problems

## Kernel panic on boot using NVMe

 1. Zero out your nvme backend file.

    dd if=/dev/zero of=backend_file bs=1M count=X

 2. Remember to upgrade the qemu-nvme branch as well. The linux and qemu-nvme
 repos follow each other.)
