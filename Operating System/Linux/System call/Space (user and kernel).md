## Space
So we talked about [rings](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/System%20call/What%20is%20System%20Call.md)
and space and ring is same way to have computer's execution env into diff privilege levels.

Ring - specific security model used by x86 processor architector to define these privelege levels
Space - logical name given to these env where kernal space is ring 0 and user space is ring 3.

So, when someone talks about a program running in User Space, they are referring to the same thing as a program running in Ring 3—an environment with restricted access. Similarly, when they talk about Kernel Space, they mean the same environment as Ring 0, which has full control over the system.

### Diagram
```
┌─────────────────────────────────────────────────────┐
│                 USERSPACE                           │
│            (Ring 3 - User Mode)                     │
│                                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ │
│  │   Your      │  │   Web       │  │   Text      │ │
│  │   Programs  │  │   Browser   │  │   Editor    │ │
│  │             │  │             │  │             │ │
│  └─────────────┘  └─────────────┘  └─────────────┘ │
│                                                     │
│          Applications, Libraries, User Code         │
└─────────────────────────────────────────────────────┘
                           │
                    System Calls
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│                KERNEL SPACE                         │
│             (Ring 0 - Kernel Mode)                  │
│                                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ │
│  │   Memory    │  │   Device    │  │   File      │ │
│  │   Manager   │  │   Drivers   │  │   System    │ │
│  │             │  │             │  │             │ │
│  └─────────────┘  └─────────────┘  └─────────────┘ │
│                                                     │
│         Operating System Kernel, Core Services      │
└─────────────────────────────────────────────────────┘
                           │
                    Hardware Access
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│                   HARDWARE                          │
│              CPU, RAM, Disk, Network                │
└─────────────────────────────────────────────────────┘
```

## **Userspace (User Mode - Ring 3)**

### **What Lives Here**
- **Your applications:** Web browsers, games, text editors
- **User programs:** Scripts, compiled programs
- **Libraries:** Shared libraries like libc
- **Runtime environments:** Python interpreter, JVM

### **What Userspace CAN Do**
```c
// These work in userspace:
int x = 42;                    // Regular variables
malloc(1000);                  // Allocate memory (through library)
printf("Hello World");         // Print to screen (through library)
fopen("file.txt", "r");        // Open file (through library)
```

### **What Userspace CANNOT Do**
```c
// These would CRASH your program if attempted directly:
outb(0x80, 0x42);             // Write to hardware port - NOPE!
asm("cli");                   // Disable interrupts - NOPE!
*(char*)0xE0000000 = 0x12;    // Access hardware registers - NOPE!
```

### **Key Characteristics**
- **Restricted privileges** (Ring 3)
- **Virtual memory** - sees virtual addresses, not physical
- **Protected** - cannot crash the system
- **Isolated** - programs cannot interfere with each other directly
- **Must ask kernel** for hardware access via system calls

---

## **Kernel Space (Kernel Mode - Ring 0)**

### **What Lives Here**
- **Operating system kernel:** Core OS code
- **Device drivers:** Hardware control software
- **System services:** Memory management, process scheduling
- **Interrupt handlers:** Handle hardware interrupts

### **What Kernel Space CAN Do**
```c
// Kernel can do EVERYTHING:
outb(0x80, 0x42);             // Direct hardware access ✓
asm("cli");                   // Disable interrupts ✓  
*(volatile uint32_t*)0xE0000000 = cmd; // Hardware registers ✓
kmalloc(size, GFP_KERNEL);    // Allocate physical memory ✓
```

### **Key Characteristics**
- **Full privileges** (Ring 0)
- **Physical memory access** - sees real RAM addresses
- **Hardware control** - direct device access
- **System-wide impact** - bugs can crash entire system
- **Handles system calls** from userspace

---

## **The Bridge: System Calls**

### **How Userspace Talks to Kernel Space**

When userspace needs kernel services, it makes a **system call**:

```c
// Userspace program:
int main() {
    int fd = open("file.txt", O_RDONLY);  // System call!
    //       ↑
    //   This looks like a function call, but...
}
```

**What actually happens:**
1. **Library function** (`open()` in libc) is called
2. Library **sets up system call** (syscall number, parameters)  
3. **Software interrupt** (INT 0x80 or SYSCALL instruction)
4. **CPU switches** from Ring 3 → Ring 0
5. **Kernel system call handler** executes
6. Kernel does the **actual work** (access hardware, open file)
7. **Results returned** to userspace
8. **CPU switches** back Ring 0 → Ring 3

---

## **Memory Layout Example**

### **Virtual Memory Map (What a Process Sees)**
```
High Memory (0xFFFFFFFF)
┌─────────────────────────────────────────┐
│              KERNEL SPACE               │ ← Only kernel can access
│         (Not visible to user)           │
├─────────────────────────────────────────┤ ← 0xC0000000 (typical)
│                                         │
│              USER SPACE                 │
│                                         │
│  Stack (grows down)         ↓           │
│                                         │
│                                         │
│                            ↑            │
│  Heap (grows up)                        │
│                                         │
│  Data Segment                           │
│  Text Segment (program code)            │
└─────────────────────────────────────────┘
Low Memory (0x00000000)
```

---

## **Real-World Examples**

### **Example 1: Reading a File**

#### **Userspace Part:**
```c
// Your program (userspace)
#include <stdio.h>
int main() {
    FILE *f = fopen("data.txt", "r");  // Userspace library call
    char buffer[100];
    fread(buffer, 1, 100, f);          // Another library call
    fclose(f);
    return 0;
}
```

#### **What Happens Behind the Scenes:**
1. `fopen()` → **system call** `open()` → **kernel space**
2. **Kernel** checks file permissions, opens file descriptor
3. `fread()` → **system call** `read()` → **kernel space**  
4. **Kernel** accesses disk hardware, reads data
5. **Kernel** copies data to userspace buffer
6. `fclose()` → **system call** `close()` → **kernel space**
7. **Kernel** releases file descriptor

### **Example 2: Network Communication**

#### **Userspace:**
```c
// Web browser (userspace)
int sock = socket(AF_INET, SOCK_STREAM, 0);    // System call
connect(sock, &server_addr, sizeof(addr));     // System call  
send(sock, "GET / HTTP/1.1", 14, 0);          // System call
recv(sock, buffer, sizeof(buffer), 0);        // System call
```

#### **Kernel Space:**
- **Network driver** handles actual hardware
- **TCP/IP stack** manages protocols
- **Socket layer** handles connections
- **Hardware interrupts** when packets arrive

---

## **Why This Separation Exists**

### **1. Security**
```c
// Malicious userspace program CANNOT:
delete_all_files_on_disk();      // No direct hardware access
crash_other_programs();          // Memory protection
disable_antivirus();             // Cannot modify kernel
```

### **2. Stability**
```c
// If your program crashes:
int *p = NULL;
*p = 42;  // Segmentation fault - kills YOUR program only
          // Kernel and other programs continue running
```

### **3. Resource Management**
```c
// Kernel controls who gets what:
// - CPU time (process scheduling)
// - Memory allocation  
// - File access permissions
// - Network bandwidth
```

### **4. Hardware Abstraction**
```c
// Userspace sees simple interface:
write(fd, data, size);

// Kernel handles complexity:
// - Which disk driver to use
// - File system format (ext4, NTFS, etc.)
// - Hardware-specific commands
// - Error handling and recovery
```

---

## **Context Switching**

### **When CPU Switches Between Spaces**

```
Time →
User → Kernel → User → Kernel → User
 App    Syscall  App   Interrupt App
```

**Userspace → Kernel Space:**
- System calls (your program requests service)
- Hardware interrupts (keyboard, network, timer)
- Exceptions (page faults, divide by zero)

**Kernel Space → Userspace:**
- System call completion
- Process scheduling (switch to different program)
- Interrupt handling complete

---

## **Performance Implications**

### **Context Switch Overhead**
```
User Mode Operation:    ~1 nanosecond
System Call:           ~300 nanoseconds  (300x slower!)
```

**Why system calls are "expensive":**
- Save user program state
- Switch privilege levels (Ring 3 → Ring 0)
- Execute kernel code
- Switch back (Ring 0 → Ring 3)  
- Restore user program state

---

## **Interview Questions**

**Q: "What's the difference between userspace and kernel space?"**
A: Userspace (Ring 3) is where applications run with restricted privileges, while kernel space (Ring 0) is where the OS runs with full hardware access. Userspace must use system calls to request kernel services.

**Q: "Why can't user programs access hardware directly?"**
A: Security and stability. Direct hardware access could crash the system or compromise other programs. The kernel mediates all hardware access.

**Q: "What happens during a system call?"**
A: CPU switches from user mode (Ring 3) to kernel mode (Ring 0), kernel executes the requested operation, then returns control to userspace with results.

**Q: "Can kernel space access userspace memory?"**
A: Yes, kernel can access all memory, but it must be careful to validate userspace pointers and handle page faults when copying data between spaces.

Validate pointers: A user program could pass a pointer to a location in memory(RAM) that it shouldn't be accessing. The kernel must check that this pointer is valid and points to a location within the program's own memory.

Handle page faults: When the kernel tries to copy data, the memory might not be in physical RAM at that exact moment (it might be in swap space on the hard drive). The kernel's memory manager handles this by triggering a page fault, which brings the required data from disk into RAM before the copy operation continues.

**Q: "Why are system calls slower than regular function calls?"**
A: System calls require context switching between privilege levels, saving/restoring state, and kernel execution overhead - much more expensive than jumping to a function in the same address space.

**Bottom Line:** Userspace = **restricted applications**, Kernel Space = **privileged OS core**. The separation provides security, stability, and controlled resource access!
