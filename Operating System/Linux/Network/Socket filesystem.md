## sockfs (Socket Filesystem)
It is a virtual file system that represents **sockets as files** in Linux system. It exists only in kernel memory (not on disk) and creates inodes/FD representations for sockets so they can be treated like files. When u create socket with socket(), kerenel represents it with FD so u can use standard file operations like read or write. And its internal kernel mechanism that enables "everything is a file" unix philosophy.

### characteristics
- not really a filesystem cuz there are no files in disk but all in RAM
- not mounted anywhere: unlike /proc or /sys u cant `cd` into it. So u use FD to see that socket like ss, netstat, lsof or /proc/<pid>/<fd>/
- invisible to users: users cannot directly interact with it
- Used to implement the "everything is a file" philosophy

