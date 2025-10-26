1) Copy-on-write

This is for efficient memory usage by fork(). So when fork() happens, the parent and child processes share the same physical pages and they are marked as **read-only**. So theres no separate pages 
for both processes, which saves memory.

But when either process tries to write to a shared page, CPU attempts to write to a **read-only page**, which MMU generates a **page fault**. The OS page fault handler sees that it is COW page and
copies the page **within RAM** so no disk is involved. That is why it is minor page fault.

2) Lazy allocation

- OS promised memory but hasn't allocated physical RAM yet
- Page doesn't exist anywhere yet
- Process accesses it → page fault → OS allocates new RAM page (zeros it out)
= ✗ This is NOT disk access - creating new RAM

lazy allocation is when ur allocating memory page for the first time
