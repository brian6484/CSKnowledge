
# Yes! Real-World Page Management Scenarios

Here are practical troubleshooting situations where you need to work with pages:

---

## Scenario 1: High Memory Usage (Page Cache Bloat)

### Problem
```bash
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           16Gi       2.0Gi       500Mi       100Mi        13Gi        12Gi
```
buff/cache has **BOTH dirty and clean pages**!!

**13GB in buff/cache!** Is this a problem?

### Investigation
```bash
# Check if it's clean page cache (safe) or dirty pages (needs writing)
cat /proc/meminfo | grep -i dirty
# Dirty:           524288 kB  ← 512MB waiting to be written!

# See what's using page cache
sudo pcstat /var/log/* /tmp/* /home/*/*.log

# Check if we're swapping
vmstat 1 5
# si (swap in) and so (swap out) should be near 0
```

### Solutions

**Option 1: Drop clean page caches (safe)**
```bash
# Drop only page cache (doesn't affect dirty pages)
sudo sync  # Flush dirty pages first!
sudo sysctl vm.drop_caches=1

# Drop dentries and inodes too
sudo sysctl vm.drop_caches=3

# Verify
free -h
```

**Option 2: Reduce dirty page threshold**
```bash
# Current settings
sysctl vm.dirty_ratio vm.dirty_background_ratio

# Make kernel flush more aggressively
sudo sysctl vm.dirty_ratio=10          # Was 20
sudo sysctl vm.dirty_background_ratio=5 # Was 10

# Make permanent
echo "vm.dirty_ratio=10" | sudo tee -a /etc/sysctl.conf
```

---

## Scenario 2: OOM Killer Strikes

### Problem
```bash
$ dmesg | tail
[12345.678] Out of memory: Killed process 1234 (mysql) score 800
[12345.679] oom-kill:constraint=CONSTRAINT_NONE,nodemask=(null)...
```

**Your database got killed!**

### Investigation
```bash
# Check memory pressure
vmstat 1
# Look at: si, so (swapping), and free memory

# Check which processes are using most memory
ps aux --sort=-%mem | head -20

# Check OOM scores (higher = more likely to be killed)
for pid in $(pgrep -x mysql); do
  echo "PID: $pid"
  cat /proc/$pid/oom_score
done

# See page fault rates (major faults = disk I/O)
ps -o pid,comm,min_flt,maj_flt -p $(pgrof mysql)
```

### Solutions

**Option 1: Adjust OOM score**
```bash
# Protect critical process from OOM killer
echo -1000 > /proc/$(pidof mysql)/oom_score_adj
# Range: -1000 (never kill) to +1000 (kill first)

# Or in systemd service:
# OOMScoreAdjust=-1000
```

**Option 2: Add swap space**
```bash
# Create swap file
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Check it's active
swapon --show
free -h
```

**Option 3: Tune swappiness**
```bash
# Current value (default 60)
cat /proc/sys/vm/swappiness

# Lower = prefer page cache eviction over swapping
sudo sysctl vm.swappiness=10

# Make permanent
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.conf
```

---

## Scenario 3: Thrashing (Excessive Paging)

### Problem
```bash
$ vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 3  5  524288  50000  10000 100000  8000 8000  9000 9000  500 1000 10 20 40 30  0
```

**High si/so (swap in/out) + high wa (I/O wait) = THRASHING!**

### Investigation
```bash
# Confirm major page faults are high
vmstat -s | grep "pages swapped"
vmstat -s | grep "pages paged"

# Check per-process major faults
ps -eo pid,comm,maj_flt --sort=-maj_flt | head

# See what's being swapped
for pid in $(ps -eo pid --no-headers); do
  echo "$pid $(cat /proc/$pid/status 2>/dev/null | grep VmSwap)"
done | grep -v "0 kB" | sort -k2 -n
```

### Solutions

**Option 1: Identify memory hog and limit it**
```bash
# Find the culprit
ps aux --sort=-%mem | head -5

# Use cgroups to limit memory
sudo cgcreate -g memory:/myapp
echo 2G > /sys/fs/cgroup/memory/myapp/memory.limit_in_bytes
sudo cgexec -g memory:myapp /path/to/app
```

**Option 2: Disable swap temporarily**
```bash
# Turn off swap (forces OOM killer instead of thrashing)
sudo swapoff -a

# If system stabilizes, you need more RAM, not swap
```

**Option 3: Increase swappiness temporarily**
```bash
# Force more aggressive swapping (counterintuitive but can help)
sudo sysctl vm.swappiness=100
# Lets kernel swap out unused pages to make room for active ones
```

---

## Scenario 4: Transparent Huge Pages (THP) Issues

### Problem
Database performance is inconsistent, high latency spikes

### Investigation
```bash
# Check THP status
cat /sys/kernel/mm/transparent_hugepage/enabled
# [always] madvise never

# Check THP usage
grep -i huge /proc/meminfo
# AnonHugePages:   2097152 kB  ← Using 2GB of huge pages

# Check defragmentation activity
cat /sys/kernel/mm/transparent_hugepage/defrag
# [always] defer defer+madvise madvise never
```

### Solution (for databases like MongoDB, Redis)
```bash
# Disable THP (many databases recommend this)
echo never | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
echo never | sudo tee /sys/kernel/mm/transparent_hugepage/defrag

# Make permanent (add to /etc/rc.local or systemd service)
```

---

## Scenario 5: Page Allocation Failures

### Problem
```bash
$ dmesg | grep -i "page allocation"
[12345] mysqld: page allocation failure: order:4, mode:0x40d0
```

### Investigation
```bash
# Check memory fragmentation
cat /proc/buddyinfo
# Node 0, zone DMA      1    0    1    0    2    1    1    0    1    1    3
# Node 0, zone Normal  45  123   67   32   16    8    4    2    1    0    0
#                                                          ↑ No large contiguous blocks!

# Check slab usage (kernel memory)
sudo slabtop
```

### Solutions

**Option 1: Compact memory**
```bash
# Force memory compaction
echo 1 | sudo tee /proc/sys/vm/compact_memory

# Enable automatic compaction
sudo sysctl vm.compaction_proactiveness=20
```

**Option 2: Drop caches to free up memory**
```bash
sudo sync
echo 3 | sudo tee /proc/sys/vm/drop_caches
```

---

## Scenario 6: Monitoring Page Activity

### Continuous Monitoring Setup
```bash
# Watch page faults in real-time
watch -n 1 'ps -eo pid,comm,min_flt,maj_flt --sort=-maj_flt | head -20'

# Monitor memory pressure events
dmesg -w | grep -i "memory"

# Track swap usage over time
vmstat -SM 5 | awk '{print strftime("%T"), $7, $8}'

# Monitor page cache hit rate
sar -B 1 10
# pgpgin/s  = pages paged in from disk
# pgpgout/s = pages paged out to disk
# fault/s   = page faults per second
# majflt/s  = major page faults (required disk I/O)
```

---

## Quick Diagnostic Script

```bash
#!/bin/bash
echo "=== Memory Overview ==="
free -h

echo -e "\n=== Swap Activity ==="
vmstat 1 3 | tail -2

echo -e "\n=== Page Cache Stats ==="
cat /proc/meminfo | grep -E "^(Cached|Dirty|Writeback):"

echo -e "\n=== Top Memory Users ==="
ps aux --sort=-%mem | head -6

echo -e "\n=== Major Page Faults ==="
ps -eo pid,comm,maj_flt --sort=-maj_flt | head -6

echo -e "\n=== Swapped Processes ==="
for pid in $(ps -eo pid --no-headers); do
    swap=$(grep VmSwap /proc/$pid/status 2>/dev/null | awk '{print $2}')
    if [ ! -z "$swap" ] && [ "$swap" -gt 0 ]; then
        comm=$(ps -p $pid -o comm=)
        echo "$pid $comm: $swap kB"
    fi
done | sort -k3 -n -r | head -5
```

---

## TL;DR - Common Page Management Tasks

```bash
# Drop page cache (free memory)
sudo sync && echo 3 | sudo tee /proc/sys/vm/drop_caches

# Check what's using memory
vmstat 1 5
free -h
ps aux --sort=-%mem | head

# Check for thrashing
vmstat 1  # Look at si/so columns

# Protect process from OOM
echo -1000 > /proc/$(pidof critical-app)/oom_score_adj

# Reduce swapping preference
sudo sysctl vm.swappiness=10

# Check page faults
ps -eo pid,comm,min_flt,maj_flt --sort=-maj_flt
```

**Yes, page-level troubleshooting is very practical and common in production systems!**
