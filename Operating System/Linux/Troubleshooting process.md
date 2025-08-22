## context
u first have to know what program and process is. Then, we should know that **program itself doesnt interact directly with
hardware or OS**. Program asks the kernel to do things like a Princess

## strace
So strace sits beside the program and kernel to log all requests.
It traces **system calls** made by a process. It works by using the ptrace system call, which allows one given process (the tracer)
to observe and control the other (tracee). So every time the tracee makes a system call like open(), read(), write(), `strace`
intercepts and logs
- system call name
- arguments
- return value

## ltrace
logs **library calls** thhe process makes. So unlike system calls with strace like read,open,write, `ltrace` logs library calls that
the process makes like printf(), malloc(). malloc() allocates memory dynamically.

## perf
logs **cpu-level performance events** of process. So things like instructions executed, cache misses, branch mispredictions, etc.
Branch misprediction is about what CPU is doing while running my code.
