# Multithreading vs Multitasking
## Multitasking
In 1 OS, multiple processes are running (but not at the same time)

## Multithreading
In 1 process, multiple threads are running.

### Advantage
1) During [context switching][https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Context%20switching.md], memory
resources the size of shared memory (code/data/heap) can be saved.
2) Since threads share memory area except stack within the process, communication burden is low and response time is fast

### Disadvantage
1) If one thread destroys resources, the other threads may be terminated
2) problem of synchronisation because resources are shared

Also in mulithreading, we dont know which thread will run and the thread after that in context switching.
