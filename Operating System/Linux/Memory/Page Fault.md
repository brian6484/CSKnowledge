## Page fault
Happens when process tries to access virtual memory page that **isnt loaded in physical RAM**.

## Diff between page fault and SEGFAULT
my original thought:seg fault and page fault diff is seg fault is when process tries to access memory that is beyond its permission or its boundary but page fault is when theres no page in the ram so it calls from disk right? Yes but more technically, page fault occurs when program tries to access a virtual memory address that the MMU finds is **not correctly mapped or is not accessible** according to the page entry table.

seg fault is correct but page fault is not specific to disk access. When page is on disk (swapped out), OS loads it from swap thats true. Theres 3 types of page fault - major and minor and segmentation and this swapping is called major page fault. Theres minor page fault that doesnt require slow disk i/o and just involve cpu and ram.

copy on write or lazy allocation where minor page fault is handled entirely in RAM without touching disk is as such:

1) Copy-on-write

Page IS already in RAM (shared between parent and child)
Process tries to write → page fault → OS copies within RAM
✗ This is NOT disk access - just copying RAM to RAM

cow is when memory pages are read only and a program tries to write on it.

2) Lazy allocation

OS promised memory but hasn't allocated physical RAM yet
Page doesn't exist anywhere yet
Process accesses it → page fault → OS allocates new RAM page (zeros it out)
✗ This is NOT disk access - creating new RAM

lazy allocation is when ur allocating memory page for the first time

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
