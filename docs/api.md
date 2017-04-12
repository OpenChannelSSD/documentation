# Linux kernel

The LightNVM API is defined in /include/linux/lightnvm.h
The user-space API is defined in /include/uapi/linux/lightnvm.h

Each of the exported symbols are described in /drivers/lightnvm/core.c

This includes documentation on the following functions

    Target specific
      nvm_register_target - Register a target with the LightNVM subsystem
      nvm_unregister_target - Unregister a target with the LightNVM subsystem
      nvm_dev_dma_alloc - Allocate device DMA-able memory
      nvm_dev_dma_free - Frees device DMA-able memory
      nvm_addr_to_generic_mode - Convert from device-specific to generic addressing
      nvm_generic_to_addr_mode - Convert from generic to device-specific addressing
      nvm_set_rqd_ppalist - Sets nvm_rqd with associated ppa list

    Media manager specific
      nvm_register_mgr - Register media manager with LightNVM subsystem
      nvm_unregister_mgr - Unregister a media manager from the LightNVM subsystem
      nvm_get_blk_unlocked - Get free logical block from logical lun
      nvm_put_blk_unlocked - Put logical block to logical lun
      nvm_get_blk - Get free logical block from logical lun
      nvm_put_blk - Put logical block to logical lun
  
    Device specific
      nvm_register - Register a device with the LightNVM subsystem
      nvm_unregister - Unregister a device from the LightNVM subsystem
      nvm_submit_io - submit an io to the underlying media media manager
      nvm_erase_blk - submit erase of the generic nvm_block
      nvm_erase_ppa - Erase the block(s) associated with physical addresses
      nvm_submit_ppa - Submits a list of ppas to device synchronously.
      nvm_end_io - Complete a nvm_rq request

Please see the each function documentation for its parameters and description.

Examples of how to invoke the APIs is found in

    Media manager
      gennvm - /drivers/lightnvm/gennvm.c
      
    Target
      pblk - /drivers/lightnvm/pblk.c
      rrpc - /drivers/lightnvm/rrpc.c
      
    Device-side
      NVMe - /drivers/nvme/host/lightnvm.c
      null_blk - /drivers/block/null_blk.c
