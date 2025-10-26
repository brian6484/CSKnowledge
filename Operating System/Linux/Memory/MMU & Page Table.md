## MMU

## diff between mmu and page table
mmu is the **hardware** that is a physical chip in cpu whereas page table is a **data structure (software)** that is stored in ram and managed by os

The Two Main Roles of the MMU
1. Address Translation
This is the primary role of the MMU. Programs operate in a virtual address space, which is a logical view of memory that is unique to each process. For example, a program might think it has access to memory addresses from 0 to 4GB. The MMU is the hardware component that, in collaboration with the OS, translates these virtual addresses into the real physical addresses in RAM.

This address translation is what makes virtual memory possible, allowing the OS to load parts of a program into memory and swap others out to the hard drive, giving the illusion of having more memory than is physically available.

2. Memory Protection
As part of the address translation process, the MMU checks the permissions of the memory page being accessed. Each page of memory is tagged with permissions (e.g., read-only, read/write, execute) via [Memory Protection Bit](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Memory/Memory%20Protection%20Bit.md) The MMU can be configured by the operating system to prevent a process from accessing pages that aren't assigned to it.

This is how it enforces the boundary between user space and kernel space. When a user-mode program attempts to access a memory address that the MMU's page tables map to the kernel's memory space, the MMU detects this violation and generates a page fault. This triggers a hardware interrupt, allowing the operating system to take control and terminate the offending process. This mechanism is crucial for system stability and security, as it prevents a single misbehaving application from corrupting the entire system.

Essentially, MMU & page table work together to translate virtual address to physical address. MMU is the **actual hardware** that performs this translation while page table is the **data structure** that MMU uses to look up and translate.

## Page table
A page table is a data structure maintained by the operating system that maps virtual memory addresses to physical memory addresses. It's essentially the "phone book" that the MMU uses to translate addresses.

## What's in a Page Table
Each entry in a page table contains:
1) Physical Page Frame Number: Where this virtual page is actually stored in RAM
2) Permission Bits: Read/Write/Execute permissions we just discussed
3) Present Bit: Is this page currently in physical memory?
4) Dirty Bit: Has this page been modified since loaded?
5) Accessed Bit: Has this page been recently used?

For example, it could look like
```
Virtual Page Number → Page Table Entry → Physical Page Frame Number

Example:
Virtual Address: 0x12345678
├─ Page Number: 0x12345 (upper bits)
└─ Offset: 0x678 (lower bits)

Page Table[0x12345] = {
    Physical Frame: 0x9ABCD,
    Permissions: R+W+X,
    Present: 1,
    Dirty: 0
}

Physical Address: 0x9ABCD678
```
