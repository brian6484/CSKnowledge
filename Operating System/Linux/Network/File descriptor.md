## FD
its a non-negative number that is like a *ticket number* that ur program use to refer to an **open file** (not closed). When process opens file, kernel
gives this FD number. This number is **local to the process** meaning multiple processes can have the same FD number that points to an open file. The file descriptor (fd) is a temporary identifier that only exists after a process calls the open() system call and is destroyed when the process calls close(). It represents a process's active connection to that file.

it also stores the process's read position of that file cuz **processes require their own read/write positions to 1 same file** [explained here](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Network/Why%20separate%20inode%20and%20fd.md)

actually the abve statement is wrong. FD doesnt store the read position but this file object is the one that stores it. And FD is just an interger and in the FD table, this FD maps to -> file object whic stores this read position, flag and mode.

gpt:
**The file object stores the file position, NOT the FD itself.** The FD is just a number (an index). However, you're right that each process needs its own read/write position for the same file. Here's how it works:

When **different processes** open the same file, each gets its own FD (say process A gets FD 3, process B gets FD 4), and each FD points to a **separate file object**. Each file object has its own position, so the processes can read independently without interfering with each other.

**But here's the key:** When a single process opens the same file twice (or after `fork()`), it can either:
- Get two separate file objects (two independent positions) - happens with two separate `open()` calls
- Share the same file object (same position) - happens after `fork()` where parent and child share file objects

**What the FD table stores:** Each process has an FD table that maps FD numbers to file object pointers. The FD itself is just the index - the file object it points to is where position, flags, and mode are stored.

So you're right that each process needs independent positions, but that's achieved through separate file objects per process, not by storing position in the FD itself. The FD is just the reference number.
