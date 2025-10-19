## Signals
tbc what are signals? They are software interrupts sent to processes, asking them to do something (like terminate, pause, etc). The process receives and responds to them.

## Diff between system call
System call is request to kernel to do something. So signal is the "what" (what message to send) and system call is the "how" (how to send it)

### SIGTERM signal 15 gracefull kill
```
User runs: kill -15 <PID>
    ↓
OS sends SIGTERM signal to process
    ↓
Process receives signal and handles it (or ignores it)
    ↓
Process terminates gracefully (or doesn't)
```
1) kill() sysstem call is invoked
2) kernel receives system call and looks up the target process
3) signal(like SIGTERM, SIGKILL,etc) is sent by kernal to that process
4) process receives and handles signal by responding (terminates, ignores, pauses, etc). It has a [signal handler]() or uses default behaviour.

### SIGKILL signal 9 force kill
Try SIGTERM (-15) first → gives process time to clean up

If unresponsive, then SIGKILL (-9) → forces termination

SIGKILL can leave orphaned/leaked files or incomplete operations
