## Zombie process
When process exits, it leaves **an entry in process table**, which is its exit status and puts it in kernels's process table.
Until parent reads its exit status with 
```
wait() or waitpid()
```
the process is called zombie. Cuz it doesnt consume CPU but occupies a PID.

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

USER       PID  PPID  STAT  COMMAND
alice     1234  1200  Z     someprocess
```

then we see which proces is that zombie's parent
```
ps -o pid,ppid,stat,cmd -p <PPID>
```

clean zombie either
ask parent to reap it or kill the parent with kill -s
