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
