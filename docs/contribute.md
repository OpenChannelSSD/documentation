Want to hack on LightNVM? Awesome!

# Filling Issues #

If you have a question about LightNVM and its tools. Feel free to make an issue Github.

 * Linux kernel (https://github.com/OpenChannelSSD/linux)
 * liblightnvm (https://github.com/OpenChannelSSD/liblightnvm)
 * lnvm-tools (https://github.com/OpenChannelSSD/lnvm-tools)
 * Documentation (https://github.com/OpenChannelSSD/documentation)

# How to become a contributer #

If you find implemented a nice feature, found a bug and fixed, and now want contribute it. Please submit a pull request through Github. 

If you want to submit a Linux kernel patch, please follow the Linux kernel guide to submit patches (https://www.kernel.org/doc/html/latest/process/howto.html). 

When submitting a patch for the Linux kernel, CC the following:

 * Maintainer: mb@lightnvm.io
 * Linux block layer list: linux-block@vger.kernel.org
 * Linux kernel mailing list: linux-kernel@vger.kernel.org

# How do I introduce a new NVMe SSD that supports the Open-Channel SSD interface? #

Please update the "nvme_nvm_ns_supported" function in the the /drivers/nvme/host/lightnvm.c file with the appropriate PCI vendor, device id, and detection method. After the patch have been made, feel free to submit it to the Linux kernel.
