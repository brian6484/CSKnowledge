## FD
its a non-negative number that is like a *ticket number* that ur program use to refer to an **open file** (not closed). When process opens file, kernel
gives this FD number. This number is **local to the process** meaning multiple processes can have the same FD number that points to an open file. The file descriptor (fd) is a temporary identifier that only exists after a process calls the open() system call and is destroyed when the process calls close(). It represents a process's active connection to that file.

it also stores the process's read position of that file cuz **processes require their own read/write positions to 1 same file**[explained here]()
