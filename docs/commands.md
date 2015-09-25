# List devices and targets

Supported devices registers itself with LightNVM upon initialization. To list registered devices, you may run the following command:

    # With lnvm (https://github.com/OpenChannelSSD/lightnvm-adm)
    lnvm devices

    # With debug interface
    cat /sys/module/lnvm/parameters/configure_debug

Targets and their versions is listed with

    lnvm info 

# Add target on top of device

Targets are initialized on top of a device. A target is the logic to expose the raw media to user-space. For example as a block device (rrpc) or directly to user-space (nba). Use the following command to
initialize a target (FTL):

    # With lnvm (https://github.com/OpenChannelSSD/lightnvm-adm)
    lnvm create -d nulln0 -n test -t rrpc
    lnvm create -d nulln0 -n test -t rrpc -o 0:0
    
    # With Debug interface
    echo "a nulln0 test rrpc 0:0" > /sys/module/lnvm/parameters/configure_debug

The parameters is as following:

 1.  a -> Adds a target to a backend device
 2.  nulln0 -> Backend device (use cat /sys/module/lnvm/parameters/configure_debug to see available devices).
 3.  test -> Name of target to be exposed at /dev/test.
 4.  rrpc -> Name of the target engine. For this case rrpc.
 5.  0:0 is start channel : end channel. This is a range of how many channels of the attached open-channel SSD device that should be allocated to the target. 

After successfully registering the target. You may issue reads and writes to
/dev/test

# Delete target
An instance of a target is deleted by:

    # With lnvm (https://github.com/OpenChannelSSD/lightnvm-adm)
    lnvm remove test
    
    # With debug interface
    echo "d test" > /sys/module/lnvm/parameters/configure_debug

deletes the configured "test" target.

# List block information on backend device

    echo "s nulln0" > /sys/module/lnvm/parameters/configure_debug

List's the internal information of a target. 
