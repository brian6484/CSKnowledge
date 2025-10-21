v impt that **each process has a signal queue**.

1) signal generation: something triggers this signal (hardware interrupt, another process via kill(), kerneli tself)
2) kernel sets signal as pending in process's *task_struct*
3) when process is about to return to user space (from system call, interrupt, exception, etc), kernel checks for pending signals
4) signal handler set up:

If the signal is pending and not blocked, kernel first saves the current user-space context (registers, stack pointer, etc). It also modifies
the user-space stack (cuz it has ring 0 privileges) to inject a signal handler frame. This changes the return address to point to the signal 
handler. When returning to user space, execution "lands" in the signal handler.

5) Handler execution: signal handler runs in user space (ring 3) but with this special stack frame
6) Return via sigreturn: handler calls `sigreturn()` system call, which restores the original context 
