# LightNVM debug interface

When the Linux Kernel is compiled with NVM_DEBUG, LightNVM's debug interface is
available through /sys/module/lnvm/parameters/configure_debug.

# List devices and targets

Supported devices register with LightNVM upon initialization. To list registered
devices use the following command:

    cat /sys/module/lnvm/parameters/configure_debug

# Add target on top of device

Targets are initialized on top of a device. Use the following command to
initialize a target (FTL):

    echo "a $DEVICE $TARGET_NAME $TARGET_TYPE %LUN_BEGIN:%LUN_END" > /sys/module/lnvm/parameters/configure_debug

$DEVICE: Backend device. Use cat /sys/module/lnvm/parameter/configure_debug to
see the available devices.
$TARGET_NAME: Name of the target to be exposed -> /dev/$TARGET_NAME
$TARGET_TYPE: Target type. Targets need to be compiled individually before they
can be instantiated at run-time. For now, rrpc is the only available target.
$LUN_BEGIN: Lower bound of the LUN range allocated to the target.
$LUN_END: Higher bound of the LUN range allocated to the target.

After successfully registering the target. You may issue reads and writes to
/dev/$TARGET_NAME

For example, to allocate LUN 0 to target tests using rrpc in the NVMe device
nvme0n1:

	echo "a nvme0n1 test rrpc 0:0 > /sys/module/lnvm/parameters/configure_debug

# Delete target
An instance of a target is deleted by:

    echo "d test" > /sys/module/lnvm/parameters/configure_debug

# List block information on backend device

To list the internal information of a target:

    echo "s nulln0" > /sys/module/lnvm/parameters/configure_debug

The output should be interpreted as:


$DEVICE_NAME: lun: $LUN_NUMBER free_blocks/inuse_blocks/bad_blocks.

