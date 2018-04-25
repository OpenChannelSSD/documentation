# Getting started with a virtual Open-Channel SSD

Using Keith Busch's QEMU branch, it is possible to expose a LightNVM-compatible device using a backend-file.

#### Configure QEMU

Create a 8GB file to hold your NVMe backend device.

    dd if=/dev/zero of=blknvme bs=1M count=8196

this creates a zeroed 8GB file called "blknvme". You can boot your favorite
Linux image with

    qemu-system-x86_64 -m 4G -smp 1 --enable-kvm
    -hda $LINUXVMFILE -append "root=/dev/sda1"
    -kernel "/home/foobar/git/linux/arch/x86_64/boot/bzImage"
    -drive file=blknvme,if=none,id=mynvme
    -device nvme,drive=mynvme,serial=deadbeef,namespaces=1,lver=1,nlbaf=5,lba_index=3,mdts=10,lnum_lun=4,lnum_pln=1,lsecs_per_pg=4

Here, replace $LINUXVMFILE with your pre-installed Linux virtual machine.

QEMU support the following LightNVM-specific parameters:

    - lver=<int>       : version of the LightNVM standard to use, Default:1
    - lbbtable=<file>  : Load bad block table from file destination (Provide path to file. If no file is provided a bad block table will be generation. Look at lbbfrequency. Default: Null (no file).
    - lnum_lun=<X>     : Number of LUNs to expose. Minimum 2 if using pblk.
    - lnum_pln=<X>     : Number of planes to expose. 
    - lsecs_per_pg=<X> : Number of sectors in a page. Minimum 5 if using pblk.

The list of LightNVM parameters in QEMU can be found in `$QUEMU_DIR/hw/block/nvme.c` at the _Advanced optional options_ comment.

Inside the virtual machine, you can now see the drive as it was a traditional Open-Channel SSD.

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


