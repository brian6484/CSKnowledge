# Clean Pages vs Active Pages in Linux Memory Management
## Page Basics

First, understand that Linux divides memory into **pages** (typically 4KB chunks). The kernel tracks different types of pages based on their state and usage.

---
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
# When you run a program, its binary is loaded into RAM
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

---

## Dirty Pages

Before explaining active pages, understand **dirty pages** (opposite of clean):

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

---

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

1. **Page is first accessed** → Goes to **inactive list**
2. **Page is accessed again while inactive** → Promoted to **active list**
3. **Active page not accessed for a while** → Demoted to inactive list
4. **Under memory pressure** → Inactive pages evicted first

### Active vs Inactive:

| Active | Inactive |
|--------|----------|
| Recently used | Not recently used |
| Protected from eviction | First to be evicted |
| "Hot" pages | "Cold" pages |
| Higher priority | Lower priority |

---

## Viewing These in Linux

### **/proc/meminfo** - See memory breakdown:

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

### **Check page cache details:**

```bash
# See what files are cached:
sudo vmtouch -v /path/to/file

# See cache usage per file:
sudo pcstat /path/to/file
```

---

## Practical Scenarios

### **Scenario 1: Reading a large file**

```bash
# First read - loads file into page cache as INACTIVE pages
cat large_file.txt > /dev/null

# Second read - pages accessed again, promoted to ACTIVE
cat large_file.txt > /dev/null

# Check memory:
free -h
# You'll see buff/cache increased
```

Pages start as **inactive** and **clean**. If accessed again, become **active**.

### **Scenario 2: Editing a file**

```bash
# Read file - creates clean, inactive pages
vim myfile.txt

# Modify and save - pages become DIRTY
# Pages also likely promoted to ACTIVE (being edited)

# Eventually kernel flushes to disk - pages become CLEAN again
# But remain ACTIVE if still being accessed
```

### **Scenario 3: Memory pressure**

```bash
# System low on memory
# Kernel's page reclaim algorithm:

1. First evict: Inactive, clean pages (instant)
2. Then evict: Inactive, dirty pages (must flush first)
3. Last resort: Active pages (demote to inactive first)
4. Finally: Swap out anonymous pages if needed
```

---

## Page States Summary

```
┌─────────────────────────────────────┐
│           All Pages                 │
└─────────┬───────────────────────────┘
          │
    ┌─────┴─────┐
    ↓           ↓
┌─────────┐  ┌──────────┐
│ Active  │  │ Inactive │
└────┬────┘  └─────┬────┘
     │             │
  ┌──┴──┐       ┌──┴──┐
  ↓     ↓       ↓     ↓
Clean Dirty   Clean Dirty
```

### All Combinations:

1. **Active + Clean** = Frequently accessed, unmodified pages (e.g., often-used program code)
2. **Active + Dirty** = Frequently accessed, modified pages (e.g., active database buffers)
3. **Inactive + Clean** = Not recently used, unmodified (e.g., old cached files) - **First to evict**
4. **Inactive + Dirty** = Not recently used, modified (e.g., old log file writes) - Must flush before evict

---

## Monitoring Page Activity

### **Check what's using page cache:**

```bash
# Top processes using cache:
sudo pcstat $(find /proc/*/exe 2>/dev/null)

# See cache pressure:
grep -E 'Active|Inactive|Dirty' /proc/meminfo
```

### **Force page cache clearing (for testing):**

```bash
# Drop clean page cache:
sudo sync; echo 1 > /proc/sys/vm/drop_caches

# Drop dentries and inodes:
sudo sync; echo 2 > /proc/sys/vm/drop_caches

# Drop everything:
sudo sync; echo 3 > /proc/sys/vm/drop_caches
```

**Warning:** Only use for testing! Linux manages cache efficiently; manual clearing usually hurts performance.

---

## Key Takeaways

| Term | Meaning | Can Evict? |
|------|---------|------------|
| **Clean** | Matches disk | Yes, instantly |
| **Dirty** | Modified, not synced | Must flush first |
| **Active** | Recently/frequently used | Last to evict |
| **Inactive** | Not recently used | First to evict |

**Best case for eviction:** Inactive + Clean (no I/O needed)
**Worst case for eviction:** Active + Dirty (must flush and still recently used)

Does this clarify clean and active pages? Let me know if you want more details on any aspect!
