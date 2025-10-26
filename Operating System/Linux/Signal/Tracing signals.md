## About
Signal tracing = monitor & debug signals the process is sending/receiving.

### strace = trace system calls AND signals
```
strace -p <pid>
```

Output shows:
- What system calls the process makes
- What signals it receives
- When signals arrive

**Example output:**
```
--- SIGCHLD (Child exited) ---
wait4(12345, [{WIFEXITED(s) and WSTATUS=0}], 0, NULL) = 12345
```
so process received SIGCHLD and child 12345 exited.

real life example
```
# Trace the SERVER process to see how IT handles signals
strace -p <server_PID> -e signal
```

Output:
```
kill(12345, SIGTERM)            = 0
--- SIGTERM (Terminated) ---
+++ killed by SIGTERM +++
```

### ltrace = trace library calls AND signals
It shows library functions calls like printf() and malloc()
```
ltrace -e signal ./my_program
```

## usage of for strace & ltrace
1) process is stuck and udk why - strace
```
strace -p <PID>
```

2) find zombie process
```
ps aux | grep defunct
# Shows zombie processes

# Find parent
strace -e trace=wait -p <parent_PID>
# Shows if parent is handling SIGCHLD
```

3) debug signal handler
```
void handler(int sig) {
    printf("Got signal %d\n", sig);
}
signal(SIGUSR1, handler);

strace -e signal ./my_program &
kill -SIGUSR1 <PID>
# See in strace output: --- SIGUSR1 ---
# See: printf output
```

## real example
If server isnt responding to **signals**
```
# Trace the SERVER process to see how IT handles signals
strace -p <server_PID> -e signal

## then in another terminal, run kill - 15 <pid> then u can see the tracing 
```

Output of this shows if signal got delivered or did server handle it via signal handler? And why didnt the process die(maybe handler 
ignored it).
