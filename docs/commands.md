# LightNVM admin tool

LightNVM can be interfaced through lightnvm-adm, an admin tool to manage create,
delete, and manage LightNVM targets and devices. As for today, the tool is
available in Github (https://github.com/OpenChannelSSD/lightnvm-adm). To install
it:

	make; sudo make install

Packages for different Linux distros are work in progress.

To show help

	lnvm --help

# List devices and targets
Supported devices register with LightNVM upon initialization. To list registered
devices use the following command:

    lnvm devices

Targets and their versions are listed with

    lnvm info

# Add target on top of device
Targets are initialized on top of a device. Use the following command to
initialize a target (FTL):

    lnvm create -d $DEVICE -n $TARGET_NAME -t $TARGET_TYPE -o
    $LUN_BEGIN:$LUN_END

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

    lnvm create -d nvme0n1 -n test -t rrpc -o 0:0

# Delete target
An instance of a target is deleted by:

    lnvm remove test
