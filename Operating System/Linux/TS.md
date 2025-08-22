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


# Finding & Terminating Process Writing to Log File

## Problem
- Process continuously writing to `/var/log/bad.log`
- Filling up disk space
- Need to terminate without deleting log file

## Solution Steps

### 1. Verify Issue
```bash
tail -f /var/log/bad.log          # Monitor active writing
ls -lh /var/log/bad.log           # Check file size
```

### 2. Find the Process (3 Methods)

**Method A: lsof (most reliable)**
lsof lists files that are open and allows us to see **which process is using which resources**
```bash
sudo lsof /var/log/bad.log
```

**Method B: fuser**
```bash
sudo fuser -v /var/log/bad.log
```

**Method C: Check file descriptors**
```bash
sudo ls -la /proc/*/fd/* 2>/dev/null | grep /var/log/bad.log
```

### 3. Get Process Details
```bash
ps aux | grep <PID>
ps -p <PID> -f
```

### 4. Terminate Process
```bash
sudo kill <PID>              # Graceful termination
sudo kill -9 <PID>           # Force kill if needed
```

### 5. Verify Solution
```bash
ls -lh /var/log/bad.log       # Check size
sleep 60 && ls -lh /var/log/bad.log  # Check again after 1 min
/home/admin/agent/check.sh    # Run verification script
```

## Alternative Search Methods
- `ps aux | grep -E "(python|bash|sh|perl)"` - Find scripts
- `ps aux | grep -i bad` - Search by keyword
- `journalctl -f | grep -i bad` - Check system logs

## Key Commands to Remember
- **lsof** = List Open Files (best for finding file writers)
- **fuser** = Show processes using files
- **kill vs kill -9** = Graceful vs force termination

# Find IP Address with Most Requests

## Problem
- Web server access log at `/home/admin/access.log`
- Each line = one HTTP request
- IP address at beginning of each line
- Find IP with most requests
- Write result to `/home/admin/highestip.txt`

## Solution Steps

### 1. Examine the Log File Structure
```bash
head -5 /home/admin/access.log    # See first 5 lines
wc -l /home/admin/access.log      # Count total lines
```

### 2. Extract and Count IP Addresses

**Method A: Using awk + sort + uniq**
so awk is text processing tool that **works on fields & records**, where field is the space-separated part of each line and
record is each line of input
```bash
awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -nr | head -1
```

**Method B: Using cut + sort + uniq**
```bash
cut -d' ' -f1 /home/admin/access.log | sort | uniq -c | sort -nr | head -1
```

### 3. Extract Only the IP Address
```bash
# Get just the IP (remove count)
awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -nr | head -1 | awk '{print $2}'
```

### 4. Write to Output File
```bash
# Complete one-liner solution
awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -nr | head -1 | awk '{print $2}' > /home/admin/highestip.txt
```

### 5. Verify Solution
```bash
cat /home/admin/highestip.txt
sha1sum /home/admin/highestip.txt    # Should match: 6ef426c40652babc0d081d438b9f353709008e93
```

## Command Breakdown

| Command | Purpose |
|---------|---------|
| `awk '{print $1}'` | Extract first field (IP address) |
| `sort` | Sort IPs alphabetically |
| `uniq -c` | Count unique occurrences |
| `sort -nr` | Sort by count (numeric, reverse) |
| `head -1` | Get top result |
| `awk '{print $2}'` | Extract IP from "count IP" format |

## Alternative Approaches

**Using grep + sort (if IPs follow standard format)**
```bash
grep -oE '^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' /home/admin/access.log | sort | uniq -c | sort -nr | head -1 | awk '{print $2}'
```

**Step by step debugging**
```bash
# Step 1: Extract IPs
awk '{print $1}' /home/admin/access.log | head -10

# Step 2: Sort and count
awk '{print $1}' /home/admin/access.log | sort | uniq -c | head -10

# Step 3: Sort by frequency
awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -nr | head -5
```

## Key Concepts
- **Field extraction**: `awk '{print $1}'` or `cut -d' ' -f1`
- **Counting duplicates**: `sort | uniq -c`
- **Sorting by frequency**: `sort -nr` (numeric reverse)
- **Pipeline chaining**: Combine commands with `|`
