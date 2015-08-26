# Code structure

The LightNVM framework is divided into support for device drivers, a core part
that consists of management, block managers and targets.

Current the NVMe device driver in

  drivers/block/nvme-core.c

has been extended with support for LightNVM devices through

  drivers/block/nvme-lightnvm.c

The source code for the core is placed within

  drivers/lightnvm/core.c
  include/linux/lightnvm.h

with the block managers implemented with the bm\_ prefix

  drivers/lightnvm/bm_hb.[ch]

and targets with the tgt\_ prefix:

  drivers/lightnvm/tgt_rrpc.[ch]

See the individual files for how to implement new targets and block managers.



