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

## Example
```
PID   PPID  STATE  COMMAND
1     0     S      init
1234  1     S      bash
5678  1234  R      ls
5679  1234  Z      <defunct>  # Zombie!
```


