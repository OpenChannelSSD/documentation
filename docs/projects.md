The Open-Channel SSD architecture is divided into several pieces. Here is a short description of each project and how it relates.

   - **Linux Kernel Support** ([https://github.com/OpenChannelSSD/linux](https://github.com/OpenChannelSSD/linux))
   

      Implements support for Open-Channel SSDs in the kernel. It is the core of identifying, managing, and using the Open-Channel SSDs.


   - **LightNVM Management Tool** ([https://github.com/OpenChannelSSD/lightnvm-adm](https://github.com/OpenChannelSSD/lightnvm-adm))
      
      Implements a management tool for managing the Open-Channel SSDs.


   - **LightNVM Test Tools** ([https://github.com/OpenChannelSSD/lightnvm-hw](https://github.com/OpenChannelSSD/lightnvm-hw))

      Various tools to test kernel and target implementations.


   - **QEMU with NVMe Open-Channel Support** ([https://github.com/OpenChannelSSD/qemu-nvme](https://github.com/OpenChannelSSD/qemu-nvme))

      Implements support for exposing a virtual open-channel SSD. Very useful for development.


   - **RocksDB Open-Channel SSD Support** ([https://github.com/OpenChannelSSD/rocksdb](https://github.com/OpenChannelSSD/rocksdb))

      Implements support in RocksDB for directly accessing Open-Channel SSDs.


   - **liblightnvm Library** ([https://github.com/OpenChannelSSD/liblightnvm](https://github.com/OpenChannelSSD/liblightnvm))


      A library that abstracts the underlying "raw" Open-Channel SSD device and provides abstractions such as append only, bad block management, etc.


   - **Documentation** ([https://github.com/OpenChannelSSD/documentation](https://github.com/OpenChannelSSD/documentation))

      The documentation that is exposed through readthedocs.org

Feel free to contribute to any of the projects.
