## why
1) multiple access - it allows indepedendent access by multiple process to that speicifc file. Each process can have its own FD to that file.
For example if theres a log file and in program A (web server), that file is FD 3 and for file offset we set it as the end of file, we can
append new log entries. Meanwhile, program B (log analyser) has that file as FD 4 and for file offset we set it as *start of file*, then we read
the log entries from the beggining of the file.

2) 
