The key distinction is between **Block Storage** and **File Storage** (or Network Filesystems).

**Storage device** is the physical hardware (SSD, HDD, USB drive) - the actual thing you can touch. **Block device** is the logical interface the OS uses to access that storage, represented as files like `/dev/sda` or `/dev/nvme0n1`. The OS reads and writes data in fixed-size blocks (typically 512 bytes or 4KB) rather than byte-by-byte.

**Key point:** One physical storage device can appear as multiple block devices - for example, a single SSD (`/dev/sda`) creates separate block devices for each partition (`/dev/sda1`, `/dev/sda2`). Block devices are how the kernel abstracts storage hardware for uniform access, regardless of whether it's an SSD, HDD, or USB drive.

### **No, a remote drive is NOT a block device.**

While a remote drive is a file storage system, it is **not** a block device in the way Linux defines it.

we first need to understand block and page

## block
a hardware/storage level concept (as opposed to memory maangement concept of page). It is the unit size that **storage devices (hdd/ssd)** reads/writes data in. 

## page 
its memory management concept. It is unit size that **OS divides RAM into** for virtual memory. 

so look at how block and page works tgt [here](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Memory/Swapping.md)


### 1. Block Device (What `lsblk` Shows) 🧱

A **block device** is a piece of storage where the operating system interacts directly with the physical hardware (or a virtual equivalent) to read and write data in fixed-size **blocks**.

* **Examples:** Physical Hard Drives (HDD/SSD), USB sticks, CD-ROMs, and virtual disk images (like VDI or VMDK).
* **How it Works:** The OS bypasses the network protocol and sees the device as a raw array of blocks. It then applies a local filesystem (like ext4 or XFS) to manage those blocks.
* **Command:** `lsblk` lists these because it queries the kernel's block device subsystem.

### 2. Network Filesystem / Remote Drive (What `df` and `mount` Show) 📂

A **network filesystem** (like NFS, SMB/CIFS) is a file storage system, but it is accessed entirely over the network using a **protocol stack**, not raw hardware blocks.

* **Examples:** Shared folders on a server, corporate file shares, cloud storage.
* **How it Works:** The client machine (your laptop) sends **commands** (like "read this file," "list this directory") over a network protocol (TCP/IP) to the remote server. The remote server handles all the low-level block-level I/O.
* **Result:** Since your kernel is talking to a network service—and not to a raw physical disk block-by-block—it **does not** register the remote share as a block device.

In summary, the reason `lsblk` excludes your network share is because it is a **protocol-based abstraction layer** above the network, not a **physical hardware layer** below the filesystem.
