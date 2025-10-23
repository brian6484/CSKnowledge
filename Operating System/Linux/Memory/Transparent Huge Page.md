## What It Is
Linux automatically uses **2MB pages instead of 4KB pages** to reduce memory management overhead.

---

## Why It Exists

**Problem:** With 64GB RAM and 4KB pages = 16 million page table entries
- [TLB](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Memory/Translation%20Lookaside%20Buffer.md) cache misses (slow memory access)
- Huge page table overhead

**Solution:** 2MB pages = 512x fewer entries = better performance

---

## How It Works

```bash
# Automatically enabled (no code changes needed)
cat /sys/kernel/mm/transparent_hugepage/enabled
# [always] madvise never
```

Kernel automatically promotes 4KB pages → 2MB pages behind the scenes.

---

## The Problems

### 1. **Latency Spikes**
- Needs 2MB contiguous memory
- Kernel defragments memory (moves pages around)
like
```
Physical RAM:
[4KB used][4KB free][4KB used][4KB free][4KB used][4KB free]...
         ↑ Fragmented! No 2MB contiguous blocks!
```
- **Causes random performance spikes**

### 2. **Memory Waste**
```
App needs:    4 KB
Kernel gives: 2 MB
Wasted:       2044 KB
```

### 3. **fork() Overhead**
- Copy-on-write copies entire 2MB (not just 4KB). fork(), making a copy, needs much more memory now. RMB while fork() allows child and parent to share the same memory space for **reads**, when either child/parent writes, then kernel allocates a new physical 4kb page and copies the original contents of 4kb page into it. So both parent and child has separate copies of that page.
- **Bad for Redis, MongoDB** (they fork for backups)

### 4. **Swap Issues**
- Must swap entire 2MB even if only 4KB used

---

## Real Impact

**MongoDB/Redis with THP:**
- ❌ Random latency spikes (100ms+)
- ❌ Unpredictable performance
- ❌ Higher CPU usage

**MongoDB/Redis with THP disabled:**
- ✓ Consistent latency
- ✓ Predictable performance

---

## Quick Commands

```bash
# Check status
cat /sys/kernel/mm/transparent_hugepage/enabled

# Check usage
grep AnonHugePages /proc/meminfo

# Disable THP (recommended for databases)
echo never | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
echo never | sudo tee /sys/kernel/mm/transparent_hugepage/defrag
```

---

## When to Use

**Enable (always):** Scientific computing, VMs, large analytics
**Disable (never):** Databases (MongoDB, Redis, Oracle), latency-sensitive apps
**Compromise (madvise):** Mixed workloads

---

## TL;DR
- **What:** 2MB pages instead of 4KB (automatic)
- **Good:** Faster for large memory workloads
- **Bad:** Causes latency spikes, wastes memory, bad for databases
- **Fix:** Disable it for databases (`echo never`)
