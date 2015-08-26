# See registered device

Devices that registers as a LightNVM device, is not exposed as a block device
right away. This requires that a target is configured. To see if a device if
registered, you may run the following command:

    cat /sys/module/lnvm/parameters/configure_debug

# Add target

After the modified kernel is booted and devices have been reigstered with
LightNVM, a target is ready to be initialized on top. Use the following command o
initialize a target (FTL):

    echo "a nulln0 test rrpc 0:0" > /sys/module/lnvm/parameters/configure_debug

The parameters is as following:

 1.  a -> Adds a target to a backend device
 2.  nulln0 -> Backend device (use cat /sys/module/lnvm/parameters/configure_debug to see available devices).
 3.  test -> Name of target to be exposed at /dev/test.
 4.  rrpc -> Name of the target engine. Our case rrpc.
 5.  0:0 is start channel : end channel. This is a range of how many channels of the attached open-channel SSD device that should be allocated to the target. 

After successfully registering the target. You may issue reads and writes to
/dev/test

# Delete target

    echo "d test" > /sys/module/lnvm/parameters/configure_debug

deletes the configured "test" target.

# List block information on backend device

    echo "s nulln0" > /sys/module/lnvm/parameters/configure_debug

List's the internal information of a target. 


