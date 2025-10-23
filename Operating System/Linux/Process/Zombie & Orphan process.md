## Zombie process
When process finishes, its resources (like memory and fd, etc) are deallocated. But its entry in the process table remains cuz the parent process needs to read the child's exit status using wait() or waitpid() system call before this entry can be removed by the OS.

The process is called zombie cuz it doesnt consume CPU but occupies a PID.

here S=Z means process is zombie
```
$ ./child_script &
$ ps -l
F S   PID  PPID  ... CMD
0 Z  1234  1000  ... child_script
```

If many zombies accumulate, they can exhuast the available PIDS. The point is these zombies
are waiting to be removed from the process table by calling wait() or waitpid(). This is called waiting to be reaped.

This is unlike orphan process, where child is **still running but its parent has exited**. There is no parent to reap so 
the system (usually init or systemd) adopts the orphan, becomes its parent and eventually reaps when it exits.

## clean zombie
first we need to see where these zombie processes (Z) are
```
ps aux | grep Z
## or
ps aux | grep defunct

USER       PID  PPID  STAT  COMMAND
alice     1234  1200  Z     someprocess
```

then we see which proces is that zombie's parent
```
ps -o pid,ppid,stat,cmd -p <PPID>
```

## can u kill zombie process
no u cant kill. If i try to kill with kill or kill -9, ur sending a signal to process but since the process is already dead it doesnt act on any signals

### 2 ways
either force its parent process to call wait()/waitpid() or terminate the parent process entirely

## Fork
Almost always zombie process is caused by fork where parent calls fork() that creates child process but child runs and finishes task and **dies**. Then parent doesnt call wait(), which turns child into zombie.

```
Parent: fork() → Child created
Child: does work, then exit() → Child dies
Parent: (doesn't call wait()) → Child becomes ZOMBIE
```

clean zombie either
ask parent to reap it or kill the parent with kill.

## Orphan
When a parent process dies while its child is still running. The child becomes orphan and gets adopted by init process (PID 1). The child continues running normally but under new parentage.

### detect
so its parent will have a PID of 1 cuz it is adopted by init process. instead of doing ps aux, we do ps -eo which gets the desired commands

```
# Show processes with PPID = 1 (likely orphans)
ps -eo pid,ppid,state,cmd | grep " 1 "

# Example output:
# PID   PPID S CMD
# 1234    1  S sleep 100        ← Orphan! Parent died
# 5678    1  S grep pattern     ← Orphan! Parent crashed
# 2      1  S [kthreadd]        ← System process (not orphan)
```


another way is Process tree: pstree -p shows them directly under init.
They have normal states (S, R, D) but parent is init/systemd instead of original parent
