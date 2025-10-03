# General tips
<img width="1182" height="766" alt="Image" src="https://github.com/user-attachments/assets/3eb1197a-cabd-4360-a209-de09b002ae7e" />

for the book, 1.11 is so useful for case study

1) Check logs
```
journalctl -xe
or
/var/log/*
```
check system or app logs.

For example, it shows log erros like port already in use or missing file

-xe means show recent erros and -u specifies the unit/service
```
journalctl -u nginx.service -xe

bind() to 0.0.0.0:80 failed (98: Address already in use)
```

2) Verify permission and file existence
If binary or config files dont exist, or if user doesnt have permission
```
ls -l /usr/bin/myapp
ls -l /etc/myapp/config.yml
```
u should check if binary and config exist and
binary file: it should be executable
config: should be readable by app's user

3) check dependency
native binary, which is executable file to run directly on ur PC CPU and OS **without needing interpreter or VM**, often depends on shared libaries (.so files). And `ldd` shows wat libraries ur program needs

```
ldd /usr/bin/myapp
libssl.so.1.1 => not found
libcrypto.so.1.1 => /lib/x86_64-linux-gnu/libcrypto.so.1.1
```

4) check env variables and configs
Some apps need env variables set like JAVA_HOME, PATH, DB_URL, etc. So u can check cur env

```
env | grep JAVA
```

but if my app uses a separate config file like yml file, wrong settings can cause startup failure

5) use strace to see system calls
if app fails mysteriously, we should run `strace`, which shows **every system call (open files, network, memory)**.

```
strace -f -o trace.log /usr/bin/myapp

error:
open("/etc/myapp/config.yml", O_RDONLY) = -1 ENOENT (No such file or directory)
```

6) check resource limit (ulimit -a)
Sometimes app doesnt start cuz of system resource limit (too many open files, not enuff memory)

```
ulimit -a

output:
open files (-n) 1024
max user processes (-u) 4096
```
So max user process is capped at 4k but if ur app needs more, hitting this limit causes server to fail. U can increase it like
```
ulimit -n 65535

```
# Redis Memory Fragmentation and Thrashing - Case Study

## 1. Problem: System Instability & High Swapping

### Symptoms
- Intermittent spikes in application latency
- High, sustained swap activity on server
- Redis cache slowdown
- Low CPU load (ruling out CPU bottleneck)

### Initial Hypothesis
Server running out of memory → kernel swapping pages frequently → **thrashing**

### Key Metrics

| Metric | Observation |
|--------|-------------|
| **System RAM** | Very low (< 1 GB) |
| **Swap Used** | > 1 GB (exceeds physical RAM) |
| **vmstat Swap Rate** | High, continuous si (79 KB/s) and so (70 KB/s) |

---

## 2. Diagnosis: Root Cause Analysis 🔍

Active swapping confirmed memory shortage, but needed to identify the source.

### Investigation Steps

| Tool/Command | Observation | Conclusion |
|--------------|-------------|------------|
| `top` / `ps aux` | `redis-server` consuming largest RSS memory | Redis is the memory pressure source |
| `redis-cli INFO memory` | `used_memory_rss` (5.88 MB) >> `used_memory` (1.48 MB) | **NOT a large dataset problem** <br> **Memory fragmentation issue** |
| `mem_fragmentation_ratio` | **4.03** | **ROOT CAUSE** <br> Redis holding ~4x more physical RAM than dataset requires <br> Memory allocator cannot efficiently reuse/return fragmented blocks |
| `maxmemory` | **0** (No limit set) | **Secondary problem** <br> Redis can fragment indefinitely <br> Guarantees eventual thrashing |

### Key Insight
```
mem_fragmentation_ratio = used_memory_rss / used_memory
4.03 = 5.88 MB / 1.48 MB

Normal ratio: 1.0 - 1.5
Critical ratio: > 1.5 (action needed)
```

---

## 3. Solution & Verification ✅

### Phase 1: Immediate Recovery (Stop Fragmentation)

**Action:**
```bash
# Graceful restart of Redis service
systemctl restart redis
# OR
redis-cli SHUTDOWN SAVE
redis-server /etc/redis/redis.conf
```

**Verification:**
```bash
redis-cli INFO memory
```

**Results:**
- `mem_fragmentation_ratio`: 4.03 → 1.05 (normal)
- `used_memory_rss`: Dropped significantly (reclaimed several MB)
- `vmstat` si/so rates: Returned to 0 immediately
- System stability restored

---

### Phase 2: Long-Term Prevention

#### 1. Set Memory Limits and Eviction Policy

**Edit `/etc/redis/redis.conf`:**
```bash
maxmemory 500mb
maxmemory-policy allkeys-lru
```

**Explanation:**
- `maxmemory`: Sets hard memory limit (prevents unbounded growth)
- `maxmemory-policy allkeys-lru`: Evicts least recently used keys when limit reached

**Other policy options:**
- `volatile-lru`: Evict keys with TTL set
- `allkeys-lfu`: Least frequently used
- `volatile-ttl`: Evict keys with shortest TTL
- `noeviction`: Return errors when memory full

#### 2. Enable Active Defragmentation

**Add to `redis.conf`:**
```bash
activedefrag yes
```

**Optional tuning parameters:**
```bash
# Minimum percentage of fragmentation to start active defrag
active-defrag-threshold-lower 10

# Maximum percentage of fragmentation at which we use maximum effort
active-defrag-threshold-upper 100

# Minimal effort for defrag in CPU percentage
active-defrag-cycle-min 1

# Maximal effort for defrag in CPU percentage
active-defrag-cycle-max 25
```

**Note:** Active defrag runs during idle CPU time, minimal performance impact

#### 3. Vertical Scaling (Infrastructure)

**Recommendation:**
- Upgrade to larger instance (e.g., 4GB RAM minimum)
- Provides buffer for future growth and fragmentation
- Critical services should not run on severely undersized VMs

**Calculation:**
```
Recommended RAM = (expected_dataset_size × 1.5) + system_overhead
                = (500 MB × 1.5) + 1 GB
                = ~1.75 - 2 GB minimum
```

---

## Key Commands for Monitoring

### Check Memory Fragmentation
```bash
redis-cli INFO memory | grep -E 'used_memory:|used_memory_rss:|mem_fragmentation_ratio:'
```

### Monitor Swap Activity
```bash
vmstat 1 5              # Watch si/so columns
free -h                 # Overall memory usage
cat /proc/meminfo       # Detailed memory info
```

### Check Redis Process Memory
```bash
ps aux | grep redis
top -p $(pgrep redis-server)
```

### Verify Configuration
```bash
redis-cli CONFIG GET maxmemory
redis-cli CONFIG GET maxmemory-policy
redis-cli CONFIG GET activedefrag
```

---

## Prevention Checklist

- [ ] Set `maxmemory` to safe limit (70-80% of available RAM)
- [ ] Configure appropriate `maxmemory-policy`
- [ ] Enable `activedefrag yes`
- [ ] Monitor `mem_fragmentation_ratio` regularly
- [ ] Disable swap for Redis OR reduce swappiness to 1
- [ ] Ensure adequate system RAM (2-4x expected dataset size)
- [ ] Set up alerts for fragmentation ratio > 1.5
- [ ] Schedule periodic Redis restarts if fragmentation persists

---

## Lessons Learned

1. **Low CPU + High Swap = Memory Issue**: Classic thrashing pattern
2. **Check Fragmentation First**: High RSS vs low used_memory indicates fragmentation, not data size
3. **Restart is Quick Fix**: Defragments immediately but doesn't prevent recurrence
4. **Set Limits**: `maxmemory=0` is dangerous in production
5. **Right-size Infrastructure**: Critical services need adequate resources
6. **Monitor Proactively**: Alert on fragmentation ratio before it becomes critical

---

## Related Issues to Watch

- OOM killer events: `dmesg | grep -i oom`
- Slow queries causing memory spikes: `redis-cli SLOWLOG GET 10`
- Key eviction patterns: `redis-cli INFO stats | grep evicted`
- Connection limits: `redis-cli INFO clients`
