## What are pages?
Pages are fixed-size blocks (typically 4kb) that OS uses to divide both virtual and physical RAM into uniform chunks. Virtual pages from a process can be mapped to physical frames (pages in RAM via page table, allowing OS to allocate memory flexibly without external fragmentation - like dividing memory into same-sized LEGO bricks that can be arranged anywhere instead of requiring one continuous block.

The kernel tracks different types of pages based on their state and usage.

## Clean Pages

**Clean pages** are memory pages whose contents are **identical to what's on disk**.

### Characteristics:
- **Can be immediately discarded** without writing to disk. For example, cache cuz its just a copy of data so even if u remove that, the main data isnt affected. 
- If the system needs memory, these pages can be freed instantly
- No data loss if removed from RAM
- **Safe to evict** under memory pressure

### Examples of Clean Pages:

**1. Cached file content that hasn't been modified:**
```bash
# Read a file - it gets cached in RAM
cat /var/log/syslog

# This creates CLEAN pages in the page cache
# The content in RAM matches what's on disk
```

**2. Program code/executables:**
```bash
# When you run a program, its binary(A binary is a file containing machine code that a computer's processor (CPU) can directly execute.) is loaded into RAM
/usr/bin/python3

# The executable code pages are CLEAN
# They match the /usr/bin/python3 file on disk
```

**3. Shared libraries:**
```bash
# When loaded, library pages are clean
ldd /usr/bin/python3
# All these .so files create clean pages when loaded
```

### Why Clean Pages Matter:
- **Free memory quickly**: Under memory pressure, kernel can drop clean pages instantly
- **No I/O penalty**: Dropping them requires no disk writes
- **Easily reloaded**: If needed again, just read from disk

## Dirty Pages
**Dirty pages** = Pages modified in RAM but **not yet written to disk**

### Examples:
```bash
# Write to a file
echo "new data" >> /tmp/myfile

# This creates DIRTY pages
# RAM content differs from disk content
```

### Characteristics:
- **Cannot be immediately discarded**
- Must be written to disk first (flushed)
- Tracked by kernel's `pdflush`/`flush` threads
- Higher cost to evict

## Active Pages

**Active pages** are pages that have been **recently accessed** or are **frequently used**.

### The Active/Inactive Lists

Linux maintains two LRU (Least Recently Used) lists for pages:

```
┌─────────────────┐
│  Active List    │  ← Recently/frequently accessed pages
└─────────────────┘
         ↓
    (ages out)
         ↓
┌─────────────────┐
│  Inactive List  │  ← Not recently accessed
└─────────────────┘
         ↓
    (evicted first)
```

### How It Works:

1) Page is accessed first time → Goes to ACTIVE list (not inactive!)
2) Active page not accessed for a while → Demoted to INACTIVE list
3) Page accessed again while inactive → Promoted back to ACTIVE list
4) Under memory pressure → INACTIVE pages evicted first

### Active vs Inactive:

| Active | Inactive |
|--------|----------|
| Recently used | Not recently used |
| Protected from eviction | First to be evicted |
| "Hot" pages | "Cold" pages |
| Higher priority | Lower priority |

## Anonymous page (anon)
## The Two Types of Memory Pages - anonymous and file-backed pages

An anonymous page is a memory page that isn't backed by any file on disk - it only exists in RAM and has no associated file. Examples include stack memory, heap memory (malloc), and uninitialized data - when these pages need to be swapped out, they go to the swap space (not back to a file) because they were created "anonymously" by the process rather than loaded from a file like executable code or memory-mapped files.

So when anon pages are swapped out to swap space (area on disk that OS uses as overflow storage for RAM), they stay there until the process needs them then there major page fault. But if process terminates before they are swapped back in, the swap space is freed. So anon pages are just temporary data that only exist during process's lifetime.

| Category | Anonymous Page (anon) | File-Backed Page (file) |
| :--- | :--- | :--- |
| **Backing Store** | **Swap Space** (or nothing) | **A file** on the hard drive (e.g., `/usr/bin/python3`). |
| **Origin/Content** | Data **created by the program** (variables, heap). | Data **read from a file** (program code, libraries, cached data). |
| **Reclamation** | If RAM is needed, this page **must be written out to swap space** (a "dirty" process). | If RAM is needed, this page is either **discarded** (if clean) or **written back to its original file** (if dirty). |
| **Examples** | Program **stack** and **heap** (where `malloc` allocates memory). | The **executable code** of Python, shared **libraries** (`libc`), and the **disk cache**. |

## How Anonymous Memory is Created
Anonymous memory pages are created when a running program needs space to do work, not just to hold a copy of a file:

1.  **Heap Allocation (The Big One):** When a program calls a function like `malloc()` (in C) or creates a large object (in Python/Java), that memory is usually allocated from the process's **heap**. This is just blank RAM given to the program, and it's anonymous.
2.  **The Stack:** The memory used for local variables inside a function call is part of the stack, which is also anonymous.
3.  **Copy-on-Write (CoW):** When a process forks (creates a child process), the memory pages are often shared. If the child process then **writes** to one of the shared file-backed pages (like a program's global variable), that page is copied and immediately becomes a **private, anonymous page** for the child.

**In short: Anonymous pages hold the unique, state-changing data of your running applications. It's the memory that truly belongs to the process and has no original counterpart on the filesystem.**

## Viewing These in Linux
### **/proc/meminfo** - detailed, real-time snapshot of the system's memory usage and configuration

proc means process.
```bash
cat /proc/meminfo
```

**Key fields:**
```
MemTotal:       16384000 kB   # Total RAM
MemFree:         2048000 kB   # Completely unused
MemAvailable:   10240000 kB   # Available for allocation

Active:          8192000 kB   # Active pages (recently used)
Inactive:        4096000 kB   # Inactive pages (not recently used)

Active(anon):    2048000 kB   # Active anonymous pages (process memory)
Inactive(anon):  1024000 kB   # Inactive anonymous pages

Active(file):    6144000 kB   # Active file cache (clean or dirty)
Inactive(file):  3072000 kB   # Inactive file cache

Dirty:            512000 kB   # Modified, not yet written to disk
Writeback:         10000 kB   # Currently being written to disk
```

**Active(file)** = Clean + Dirty pages that are actively used
**Inactive(file)** = Clean + Dirty pages not recently used

### **free -h** - Simpler view:
shows ram, not memory 
```bash
free -h
```

```
              total        used        free      shared  buff/cache   available
Mem:           15Gi       3.2Gi       8.1Gi       123Mi       4.5Gi        11Gi
Swap:         2.0Gi          0B       2.0Gi
```

- **buff/cache** includes both active and inactive page cache
- **available** = what can be freed (includes inactive clean pages)

### **vmstat** - See page activity:

```bash
vmstat 1
```

```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 8388608 204800 4608000    0    0    10    50  100  200  5  2 93  0  0
```

- **buff** = Buffer cache (metadata)
- **cache** = Page cache (file content)

those are cache data that can be freed up if memory runs out and need more RAM immediately

### **Check page cache details:**

```bash
# See what files are cached:
sudo vmtouch -v /path/to/file

# See cache usage per file:
sudo pcstat /path/to/file
```
