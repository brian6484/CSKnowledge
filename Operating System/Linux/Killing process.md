## We first have to understand signal
signal - software interreupt sent to process to notify of an event. There are a few like SIGKILL (kill immediately),
SIGTERM (terminate politely), SIGSTOP (pause), SIGCHLD (child status changed).

## How kernel handles it
When signal is sent, kernel **marks that process with a signal**. This signal is delivered asyncrhonously so process might 
receive it while executing some code.

## Killing process
kill <pid> sends SIGTERM(signal 15), which is a graceful termination request, to a process. 
The process can respond in 3 ways
- catch this req and clean up resources before exiting
- ignore it (not common)
- default: kernel performs default action

If process doesnt terminate, u can kill with SIGKILL (signal 9), which is ungraceful. The process is killed 
instantly without cleanup
```
kill -9 <pid>   # immediately terminates process; cannot be caught
```
