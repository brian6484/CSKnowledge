## inode
remember that when troubleshooting, its not just memory on disk that matters but also the remaining number of inodes that matter?

inode(index node) is a data structure that sotres the file metadata but not the actual file name and data. Each file has a unique inode
number.

The inode is a permanent structure that represents the file on the disk and** always exists** (unlike FD that is a temporary ID for **open** connections/files), regardless of whether the file is currently being used (open) or not (closed). It is the file's identity.
