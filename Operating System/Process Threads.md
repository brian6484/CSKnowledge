## Definitions
Process is a *unit of work* that has been allocated resources from the OS.
Thread is a *unit of execution flow* that uses the resources allocated to a *process*.

## program -> process -> thread
The general flow goes like this but what exactly is a program? Refresh your memories 
[here][https://github.com/brian6484/CSKnowledge/blob/main/Network/Program.md]. 

So once program's state goes from static to dynamic, as mentioned in that link it is loaded into RAM and is now
in dynamic state. The program in dynamic state is known as **process**. So process is a computer program that is 
executed by CPU and loaded in RAM as such

![Screenshot 2023-11-15 000637](https://github.com/brian6484/CSKnowledge/assets/56388433/193ba13e-5ee5-447d-b751-994032dcfede)

## process -> thread
Back in the days, program used to run from start to end in just 1 process. But as programs gets more demanding in terms of workload, 
using just one process is insufficient. Why not we use multiple processes to run 1 program then? There are actually 3 ways - IPC, LPC
and making a separate shared memory area to be shared amongst processes. But these requires a replacement of CPU register, initialising
cache memory between RAM and CPU so the burden is big. Back then it was imposs bcos OS allocated resources to 1 specific process, which 
cannot be shared with other processes for *safety reasons*.

So process had to have a smaller unit of work - *thread*.

![Screenshot 2023-11-15 123548](https://github.com/brian6484/CSKnowledge/assets/56388433/10d6a5cb-1d61-42d9-8906-7247134c8203)
The time arrow is not that clear but as time arrow is downwards.

Thread is a specific execution path *within a process* so it shares the memory with other threads within that process. If process is a chunk
of code, thread is an individual function in that process.

## Exactly what resources are allocated to process/thread by OS?

### Process
![Screenshot 2023-11-15 131830](https://github.com/brian6484/CSKnowledge/assets/56388433/c1a1374b-2100-4537-9eeb-c0ce7640eb40)
For process, OS assigns an independent memory area to each process - in the form of code, stack, heap and data.
Because the memory area is *independent* and specific to that process, other processes cannot access it.

### Thread
![Screenshot 2023-11-15 131841](https://github.com/brian6484/CSKnowledge/assets/56388433/2de85bae-c85e-43fc-a1cc-9a793c3417b0)
In the independent memory area of a process, the memory area *in the form of Stack* is allocated separately for each threads
but the remaining memory area in Code/Data/Heap format is shared. So each thread has a separate stack but the heap area can be read
and written by all the threads in that process.

## Diff between the 2 
So if a proces fails, it will not affect other process (unless a file that is shared is corrupted) but for a thread, since it shares
Code/Heap/Data memory area, if a thread fails then other threads also fail in that same process. It is like if your function(thread) fails,
it stops other functions from running and stops the process.

## Perspective
For a CPU, the minimal unit of work is a thread but from OS perspective, the minimal unit of work is a process. So if process is a smallest unit of work
by OS, then threads that belong to a process should have the same memory area.






