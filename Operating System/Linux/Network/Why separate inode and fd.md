## why
The core reason for separating the **inode** and the **file descriptor (fd)** in Unix-like operating systems is to effectively manage the distinction between a file's **permanent identity** and a process's **temporary use** of it. The **inode** is a permanent, system-wide data structure that holds the file's **metadata** (permissions, size, disk location, etc.), existing even when the file is closed. In contrast, the **file descriptor** is a temporary, per-process integer handle that grants access to an "open file description," which tracks the **dynamic state** of a file, such as the **current read/write offset** and the access mode. This separation is crucial for enabling features like **concurrent access** (two processes reading the same file at different positions) and **hard links** (multiple names pointing to the same file) while also providing a unified interface for accessing various resources, including non-disk entities like sockets and pipes.

i thought fd's incapability to read closed files is the reason why we need inode but this incapability is a consequence, not a reason. Processes need independent positions when reading or writing to the same file 
```
# Process A is a log reader, reading from line 1000
cat /var/log/syslog | tail -n 100  # position at byte 50000

# Process B is also reading the SAME log file from the beginning
grep "error" /var/log/syslog  # position at byte 0
```
if both processes share just 1 position, then process B would overwrite process A's read position to byte 0. That is why the read position is saved in the FD.
