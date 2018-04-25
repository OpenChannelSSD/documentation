# Open-Channel SSDs

To use an open-channel SSD, the host storage stack must have support for the open-channel ssd specification. Support is available through:

 * Linux kernel (4.12+)
 * nvme-cli (0.8+ )
 * liblightnvm (Linux kernel 4.14+)

The linux kernel implements the 1.2 (since 4.12) and 2.0 (since 4.17) of the Open-Channel SSD specification. When an open-channel SSD is found by the NVMe subsystem, it registers with the "lightnvm" subsystem.

The kernel implements sysfs entries to enumerate the geometry of the SSD through /dev/block/nvme*/lightnvm/. One can easily access this after the kernel has booted.

After the system bringup, and an open-channel SSD has been detected, one can either initialize a lightnvm target on top, interact directly using traditional block interface (with zoned block device support), or accessing the drive through nvme-cli, or liblightnvm.

The initialization of lightnvm targets is done through nvme-cli, which implements target management.

If no hardware is availble is available, one can use the open-channel SSD enabled qemu that has support for either 1.2 or 2.0 of specification. 
