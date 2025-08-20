## Killing process
kill <pid> sends SIGTERM(signal 15), which is a graceful termination request, to a process. 
The process can
- catch this req and clean up resources before exiting
- ignore it (not common)

If process doesnt terminate, u can kill with SIGKILL (signal 9), which is ungraceful. The process is killed 
instantly without cleanup
```
kill -9 <pid>   # immediately terminates process; cannot be caught
```
