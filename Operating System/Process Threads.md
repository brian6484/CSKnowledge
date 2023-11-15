## Definitions
Process is a *unit of work* that has been allocated resources from the OS.
Thread is a *unit of execution flow that uses the resources allocated to a process.

## program -> process -> thread
The general flow goes like this but what exactly is a program? Refresh your memories 
[here][https://github.com/brian6484/CSKnowledge/blob/main/Network/Program.md]. 

So once program's state goes from static to dynamic, as mentioned in that link it is loaded into RAM and is now
in dynamic state. The program in dynamic state is known as **process**. So process is a computer program that is 
executed by CPU and loaded in RAM as such

![Screenshot 2023-11-15 000637](https://github.com/brian6484/CSKnowledge/assets/56388433/193ba13e-5ee5-447d-b751-994032dcfede)

## process -> thread
Back in the days, program used to run from start to end in just 1 process. But as programs gets more demanding in terms of workload, 
using just one process is insufficient. Why not we use multiple processes to run 1 program then? We will get to IPC but back then,
it was imposs bcos OS allocated resources to 1 specific process, which cannot be shared with other processes for *safety reasons*.
So process had to have a smaller unit of work - *thread*.

![Screenshot 2023-11-15 123548](https://github.com/brian6484/CSKnowledge/assets/56388433/10d6a5cb-1d61-42d9-8906-7247134c8203)

Thread is a specific execution path *within a process* so it shares the memory with other threads within that process.


