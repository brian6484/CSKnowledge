## What It Is
Linux automatically uses **2MB pages instead of 4KB pages** to reduce memory management overhead.

---

## Why It Exists

**Problem:** With 64GB RAM and 4KB pages = 16 million page table entries
- TLB cache misses (slow memory access)
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
- **Causes random performance spikes**

### 2. **Memory Waste**
```
App needs:    4 KB
Kernel gives: 2 MB
Wasted:       2044 KB
```

### 3. **Fork Overhead**
- Copy-on-write copies entire 2MB (not just 4KB)
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
