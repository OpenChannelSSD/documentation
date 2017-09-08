# How to use

To use an Open-Channel SSD, support in the operating system kernel is required. The Linux kernel has support since the LightNVM subsystem was introduced in version 4.4. As the project is under development, it is preferred to use the latest available kernel. You'll need the the following to get going:

 * Linux kernel 4.12+
 * Latest nvme-cli
 * qemu with Open-Channel support (optional)

## Install Linux Kernel 4.12+

Pblk, which is used in this guide, is available from version 4.12+. Make sure to install and boot the kernel before continuing.

## Install nvme-cli tool

nvme-cli is the tool used to administrate nvme devices. It can be installed using

    sudo apt-get install nvme-cli
    
or installed from [https://github.com/linux-nvme/nvme-cli](https://github.com/linux-nvme/nvme-cli)
    
If you are not running Ubuntu, please see the nvme-cli github project for instructions.

## Device options

### Open-Channel SSD hardware

If you have a LightNVM SDK from CNEX Labs, or another Open-Channel SSD, you should be able to see the device using

    sudo nvme lnvm list
    
which should output the following:

    nvme lnvm list
    Number of devices: 1
    Device      	Block manager	Version
    nvme0n1     	gennvm      	(0,1,0)
    
If block manager reports none (only pre-4.8 kernels), the device should be first initialized using

    sudo nvme lnvm init -d nvme0n1
    
### QEMU

Using Keith Busch's QEMU branch, it is possible to expose a LightNVM-compatible device using a backend-file. See the guide below for installing his version.

#### Configure QEMU

Create an empty file to hold your NVMe device.

    dd if=/dev/zero of=blknvme bs=1M count=8196

this creates a zeroed 8GB file called "blknvme". You can boot your favorite
Linux image with

    qemu-system-x86_64 -m 4G -smp 1 --enable-kvm
    -hda $LINUXVMFILE -append "root=/dev/sda1"
    -kernel "/home/foobar/git/linux/arch/x86_64/boot/bzImage"
    -drive file=blknvme,if=none,id=mynvme
    -device nvme,drive=mynvme,serial=deadbeef,namespaces=1,lver=1,nlbaf=5,lba_index=3,mdts=10,lnum_lun=4

Here, replace $LINUXVMFILE with your pre-installed Linux virtual machine.

QEMU support the following LightNVM-specific parameters:

    - lver=<int>       : version of the LightNVM standard to use, Default:1
    - lbbtable=<file>  : Load bad block table from file destination (Provide path to file. If no file is provided a bad block table will be generation. Look at lbbfrequency. Default: Null (no file).
    - lnum_lun=<X>     : Number of LUNs to expose. Minimum 2 for if using pblk.

The list of LightNVM parameters in QEMU can be found in `$QUEMU_DIR/hw/block/nvme.c` at the _Advanced optional options_ comment.

Inside the virtual machine, you can now see the drive as it was a traditional Open-Channel SSD.

## Instantiate pblk

When the installation is finished and the kernel have been booted. Devices can be enumerated by:

    sudo nvme lnvm list
   
and initialized by:

    sudo nvme lnvm create -d nvme0n1 --lun-begin=0 --lun-end=3 -n mydevice -t pblk

use the option -f to avoid recovering the L2P table from the device for quick initialization

    sudo nvme lnvm create -d nvme0n1 --lun-begin=0 --lun-end=3 -n mydevice -t pblk -f

for other options for --help on each command. For example
    sudo nvme lnvm create --help
    
Assuming nvme0n1 was shown during "nvme lnvm list", it will then expose /dev/mydevice as a block device using it as the backend. Please note that pblk is only available at the Linux kernel Github repository, and it yet to be upstream.

## Source install

### Compile latest kernel

The latest LightNVM kernel can be found at:

   `git clone https://github.com/OpenChannelSSD/linux.git`

in the "for-next" branch.

Make sure that the .config file at least includes:

    CONFIG_NVM=y
    # Expose the /sys/module/lnvm/parameters/configure_debug interface
    CONFIG_NVM_DEBUG=y
    # Target support (required to expose the open-channel SSD as a block device)
    CONFIG_NVM_PBLK=y    
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

Either you need to run nvme as root, or you are running an older kernel than 4.4.

## Kernel panic on boot using NVMe

 1. Zero out your nvme backend file.

    dd if=/dev/zero of=backend_file bs=1M count=X

 2. Remember to upgrade the qemu-nvme branch as well. The linux and qemu-nvme
 repos follow each other.
