## MMU
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
## The Problem with Single-Level Page Tables

Imagine a 32-bit system with 4KB pages:
- Virtual address space: 4GB (2³²)
- Page size: 4KB (2¹²)
- Number of pages: 4GB ÷ 4KB = 1 million pages
- **Single page table would need 1 million entries!**

Even worse, **every process** would need its own 1-million-entry page table, even if the process only uses a few pages.

## Multi-Level Solution

Instead of one huge table, use a **tree structure** with multiple smaller tables.

## Two-Level Example (32-bit system)

Split the virtual address differently:
```
32-bit Virtual Address: [Directory Index][Table Index][Offset]
                        [   10 bits   ][  10 bits  ][12 bits]
```

**Level 1 - Page Directory**: 1024 entries (10 bits)
**Level 2 - Page Tables**: Each has 1024 entries (10 bits)

## How Translation Works

Let's translate virtual address `0x12345678`:

```
Virtual Address: 0x12345678
Binary: 00010010001101000101011001111000

Split into parts:
Directory Index: 001001000 (0x48 = 72)
Table Index:     1101000101 (0x345 = 837) 
Offset:         011001111000 (0x678)
```

**Step 1**: Look up entry 72 in Page Directory
```
Page Directory[72] → Points to Page Table #5
```

**Step 2**: Look up entry 837 in Page Table #5
```
Page Table #5[837] → Physical frame 0x9AB
```

**Step 3**: Combine physical frame + offset
```
Physical Address: 0x9AB678
```

## Memory Savings Example

**Single-level**: Every process needs 1 million entries = 4MB per process

**Two-level**: 
- Process using only 100 pages spread across 3 different page tables
- Page Directory: 1024 entries (always needed)
- Page Tables: Only 3 tables × 1024 entries = 3072 entries
- **Total: 4096 entries instead of 1 million!**

## Visual Representation

```
Virtual Address 0x12345678
       ↓
┌─────────────┐
│ Page Dir[72]│ ──┐
└─────────────┘   │
                  ↓
              ┌─────────────┐
              │PageTable[837]│ ──→ Physical Frame 0x9AB
              └─────────────┘
                                           ↓
                                    Physical Address 0x9AB678
```

## Modern 64-bit Systems (4-Level)

x86-64 uses 4 levels because 64-bit address space is huge:
```
48-bit Virtual Address:
[PML4][PDP][PD][PT][Offset]
[ 9 ][ 9 ][9 ][9 ][  12  ] bits each
```

## Key Benefits

**Space Efficient**: Only allocate page tables for memory actually being used
**Flexible**: Can easily add/remove page tables as processes grow
**Fast**: Still only a few memory accesses to translate an address
**Scalable**: Works for tiny processes and huge processes

The multi-level approach trades a bit of translation complexity for massive memory savings!
