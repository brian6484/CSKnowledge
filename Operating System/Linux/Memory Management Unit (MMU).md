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
