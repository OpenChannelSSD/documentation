# Linux kernel support

Support for Open-Channel SSDs is available in version 4.4+ of the Linux kernel, pblk was added in 4.12, liblightnvm support was added in 4.14, and finally the 2.0 specification support was added in 4.17.

The open-channel SSD can either be accessed through lightnvm targets or liblightnvm.

# Install nvme-cli tool

nvme-cli is the tool used to administrate nvme devices. It can be installed using:

    sudo apt-get install nvme-cli

or installed from the [https://github.com/linux-nvme/nvme-cli](https://github.com/linux-nvme/nvme-cli) repository.

If you are not running Ubuntu, please see the nvme-cli github project for instructions.

# Getting started with nvme-cli

nvme-cli implements support for enumerating open-channel SSDs, and performing target management.

Devices can be enumerated by:

    sudo nvme lnvm list

which for a single drive outputs the following:

    Number of devices: 1
    Device      	Block manager	Version
    nvme0n1     	gennvm      	(0,1,0)

If block manager reports none (only pre-4.8 kernels), the device should be first initialized using:

    sudo nvme lnvm init -d nvme0n1

Initialization of target instances is done through:

    sudo nvme lnvm create -d {blkdevname} --lun-begin={} --lun-end={} -n {targetname} -t {target engine}

 * blkdevname is the block name. E.g., nvme0n1
 * --lun-begin & --lun-end defines the range of parallel units to use. E.g., 0 and 3 to use 4 parallel units for initialization.
 * targetname. Block device name to expose. E.g., mydev exposes the drive as /dev/mydev
 * targetengine. Module to load. e.g., pblk

It is not necessary to initialize anything for being able to use liblightnvm.

Removal of target instance:

    sudo nvme lnvm remove -n {targetname}

 * targetname. E.g., mydev

for other options, use --help on each command. For example

    sudo nvme lnvm create --help

# Getting started with pblk

pblk is a lightnvm target that implements a host-side translation layer, that enables an open-channel SSD to be exposed as a traditional block device. It is available from version Linux kernel 4.12+ (4.17+ is preferred for OCSSD 2.0 support).

Initialize pblk as /dev/mydevice on nvme0n1, using parallel unit 0-3.

    sudo nvme lnvm create -d nvme0n1 --lun-begin=0 --lun-end=3 -n mydevice -t pblk

use the option -f to skip recoverying of mapping table. This removes all data on drive.

    sudo nvme lnvm create -d nvme0n1 --lun-begin=0 --lun-end=3 -n mydevice -t pblk -f

More information at [http://lightnvm.io/pblk-tools/](http://lightnvm.io/pblk-tools/)
