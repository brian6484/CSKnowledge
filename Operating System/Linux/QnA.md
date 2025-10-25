1) diff between process and thread

A **process** is an independent, running instance of a program, viewed by the Operating System (OS) as a completely separate execution environment with its own dedicated resources, 
including a private virtual address space, file descriptors, and a unique Process ID (PID). This "completely separate executio env" means OS treats each process as a self-contained unit that
cannot directly interfere with other process's resources or memory. In contrast, a **thread** is a light-weight unit of execution that exists *within* a process; it has its own program counter, register set, and stack (for function calls and local variables), but it **shares** the process's resources—specifically the code segment, global data, and heap memory—with all other threads in that same process, which is why creating a thread and switching between them is significantly faster and less resource-intensive than doing the same for a process.

2) Explain the different states a process can be in.
* **R (Running/Runnable):** This means the process is either actively **executing** instructions on a CPU, or it is ready to run and waiting in the run queue for the CPU scheduler.
* **S (Interruptible Sleep):** The process is **waiting for an event** (like input, a timer, or a resource) to complete. Crucially, it **can be woken up** by signals (e.g., `SIGTERM` or `SIGKILL`).
* **D (Uninterruptible Sleep):** The process is performing a kernel-level I/O operation (like disk read/write) that must **not be interrupted**. It **ignores all signals**, including `SIGKILL`, until the I/O operation finishes or the system reboots. This is the state that typically indicates a hardware or I/O subsystem problem if prolonged.
* **T (Stopped):** The process has been **suspended** by a job control signal (like `SIGSTOP` from `Ctrl+Z`) or is being traced by a debugger. It can be resumed with a `SIGCONT` signal.
* **Z (Zombie/Defunct):** The process has **terminated** and released all its resources, but its entry (including its exit status) is still held in the process table. It remains here until the parent process calls `wait()` or `waitpid()` to "reap" or clean up its status. If a process is stuck in this state, it indicates a **bug in the parent process**.

3) What is a Process Control Block (PCB), and what information does it typically contain
The **Process Control Block (PCB)**, also known as the Task Control Block, is a critical data structure used by the operating system to manage and track every process. It acts as a process's "identity card" and is absolutely essential for a multitasking OS to switch between processes (context switching) and resume execution from the exact point where it left off. The PCB contains essential information, including the process's unique **Process ID (PID)** and current **state** (Running, Ready, Waiting, etc.); the saved execution context, such as the **Program Counter** and **CPU Registers**, which are vital for performing a seamless **context switch**; and various resource management details like **scheduling priority**, memory management information (e.g., page table pointers), and a list of **open files** and I/O devices allocated to the process.

4) Whats CPU register and Program Counter?
The **CPU Registers** and the **Program Counter (PC)** are essential, high-speed storage locations residing directly within the processor that together define a process's current execution state. **CPU Registers** are the processor's temporary "scratchpad," holding data, memory addresses, and intermediate results required for immediate calculations, while the **Program Counter** is a special register that stores the memory address of the very next instruction the CPU must fetch and execute, thereby controlling the sequential flow of the program. Saving the full contents of both the registers and the PC to the Process Control Block (PCB) is the single most critical step that enables the operating system to successfully perform a **context switch** and later resume the process exactly where it left off.

This is cuz when os switches from process a to b, Process B would immediately overwrite all the values in the registers with its own data. If Process A's data isn't saved, when Process A resumes, it will perform calculations using Process B's data, leading to corruption, incorrect results, or a fatal error. And for PC, When the OS interrupts Process A (a context switch), the PC holds the exact line of code Process A was about to run. If the OS did not save and restore this value, when Process A was resumed later, the CPU would have no idea where the program was, and the process would start over or crash. 

5) Describe the concept of a Context Switch. What overhead does it introduce?
A **context switch** is the process where the Central Processing Unit (CPU) halts the execution of one process or thread and begins executing another. It is the fundamental mechanism that enables **multitasking** in an operating system. The process involves two primary overhead steps: **saving the context** (the current state) of the running entity into its Process Control Block (PCB) or Thread Control Block (TCB), and then **loading the context** of the next entity from its control block into the CPU's registers.

The overhead of a context switch differs significantly between processes and threads. A **process switch** is more expensive because processes have independent memory spaces, requiring the OS to perform the register save/load and critically, update the **Memory Management Unit (MMU)** by changing the page table pointers (often involving a TLB Translation Lookaside Buffer flush). This is cuz When the Operating System (OS) decides to switch to a different process (Process B):

The OS saves the current value of the Page Table Base Register (which points to Process A's table) into Process A's PCB. This page table base register is a type of cpu register that holds a page table **pointer** to the **starting address of page table**. 

The OS loads the Page Table Base Register with the address of Process B's page table, which it retrieves from Process B's PCB.

This is the "update the Memory Management Unit by changing the page table pointers" step. The MMU is now ready to correctly translate addresses for Process B. TLB also needs to be flushed cuz we are now using a completely differnt page table. Whereas a **thread switch** is much cheaper because threads within the same process share the same memory map, meaning the OS only needs to perform the quick register save/load without touching the MMU's translation data.

6) 



