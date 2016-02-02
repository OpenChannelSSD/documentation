# How to use

Open-Channel SSDs require support in the kernel. LightNVM, the kernel subsystem
enabling such support is part of the vanilla Linux Kernel from 4.4+. The latest source code is available at the Open-Channel SSD project in Github (see below).

After booting the kernel. To enable LightNVM, the following must be met:

1. A compatible device driver that is registered with LightNVM. For example
null_blk, QEMU NVMe or an Open-Channel SSD, such as the CNEX Labs Westlake SDK.
2. An initialized media manager on top of the device driver. Note that LightNVM
provides a generic media manager. The media manager maintains a list of
free/in-use/bad blocks on media and exposes a get/put block API that upper
layers can use to allocate into the SSD address space.
3. An initialized target on top of the block manager that exposes the SSD address
space. The target can expose a traditional block I/O interface, or more esoteric
interfaces such as key-value stores, object-stores, etc.

## Install Kernel 4.4+

LightNVM is directly supported in Linux kernel 4.4+. If you don't have a 4.4 kernel installed, you can follow the guide at: [http://linuxdaddy.com/blog/install-kernel-4-4-on-ubuntu/](http://linuxdaddy.com/blog/install-kernel-4-4-on-ubuntu/) to install.

## Install lnvm tool

lnvm can easily be installed on Ubuntu by adding the LightNVM PPA repository and install its package:

    sudo add-apt-repository ppa:lightnvm/ppa; sudo apt-get update
    sudo apt-get install lnvm
    
If you are not running Ubuntu, please see the Command Line section for how to install from source.

## Instantiate with the null_blk driver

Instantiate the module with the following parameters

`use_lightnvm=1 gb=4`

For example:

`modprobe null_blk use_lightnvm=1 gb=4`

or append to your kernel parameters on booting:

`null_blk.use_lightnvm=1 null_blk.gb=4`

That will instantiate the LightNVM driver with a 4GB SSD. You can see that it
was instantiated by checking the kernel log.

`dmesg |grep nvm`

where the output should be similar to

    [    2.120740] nvm: registered nulln0 with luns: 1 blocks: 2048 sector size: 4096

## Instantiate with NVMe using QEMU

Using Keith Busch's QEMU branch, it is possible to expose a LightNVM-compatible device using a backend-file. See the guide below for installing his version.

### Configure QEMU

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

QEMU support the following LightNVM-specific parameters:

    - lver=<int>       : version of the LightNVM standard to use, Default:1
    - lreadl2ptbl=<int>  : Load logical to physical table. 1: yes, 0: no. Default: 1
    - lbbtable=<file>    : Load bad block table from file destination (Provide path to file. If no file is provided a bad block table will be generation. Look at lbbfrequency. Default: Null (no file).
    - lbbfrequency:<int> : Bad block frequency for generating bad block table. If no frequency is provided LNVM_DEFAULT_BB_FREQ will be used.

The list of LightNVM parameters in QEMU can be found in `$QUEMU_DIR/hw/block/nvme.c` at the _Advanced optional options_ comment.

## Instantiate media manager and target

When the installation is finished and the kernel have been booted. Devices can be enumerated by:

    sudo lnvm devices
   
and initialized by:

    sudo lnvm create -d nullb0 -t mydevice
    
Assuming nullb0 was shown during "lnvm devices", it will then expose /dev/mydevice as a block device using it as the backend. 

That's it! 

## Source install

### Compile latest kernel

The latest LightNVM can be found at:

   `git clone https://github.com/OpenChannelSSD/linux.git`

in the "for-next" branch.

Make sure that the .config file at least includes:

    CONFIG_NVM=y
    # Expose the /sys/module/lnvm/parameters/configure_debug interface
    CONFIG_NVM_DEBUG=y
    # Hybrid target support (required to expose the open-channel SSD as a block device)
    CONFIG_NVM_RRPC=y
    # generic media manager support (required)
    CONFIG_NVM_GENNVM=y
    # For null_blk support
    CONFIG_BLK_DEV_NULL=y
    # For NVMe support
    CONFIG_BLK_DEV_NVME=y

Compile the kernel and install using the guide for your distribution.

### QEMU Installation

QEMU support for Open-Channel SSDs is based on top of Keith Busch's qemu-nvme
branch, which implements an NVMe compatible device.

Clone the qemu source from

    git clone https://github.com/OpenChannelSSD/qemu-nvme.git

and configure the QEMU source with

    ./configure --enable-linux-aio --target-list=x86_64-softmmu --enable-kvm

then install by
	make and install

# Common Problems

## Failed to open LightNVM mgmt /dev/lightnvm/control. Error: -1

Either you need to run lnvm as root, or you are running an older kernel than 4.4.

## Kernel panic on boot using NVMe

 1. Zero out your nvme backend file.

    dd if=/dev/zero of=backend_file bs=1M count=X

 2. Remember to upgrade the qemu-nvme branch as well. The linux and qemu-nvme
 repos follow each other.
