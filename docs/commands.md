LightNVM can be interfaced through the lightnvm cli tool (lnvm). It allows you to create,
delete, and manage LightNVM targets and devices. 

lnvm can easily be installed on Ubuntu by adding the LightNVM PPA repository and install its package:

    sudo add-apt-repository ppa:lightnvm/ppa; sudo apt-get update
    sudo apt-get install lnvm

If you are not running Ubuntu or wish to compile from source. It is also available on Github (https://github.com/OpenChannelSSD/lnvm)

	git clone https://github.com/OpenChannelSSD/lnvm.git
	cd lnvm
	make; sudo make install

After lnvm has neen installed, its commands can be listed with:

	lnvm --help

To list devices and targets
Supported devices register with LightNVM upon initialization. To list registered
devices:

    lnvm devices

To list Targets and associated versions:

    lnvm info

To add a target on top of a device registered with the gennvm media manager:

    lnvm create -d $DEVICE -n $TARGET_NAME -t $TARGET_TYPE -o
    $LUN_BEGIN:$LUN_END

1. $DEVICE: Backend device. lnvm devices to list available devices.
2. $TARGET_NAME: Name of the target to be exposed -> /dev/$TARGET_NAME
3. $TARGET_TYPE: Target type. Targets need to be compiled individually before they
can be instantiated at run-time. For now, rrpc is the only available target.
4. $LUN_BEGIN: Lower bound of the LUN range allocated to the target.
5. $LUN_END: Higher bound of the LUN range allocated to the target.

After successfully registering the target. You may issue reads and writes to
/dev/$TARGET_NAME

For example, to allocate LUN 0 to target tests using rrpc in the NVMe device
nvme0n1:

    lnvm create -d nvme0n1 -n mydevice -t rrpc -o 0:0

A target instance are removed again by:

    lnvm remove mydevice
