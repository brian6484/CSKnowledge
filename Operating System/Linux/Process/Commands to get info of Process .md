## Available Format Options

### **Common `-eo` Fields**:
```bash
ps -eo pid        # Process ID
ps -eo ppid       # Parent Process ID  
ps -eo state      # Process state (R,S,D,Z,T)
ps -eo cmd        # Command name
ps -eo args       # Full command line with arguments
ps -eo user       # User name
ps -eo rss        # Memory usage (KB)
ps -eo pcpu       # CPU percentage
ps -eo etime      # Elapsed time since process started
ps -eo nice       # Nice value
ps -eo pri        # Priority
```

### **Combine Multiple Fields**:
```bash
# Comma-separated list
ps -eo pid,user,state,pcpu,cmd

# With custom headers
ps -eo pid=PID,ppid=PARENT,state=STATE,cmd=COMMAND
```

## Practical Examples

### **Orphan Detection**:
```bash
# Method 1: Simple
ps aux | awk '$3==1 {print}'  # Hard to see PPID clearly

# Method 2: Clean
ps -eo pid,ppid,cmd | grep " 1 "  # PPID clearly visible
```

### **Zombie Detection**:
```bash
# Method 1: Traditional  
ps aux | grep defunct

# Method 2: Clean
ps -eo pid,state,cmd | grep " Z "
```

### **Memory Troubleshooting**:
```bash
# Show top memory consumers
ps -eo pid,rss,cmd --sort=-rss | head -10

# Show processes using > 100MB
ps -eo pid,rss,cmd | awk '$2 > 100000 {print}'
```

### **Process Hierarchy**:
```bash
# Show parent-child relationships clearly
ps -eo pid,ppid,cmd --forest

# Output:
# PID  PPID CMD
#   1     0 /sbin/init
# 998     1  \_ bash
#1234   998      \_ sleep 100
#1235   998      \_ grep pattern
```

## State Values Explained

```bash
ps -eo pid,state,cmd

# STATE meanings:
# R = Running or runnable
# S = Interruptible sleep (waiting)  
# D = Uninterruptible sleep (usually I/O)
# Z = Zombie (terminated but not reaped)
# T = Stopped (by signal or debugger)
# I = Idle kernel thread
```

## Interview-Ready Commands

```bash
# Find orphans
ps -eo pid,ppid,cmd | awk '$2==1 && NR>1 {print "Orphan:", $0}'

# Find zombies  
ps -eo pid,state,cmd | awk '$2=="Z" {print "Zombie:", $0}'

# Show process tree
ps -eo pid,ppid,cmd --forest

# Top CPU users
ps -eo pid,pcpu,cmd --sort=-pcpu | head -10

# Top memory users
ps -eo pid,rss,cmd --sort=-rss | head -10
```

## Summary

- **`ps aux`**: Fixed format, lots of info, good for general overview
- **`ps -eo`**: Custom format, specific info, better for targeted analysis

For process state analysis (orphans, zombies, etc.), **`ps -eo`** gives you cleaner, more focused output that's easier to parse and understand!
