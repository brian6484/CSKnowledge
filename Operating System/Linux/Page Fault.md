## Page fault
Happens when process tries to access virtual memory page that **isnt loaded in physical RAM**.

## Flow
The Page Fault Process

1) MMU Detects Missing Page: CPU tries to access virtual address, MMU can't find it in page tables
2) Hardware Interrupt: MMU triggers a page fault interrupt to the CPU
3) OS Takes Control: Kernel's page fault handler runs
4) OS Investigates: Determines why the page isn't available
5) OS Takes Action: Loads page from disk, allocates new memory, or handles error
6) Update Page Tables: OS updates page tables with new physical address
7) Resume Process: Process continues as if nothing happened

## Types of Page Faults
Minor Page Fault: Page is in memory but not in process's page table (just needs mapping)
Major Page Fault: Page must be loaded from disk (slow operation)
Segmentation Fault: Invalid access - process tried to access memory it shouldn't (terminates process)
