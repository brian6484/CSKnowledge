## Page fault
There are 2 types of pag fault - valid and invalid. Valid page fault happens when process tries to access virtual memory page that **isnt loaded in physical RAM**. But invalid page fault is acccessing a protected kernel memory or memory that this process doesnt have permission for.

Theres 2 types of valid page fault - major and minor and segmentation and this swapping is called major page fault. Theres minor page fault that doesnt require slow disk i/o and just involve cpu and ram. [copy on write or lazy allocation]() where minor page fault is handled entirely in RAM without touching disk.

## Flow
The Page Fault Process

1) MMU Detects Missing Page: CPU tries to access virtual address, MMU can't find it in page tables
2) Hardware Interrupt: MMU triggers a page fault interrupt to the CPU
3) OS Takes Control: Kernel's page fault handler runs
4) OS Investigates: Determines why the page isn't available
5) OS Takes Action: Loads page from disk, allocates new memory, or handles error
6) Update Page Tables: OS updates page tables with new physical address
7) Resume Process: Process continues as if nothing happened

So page fault is a **hardware interrupt**, not a **software signal like SIGTERM/SIGKILL**.

## Types of Page Faults
Minor Page Fault: Page is in memory but not in process's page table (just needs mapping). Absence in page table could be of lazy mapping, where  C runtime library allocates the virtual address space, but the OS often doesn't assign physical pages or update the page table until the program actually attempts to write data to that new memory.

Major Page Fault: Page must be loaded from disk (slow operation)

Segmentation Fault: Invalid access - process tried to access memory it shouldn't (terminates process)
