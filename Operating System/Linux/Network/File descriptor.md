## FD
its a non-negative number that is like a *ticket number* that ur program use to refer to an open file. When process opens file, kernel
gives this FD number. This number is **local to the process** meaning multiple processes can have the same FD number that points to an open file.
