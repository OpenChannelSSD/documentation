# Open-Channel Solid State Drives
An Open-Channel SSD is a device that shares responsibilities with the host to implement and maintain functionalities that a traditional SSD keeps strictly in firmware. 

By moving traditional SSD responsibilities to the host, I/Os latencies can be significantly more predictable. The added responsibilities allows host software to adapt its data placement, garbage collection and I/O scheduling algorithms to a given user I/O workload. Traditional, these optimizations are applied on the device side of the storage interface and hidden behind a narrow storage interface, which prevents significant optimizations and algorithms to change during run-time.

The responsibilities are shared such that the host manages data placement, garbage collection, and I/O scheduling. While the device manages bad blocks, durability, and implement efficient hardware engies for writing to the non-volatile memories it has attached. 

![Figure 1](img/LightNVMArch.png)


Figure 1 depicts the Linux host architecture for Open-channel SSDs, also known as LightNVM. It consists of three fundamental components: LightNVM compatible device drivers, media managers, and targets.

## Device Drivers
A storage device driver implements support for the Open-Channel SSD Physical Page Address command set. The command set includes (i) identify structure, used by the host to discover available features, extensions, and characteristics of an open-channel device; and (ii) and physical data commands to communicate efficiently with the storage non-volatile media.

## Media Managers
The media manager abstracts the underlying physical media by hiding its constraints and access details. It is responsible for (i) name mapping between vendor specific and generic addressing format, (ii) device-specific SSD state management (if not implemented at the device-side), and (iii) recovery – to guarantee durability when manipulating the metadata associated with SSD state management. A media manager acts as a mediator between physical media, and users, abstracting away media complexities. The LightNVM general media manager implements minimal block management and lets targets or user space applications perform the actual FTL. It is possible for a media manager to implement the whole FTL and manage user-space interfaces. For example, a media manager could expose a block device similar to a traditional block storage device. Such a media manager would manage functionalities such as data placement, garbage collection, and block management.


## Targets
When using the general media manager, targets implement FTL functionality (e.g., translation logic, data placement, or garbage collection). They also expose a storage interface to user space. Examples of such interfaces include block devices, and key-value stores, or object stores. Target and device (media manager) division, grouping and assignments are flexible in such a way that one device can be split and assigned to multiple targets or multiple devices can belong to one target.
