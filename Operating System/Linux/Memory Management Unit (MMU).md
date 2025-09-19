## MMU
It converts virtual addresses (what programs see) into physical addresses (in the RAM).

### How it works
When a program accesses memory:

1) CPU generates a virtual address (e.g., 0x1000)
2) MMU intercepts this address
3) MMU consults the page table to find the corresponding physical address (e.g., 0x5A2000)
4) MMU provides the physical address to the memory bus
5) RAM returns the data at that physical location

### Features
Translation Lookaside Buffer (TLB):

- High-speed cache of recent address translations
- Avoids slow page table lookups for frequently accessed pages
- Dramatically improves performance

[Page Fault Handling](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Page%20Fault.md):

- When a virtual page isn't in physical memory, MMU triggers a page fault
- OS handles loading the page from disk or allocating new memory

[Memory Protection Bits](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Memory%20Protection%20Bit.md):

- Read/Write/Execute permissions per page
- User/Kernel mode access control
- Prevents buffer overflows from executing malicious code



