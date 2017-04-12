LightNVM can be interfaced through the nvme cli tool (nvme-cli). It allows you to create,
delete, and manage LightNVM targets and devices. 

lnvm can easily be installed on Ubuntu by installing the nvme-cli package

    sudo apt-get install nvme

If you are not running Ubuntu or wish to compile from source. It is also available at Github (https://github.com/linux-nvme/nvme-cli)

After nvme cli has been installed, its commands can be listed with:

    nvme lnvm --help

To list devices and targets
Supported devices register with LightNVM upon initialization. To list registered
devices:

    nvme lnvm list

To list available targets and their version:

    nvme list info
    
A device must first have a media registered when it is first used (pre-4.11)

    nvme lnvm init -d $DEVICE

To add a target on top of a device registered with the gennvm media manager:

    nvme lnvm create -d $DEVICE -n $TARGET_NAME -t $TARGET_TYPE -b $LUN_BEGIN -e $LUN_END

1. $DEVICE: Backend device. lnvm devices to list available devices.
2. $TARGET_NAME: Name of the target to be exposed -> /dev/$TARGET_NAME
3. $TARGET_TYPE: Target type. Targets need to be compiled individually before they
can be instantiated at run-time. For now, rrpc and pblk are the available implementations.
4. $LUN_BEGIN: Lower bound of the LUN range allocated to the target.
5. $LUN_END: Higher bound of the LUN range allocated to the target.

After successfully registering the target. You may issue reads and writes to
/dev/$TARGET_NAME

For example, to allocate LUNs 0 to 63 to a pblk instance using the NVMe device
nvme0n1:

    nvme lnvm create -d nvme0n1 -n mydevice -t pblk -b 0 -e 63

A target instance are removed again by:

    nvme lnvm remove targetname
