## Before
so we saw tracing system calls is done via strace() but what about tracing function calls? So function call/routine call is when a program executes code or
logical task that is defined by programmer. It could be calculate number or fomat string. It is **internal to the process* so it runs in user space unlike system call.

theres 2 ways - either use ltrace which is easiest or perf/ftrace.

## ltrace
before we explain we need to know shared libraries (like `libc`)

### shared library
its a collection of compiled code for common functions that many programs use. OS loads these libraries *once* into memory and shares them among all 
running programs. For e.g. the most famous is libc library, which has essential functions for c or c++ programs.

## so ltrace
so when ur application code *wants to do something common*, like allocate memory or open file, it makes a call to a function in these libraries.
For example when ur code wants open file, it makes call to `fopen` function in `libc` library. ltrace sits at the boundary between ur program and this 
shared library. Since ltrace monitors library calls, it gives a high-level, more human redable actions than **low-level system calls** like `strace`.

## so wat if the function isnt in the shared library? then ltrace cant trace?
Thats the limitation. 
