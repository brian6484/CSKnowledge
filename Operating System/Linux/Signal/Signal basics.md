## Signals
tbc what are signals? They are software interrupts sent to processes, asking them to do something (like terminate, pause, etc). The process receives and responds to them. It can interrupt system calls and while sys call is synchronous, signal is asynchronous.

## Diff between system call
System call is request to kernel to do something. So signal is the "what" (what message to send) and system call is the "how" (how to send it)

### SIGTERM signal 15 gracefull kill
Process can catch and handle this signal, clean up before exiting.
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
Try SIGTERM (-15) first → gives process time to clean up cuz this is Force kill and **cannot be caught**. 

If unresponsive, then SIGKILL (-9) → forces termination.

SIGKILL can leave orphaned/leaked files or incomplete operations

### SIGSTOP signal 19
It pauses/suspends process and **cannot be caught**. Usually used with SIGCONT
```
kill -STOP pid
```

### SIGCONT signal 18
It resumes/continues a stopped process.
```
kill -CONT pid
```

### SIGCHLD signal 17
Its a signal by kernel to indciate that the child process has done its work and has exited. It tells the parent to clean it up or else it becomes zombie 
```
Parent process spawns child process
    ↓
Child does work
    ↓
Child exits/terminates
    ↓
Kernel sends SIGCHLD to parent
    ↓
Parent should wait for child and clean up
```

### SIGSEGV signal 11
Its for [segmentatioon fault](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Signal/SegmentationFault.md) where memory access violation is occrurred. The default is to terminate the process 

### SIGINT signal 2
Its for interrupt signal (ctrl +c) and is sent when user presses ctrl + c. Default is to terminate process.

### SIGUSR1 & SIGUSR2 signal 10, 12
Its user defined custom signals. Its for application-specific use and we can make the logic ourselves. Default behaviour is to terminate.

## Signals that cannot be caught - SIGKILL 9 & SIGSTOP 19
Process cannot catch it means **processes cannot set up a signal handler for that signal**. It is the kernel that force the default
action no matter what.

## 2 ways for process to handle signal
1) SIGN_IGN = ignore the signal
```
signal(SIGTERM, SIG_IGN);  // Ignore SIGTERM
```
When process receives SIGTERM, signal is ignored completely.

2) SIG_DFL = use default handler
```
signal(SIGTERM, SIG_DFL);  // Use default handler
```
So when process receives SIGTERM, default action happens (usually terminate) 
```
// No handler set (default)
kill -15 <PID>  → Process terminates

// SIG_DFL set
signal(SIGTERM, SIG_DFL);
kill -15 <PID>  → Process terminates (same)

// SIG_IGN set
signal(SIGTERM, SIG_IGN);
kill -15 <PID>  → Process ignores, keeps running

// Custom handler
void my_handler(int sig) { printf("Caught!\n"); }
signal(SIGTERM, my_handler);
kill -15 <PID>  → my_handler() runs, prints "Caught!"
```
