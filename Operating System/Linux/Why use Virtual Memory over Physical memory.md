First we have to understand physical and virutal memory

## Physical memory
actual hardware memory installed in your system. I always thought its RAM + disk cuz its **physical memory devices** but its only
just RAM. This is cuz CPU can directly access (read and write) from and to RAM with memory addresses, sending an address on the address bus and 
and data comes back on the data bus within few CPU cycles. But disk cannot be accessed directly by CPU.

## Virtual memory
abstraction layer that provides each process with an illusion of **its own large, contiguous address space**. So each process gets its own 
virtual address space (typically 4GB on 32-bit systems).

## Prob with using physical memory
[Memory Fragmentation and Allocation Chaos]
Without virtual memory, Program A might use physical addresses 0x1000-0x5000, Program B uses 0x6000-0x8000, and Program C uses 0x9000-0xC000. When Program B exits, you have a 0x2000-byte hole in the middle of memory. If Program D needs 0x3000 bytes, it can't fit in that hole, even though there's enough total free memory. Over time, physical memory becomes a Swiss cheese of unusable fragments.

[Security Nightmare]
Any program could read or write any physical memory location. Program A could easily corrupt Program B's data, steal passwords from Program C, or even modify the operating system kernel. There would be no isolation between processes whatsoever.

[Inflexible Memory Layout]
Programs would need to know their exact physical memory locations at compile time, or use complex runtime relocation. You couldn't run two copies of the same program simultaneously because they'd try to use the same physical addresses.

[No Memory Sharing]
Multiple programs using the same library (like libc) would each need their own copy in physical memory, wasting enormous amounts of RAM.

## How VM saves this problem
[Isolation and Protection]
Each process gets its own complete virtual address space (0x00000000 to 0xFFFFFFFF on 32-bit systems). Process A's virtual address 0x1000 is completely separate from Process B's virtual address 0x1000. The hardware enforces this separation.

[Simplified Programming Model]
Every program can be compiled assuming it starts at address 0x00000000, with a predictable layout: code at the bottom, data above that, heap growing up, stack growing down from the top. Programs don't need to know where they actually reside in physical memory.

[Efficient Memory Use]
Multiple processes can share the same physical pages for read-only code and data. When you run 10 copies of a text editor, they all share the same physical pages for the executable code.

[Demand Loading]
Only the parts of a program currently being used need to be in physical memory. A 100MB program might only have 5MB actually loaded at any given time.
