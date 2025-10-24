## Tracing
It is monitoring & recording program/system execution *in real-time*. We normally use it for debugging or performance analaysis. Theres 2 levels - users-space (applications) via `strace` and Kernel space (OS internals) via `ftrace` and `perf`.

## strace
strace traces **system calls and signals** that a program uses. The concept behind strace is sytem call cuz user space programs (like ls or cat or ur own scripts) cannot directly access system resources like file system, network or hardware. So need to ask kernel via system call.

### how
so firstly **ptrace() system call** is SUPER IMPT cuz it allows program (i.e. the tracer, which is my strace) to inspect and control the execution of another program (i.e. the tracee, the program that im trying to debug).

1) Attaching/Launching: strace launches or attaches to the target program (via fork/exec) and uses ptrace() system call to become its parent/tracer
2) Interception: kernel is configured by `ptrace()` to stop the target process **every time it tries to execute or return from a system call**
3) Logging: when process stops, strace inspects the reigsters of stopped process to see which system call is being attempted (read/write/open) and the arguments passed to it
4) resumption: strace then tells kernel to resume this execution of process
5) output: for every system call, strace prints a line of output to standard error.

so strace is like a debugging point. 

## what if i strace on process with D state?
So i thought strace can see the history of system calls to the tracee. But actually thats wrong. strace is real-time so  if a process is in $\text{D}$ state, $\text{strace}$ cannot attach and will wait forever for the process to become runnable again.
