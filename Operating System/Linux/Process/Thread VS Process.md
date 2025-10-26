
In Linux, a **process** is an independent execution unit with its own **address space** and system resources, while a **thread** is a **lightweight execution unit** within a process that shares most of its resources.

* **Process:**
  Each process has its own **virtual address space**, including its own **code, data, heap, and stack segments**.
  Communication between processes requires explicit mechanisms like **pipes, message queues, or shared memory (IPC)**.

* **Thread:**
  Threads within the same process **share** the same **code, data, and heap**, but each thread has its **own stack** for local variables and function calls, as well as its own **CPU registers** and **thread ID (TID)**.
  Because of this shared memory, threads can communicate and access shared data faster but must handle **synchronisation** carefully to avoid race conditions.

---

**Concise interview version:**

> “A Linux process has its own independent memory space, including code, data, heap, and stack. Threads, on the other hand, share the same code, data, and heap of the process but have their own stacks and registers. This makes threads more lightweight but requires careful synchronisation.”

---

