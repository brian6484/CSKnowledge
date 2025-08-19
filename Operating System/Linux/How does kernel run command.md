## kernel-level breakdown of what happens when you run ls -l
1) shell read this command and looks for the `ls` executable in directories listed in $Path
2) execve() system call -> kernel creates new process for `ls`
3) system calls/kernel interaction
4) return to user space -> kernel returns the requested data to the command and this command formats output and writes
to stdout, which the shell displays
