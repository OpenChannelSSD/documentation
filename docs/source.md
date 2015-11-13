# Linux Kernel

## Branches

 - master - Latest kernel master
 - for-X.X/feature -> X.X is the kernel version and feature is the work contained in the branch. These are merged into for-next
 - for-next - Contains all for-X.X/feature patches for next kernel cycle
 - for-jens -> patches ready to be picked up for upstream

## Code structure

The LightNVM framework is divided into support for device drivers, a core part
that consists of management, block managers and targets.

Current the NVMe device driver in

    drivers/nvme/host/pci.c

has been extended with support for LightNVM devices through

    drivers/nvme/host/lightnvm.c

The source code for the LightNVM core is placed within

    drivers/lightnvm/core.c
    include/linux/lightnvm.h

with the generic media manager implemented at:

    drivers/lightnvm/gennvm.[ch]

and rrpc target available at:

    drivers/lightnvm/rrpc.[ch]

See the individual files for how to implement new targets and media managers.
