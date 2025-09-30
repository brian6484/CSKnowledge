## sockfs (Socket Filesystem)
its a virtual file system that represents sockets as files in Linux system. 

### characteristics
- not really a filesystem cuz there are no files in disk but all in RAM
- not mounted anywhere: unlike /proc or /sys u cant `cd` into it
- invisible to users: users cannot directly interact with it
- Used to implement the "everything is a file" philosophy

