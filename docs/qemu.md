# Getting started with a virtual Open-Channel SSD

To enable fast development and interface tests, one can utilize qemu to expose a virtual open-channel ssd within the virtual machine.

There is two implementations available. One for each revision of the open-channel ssd specification.

## Open-Channel SSD 2.0

Revision 2.0 implementation is available here: [https://github.com/OpenChannelSSD/qemu-nvme](https://github.com/OpenChannelSSD/qemu-nvme)

## Open-Channel SSD 1.2 (deprecated)

Revision 1.2 implementation is available here: [https://github.com/OpenChannelSSD/qemu-nvme/tree/ocssd12](https://github.com/OpenChannelSSD/qemu-nvme/tree/ocssd12)

Note that revision 1.2 is no longer being maintained.

# Use a custom kernel

The development of the lightnvm subsystem is hosted here:

   `git clone https://github.com/OpenChannelSSD/linux.git`

and the latest work is found in the for-4.XX/core branch.

Make sure that the Linux kernel .config file at least includes:

    # For NVMe support
    CONFIG_BLK_DEV_NVME=y
    # For LightNVM support
    CONFIG_NVM=y
    # Expose the /sys/module/lnvm/parameters/configure_debug interface
    CONFIG_NVM_DEBUG=y
    # Target support (required to expose the open-channel SSD as a block device)
    CONFIG_NVM_PBLK=y

Compile the kernel and install using the guide for your distribution.

# Qemu Development Environment

To speed up development, one can pass a kernel image directly to qemu to boot. Example kernel config file is provided in the qemu-nvme repository (/kernel.config). Overwrite .config in the kernel source directory and compile.

The kernel can be passed through to qemu using the following arguments.

    -append "root=/dev/vda1" \
    -kernel "{linux source directory}/arch/x86_64/boot/bzImage"

