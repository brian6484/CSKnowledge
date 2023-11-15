# Context switching
When context switch occurs, OS stops the execution of a certain thread and transfers control to another.
Threads share certain memory area like code/data/heap, which include *global variables* while the stack area is independent, which stores local variables
and function call info.

So when context switch occurs, only the thread-specific resource of stack area needs to be saved and restored in a memory space of the
incoming thread but the shared memory doesn't have to be saved or restored. For example, the global and static variables do not have to be
duplicated during a context switch, saving memory and time.
