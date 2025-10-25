1) diff between process and thread

A **process** is an independent, running instance of a program, viewed by the Operating System (OS) as a completely separate execution environment with its own dedicated resources, 
including a private virtual address space, file descriptors, and a unique Process ID (PID). This "completely separate executio env" means OS treats each process as a self-contained unit that
cannot directly interfere with other process's resources or memory. In contrast, a **thread** is a light-weight unit of execution that exists *within* a process; it has its own program counter, register set, and stack (for function calls and local variables), but it **shares** the process's resources—specifically the code segment, global data, and heap memory—with all other threads in that same process, which is why creating a thread and switching between them is significantly faster and less resource-intensive than doing the same for a process.

