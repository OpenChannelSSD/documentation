# Open-Channel Solid State Drives
Open-Channel SSDs are devices that share responsibilities with the host in order
to implement and maintain features that typical SSDs keep strictly in firmware.
These include (i) the Flash Translation Layer (FTL), (ii) bad block management,
and (iii) dedicated hardware units such as the flash controller, the interface
controller, and large amounts of flash chips. The motivation behind moving
certain responsibilities to the host is to make I/O data commands predictable
from the host-side. In this way, the device enables the host to adapt the FTL
algorithms and optimizations to match the appropriate user workloads that it
executes.

An example of a shared responsibility configuration is the following: The host
manages data placement, garbage collection, and has knowledge about the parallel
units within the device to schedule and manage I/O streams. The device manages
bad blocks and maintains the mapping tables. The device implements hardware
extensions to support atomic IOs and metadata persistence too. These are exposed
to the host through a common interface. 

The baseline is that Open-channels SSD expose direct access to their physical
flash storage, while keeping a subset of the internal features of traditional
SSDs. The responsibilities that are delegated to the host depend entirely on the
device capabilities.

![Figure 1](img/LightNVMArch.pdf)


The Linux host architecture for Open-channel SSDs is depicted in Figure 1.
It consists of four fundamental components: LightNVM compatible device driver,
framework manager, media manager, and targets.

- LightNVM device driver. The device driver is responsible for implementing the
  storage protocol use for communication between host and SSD. A device driver
  instance is created for each connected Open-Channel SSD. It relies on the
  Linux block manager to handle command submission/completion, timeouts, and
  tags. Currently, LightNVM uses NVMe, which is extended with (i) identify
  device structures, used by the host to discover the available features,
  extensions, and characteristics of the device; and (ii) and a series of
  custom commands, which enables the host to issue I/O commands that act
  directly on the physical media.

- Framework Manager. The framework management acts as a mediator between device
  drivers, media managers and targets. It provides supporting functionalities
  for device initialization, teardown or accounting.

- Media Manager. The media manager abstracts the underlying physical media by
  hiding its constraints and access details. It is responsible for (i) name
  mapping between vendor specific and generic addressing format, (ii)
  device-specific SSD state management, and (iii) recovery – to guarantee
  durability when manipulating the metadata associated with SSD state
  management. Each media manager implementation can vary much depending on the
  needs of upper layer it is exposed to. LightNVM’s generic media manager
  implements minimal block management and let targets or user space applications
  implement the actual FTL. Still, it is possible for a media managers to
  implement the whole FTL and manage user space interfaces. For example, a media
  manager could expose a block device similar to a traditional block storage
  device. Such a media manager would manage functionalities such as data
  placement, garbage collection and block management.

- LightNVM Targets. When using the generic media manager, targets implement FTL
  functionality (e.g., translation logic, data placement, or garbage
  collection). They also expose a storage interface to user space. Examples of
  such interfaces include block devices, and key-value stores, or object stores.
  Custom interfaces can also be implemented. Target and device (media manager)
  division, grouping and assignments are flexible in such a way that one device
  can be split and assigned into multiple targets or multiple devices can belong
  to one target.

Additionally, LightNVM connects to user space through liblightnvm, which exposes
a get_block/put_block interface for applications to implement their own FTLs.

