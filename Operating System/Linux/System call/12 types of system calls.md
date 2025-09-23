## **The 12 Key System Calls**

### **1. open/openat**
```c
int open(const char *pathname, int flags, mode_t mode);
```
- Opens files/devices
- Returns file descriptor
- Flags: O_RDONLY, O_WRONLY, O_CREAT, etc.

### **2. read**
```c
ssize_t read(int fd, void *buf, size_t count);
```
- Reads data from file descriptor
- Returns bytes read (0 = EOF, -1 = error)

### **3. write**
```c
ssize_t write(int fd, const void *buf, size_t count);
```
- Writes data to file descriptor
- Returns bytes written

### **4. close**
```c
int close(int fd);
```
- Closes file descriptor
- Frees kernel resources

### **5. fork**
```c
pid_t fork(void);
```
- Creates new process (child)
- Returns 0 in child, child PID in parent

### **6. execve**
```c
int execve(const char *filename, char *const argv[], char *const envp[]);
```
- Replaces current process with new program
- Doesn't return on success

### **7. exit**
```c
void exit(int status);
```
- Terminates process
- Returns exit status to parent

### **8. wait/waitpid**
```c
pid_t wait(int *status);
pid_t waitpid(pid_t pid, int *status, int options);
```
- Parent waits for child process completion
- Prevents zombie processes

### **9. mmap**
```c
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
```
- Maps files/memory into address space
- Used for shared memory, file I/O

### **10. brk/sbrk**
```c
int brk(void *addr);
void *sbrk(intptr_t increment);
```
- Adjusts heap size
- Used by malloc() internally

### **11. socket**
```c
int socket(int domain, int type, int protocol);
```
- Creates network socket
- Returns socket file descriptor

### **12. clone**
```c
int clone(int (*fn)(void *), void *child_stack, int flags, void *arg);
```
- More flexible version of fork()
- Can create threads, containers

---

## **Common Interview Questions**

**Q: "What happens when you call read() in your program?"**
A: Library function → system call setup → software interrupt → mode switch to kernel → kernel reads data → return to userspace

**Q: "Why can't user programs access hardware directly?"**
A: CPU protection rings prevent direct hardware access from Ring 3 (user mode). Must go through kernel (Ring 0).

**Q: "What's the difference between fork() and exec()?"**
A: fork() creates copy of process, exec() replaces current process with new program

**Q: "How does malloc() work internally?"**
A: Uses brk()/sbrk() system calls to adjust heap size, or mmap() for large allocations

**Q: "What's a file descriptor?"**
A: Integer that kernel uses to identify open files/sockets in a process. 0=stdin, 1=stdout, 2=stderr

**Q: "Why are system calls expensive?"**
A: Mode switching overhead - CPU must save context, switch privilege levels, execute kernel code, restore context

---

## **Practical Examples**

### **Process Creation Pattern**
```c
pid_t pid = fork();
if (pid == 0) {
    // Child process
    execve("/bin/ls", argv, envp);
} else {
    // Parent process  
    wait(&status);
}
```

### **File I/O Pattern**
```c
int fd = open("file.txt", O_RDONLY);
char buffer[1024];
ssize_t bytes = read(fd, buffer, sizeof(buffer));
close(fd);
```

**Key Point:** Modern systems use optimizations like vDSO (virtual dynamic shared object) to speed up common system calls by avoiding mode switches when possible.
