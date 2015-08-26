# Open-Channel Solid State Drives

An Open-Channel Solid State Drive is an SSD that exposes the physical flash storage through the logical address space, while keeping a subset of the internal features of common SSDs. 

A common SSD consists of a flash translation layer (FTL), bad block management, and dedicated hardware units such as a flash controller and a host interface controller, bundled together with a large amount of flash chips.

Open-channel SSDs moves part or all of the FTL responsibility into the host system. This allows the host to manage data placement, garbage collection, and parallelism on behalf of the SSD. To not move everything into the host, the device may continue to maintain information about bad blocks and implement a simpler FTL that allows extensions such as atomic IOs, metadata persistence and other features to be accelerated.

The Linux host architecture for Open-channel SSDs, depicted in Figure 1, consists of device driver integration,  multiple block managers and multiple targets and a core. To initialize an open-channel SSD, a storage protocol, such as NVMe, is extended with identify structures and custom command set. That allows the host to enumerate the available features, extensions and hardware of the device and issue I/Os directly to the physical media.

![Figure 1](img/LightNVMArch.png)


The block manager handles flash block information on disk. A block manager is different for which types of features and extensions that an SSD supports. For example, a host-based open-channel SSD requires maintaining a metadata file-system, while a hybrid SSD, which may implement block management, lets the block manager read the block management information from disk.

Targets implement the translation logic, data placement, garbage collection routines and so forth. The target may then choose to export the address space is various ways. Such as a block device, key-value store, or object store. By dividing the responsibility between block management, and data placement and garbage collection. The target is decoupled from the underlying media and thereby allows multiple vendors to provide hardware and allows the target to cover multiple disk and create a single address space across all media available. 

The core acts as a mediator between device drivers, block managers and targets. The core implements functionality shared across targets, such as initialization, teardown and performance counters.
