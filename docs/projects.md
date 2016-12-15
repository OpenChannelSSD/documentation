The Open-Channel SSD architecture is divided into several pieces. Here is a short description of each project and how it relates.

   - **Linux Kernel Support**
     ([https://github.com/OpenChannelSSD/linux](https://github.com/OpenChannelSSD/linux)) <br>
      Implements support for Open-Channel SSDs in the kernel. It is the core of identifying, managing, and using the Open-Channel SSDs.
      
   - **liblightnvm Library**
     ([https://github.com/OpenChannelSSD/liblightnvm](https://github.com/OpenChannelSSD/liblightnvm))<br>
      A library that abstracts the underlying "raw" Open-Channel SSD device and provides abstractions such as append only, bad block management, etc.
   
   - **General documentation**
     ([https://github.com/OpenChannelSSD/documentation](https://github.com/OpenChannelSSD/documentation))<br>
      The documentation that is exposed through readthedocs.org
      
   - **liblightnvm documentation**
     ([http://lightnvm.io/liblightnvm](http://lightnvm.io/liblightnvm))<br>
      The liblightnvm documentation
      
   - **LightNVM Conditioning Tool**
     ([https://github.com/OpenChannelSSD/lnvm](https://github.com/OpenChannelSSD/lnvm-tools))
     <br>
      Provides the `lnvm-tool` cli management tool for verifying and conditioning Open-Channel SSDs.

   - **LightNVM Test Tools**
     ([https://github.com/OpenChannelSSD/lightnvm-hw](https://github.com/OpenChannelSSD/lightnvm-hw))<br>
      Various tools to test kernel and target implementations.

   - **QEMU with NVMe Open-Channel Support**
     ([https://github.com/OpenChannelSSD/qemu-nvme](https://github.com/OpenChannelSSD/qemu-nvme))<br>
      Implements support for exposing a virtual open-channel SSD. Very useful for development.


   
Feel free to contribute to the projects.
