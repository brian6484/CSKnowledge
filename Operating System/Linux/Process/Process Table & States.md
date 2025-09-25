## Process Table
Its different from page table, which converts virtual memory address to physical address. Process table is a kernel data structure that
tracks **all running processes in system**. Think of it as the kernel's "master list" of every process.

```
ps aux    # Shows process table info
top       # Live view of process table
cat /proc/<PID>/status  # Detailed info for specific process
```

## States
R - Running (using CPU)
S - Sleeping (interruptible)
D - Sleeping (uninterruptible)
Z - Zombie (finished but not reaped)
T - Stopped
X - Dead

Interruptible Sleep (S) = Light Sleeper
A person who wakes up easily when you tap them or make noise. Process is waiting for something, but can be interrupted by signals.
It is normally safe to interrupt.

Its normally like sleep(), or wait() for child process or waiting for keyboard input.

Uninterruptible Sleep (D) = Deep Sleeper
A person who won't wake up even if you shake them - they're in deep surgery and must not be interrupted. Process is doing something critical that cannot be safely interrupted. If interrupted: File system corruption, kernel panic, data loss!

Its normally like hard disk read/write operations, Hardware device drivers waiting for hardware, Network File System (NFS) operations which is letting me access files on a remote computer. NFS needs D state cuz network req is "in flight" where kernel is wating for response. Interrupting may corrupt network protocol. Also, File system consistency requires atomic operations.

```
# Example: Mount a remote directory
sudo mount -t nfs 192.168.1.100:/home/shared /mnt/remote
                  └─────────────────────────┘ └──────────┘
                        REMOTE PATH           LOCAL PATH

# Now you can use remote files as if they're local:
ls /mnt/remote/          # Actually lists files on 192.168.1.100
cat /mnt/remote/file.txt # Reads file from remote server
cp local.txt /mnt/remote/ # Copies to remote server
```

## Example
```
PID   PPID  STATE  COMMAND
1     0     S      init
1234  1     S      bash
5678  1234  R      ls
5679  1234  Z      <defunct>  # Zombie!
```


