## Kernel
Kernel is a bridge between hardware and user applications. Without it, programs need to directly access hardware resources which can compromise
security, conflict and system stability. Kernel provides abstraction, resource management and controlled/privileged access to resources.

### Main Kernel Responsibilities:

**Process Management**
- Creates, schedules, and terminates processes
- Manages process states (running, waiting, sleeping, zombie)
- Handles context switching between processes
- Manages process memory spaces and permissions

**Memory Management**
- Allocates and deallocates memory for processes
- Implements virtual memory and paging
- Manages the memory mapping between virtual and physical addresses
- Handles memory protection and segmentation

**File System Management**
- Provides a unified interface to different file systems (ext4, NTFS, etc.)
- Manages file operations (read, write, create, delete)
- Handles file permissions and ownership
- Manages disk I/O and buffering

**Device Management**
- Controls hardware devices through device drivers
- Manages I/O operations
- Handles interrupts from hardware devices
- Provides device abstraction for applications

**Network Management**
- Implements network protocols (TCP/IP stack)
- Manages network interfaces
- Handles packet routing and filtering
- Provides socket interfaces for applications

## Init Process

**What is init and why is it important?**
Init is the first user-space process started by the kernel (PID 1). It's the **ancestor of all other processes** and is responsible for system initialization and maintaining the system during runtime.

### Main Init Responsibilities:

**System Initialization**
- Mounts file systems
- Starts essential system services
- Sets up network interfaces
- Configures system parameters

**Process Management**
- Adopts orphaned processes (becomes their parent)
- Reaps zombie processes to prevent resource leaks
- Manages system runlevels/targets
- Handles system shutdown and reboot

**Service Management** (especially with systemd)
- Starts, stops, and monitors system services
- Manages service dependencies
- Handles service failures and restarts
- Provides logging and status information

## Kernel Questions

**How does the kernel handle system calls?**

The kernel uses a system call table (syscall table) that maps system call numbers to specific kernel functions. When a system call occurs:

btw remember that register is high-speed storage space **within CPU**? It is even faster than RAM cuz RAM is physically separate from CPU
but register is within CPU so is the fastest.
1. CPU switches to kernel mode via software interrupt
2. Kernel saves the current process context (registers, stack pointer)
3. System call number is extracted from a CPU register (typically EAX on x86)
4. Kernel looks up the corresponding function in the syscall table
5. Parameters are validated and copied from user space to kernel space
6. The appropriate kernel function executes
7. Return value is placed in a register and control returns to user space

**What's the difference between kernel space and user space?**

- **Kernel Space**: Protected memory region where kernel code runs with full hardware privileges (Ring 0). Can access all memory, execute privileged instructions, and directly control hardware
- **User Space**: Restricted memory region where user applications run (Ring 3). Limited memory access, cannot execute privileged instructions, must use system calls to access kernel services
- **Protection**: Memory management unit (MMU) enforces boundaries - user space cannot directly access kernel space memory

**How does virtual memory work?**

Virtual memory creates an abstraction layer between processes and physical RAM:
- Each process gets its own virtual address space (typically 4GB on 32-bit systems)
- Virtual addresses are translated to physical addresses via page tables
- Pages can be swapped to disk when physical memory is full
- Provides memory protection - processes cannot access each other's memory
- Enables features like copy-on-write, memory mapping, and shared libraries

**What happens during a context switch?**

1. **Save current process state**: CPU registers, program counter, stack pointer saved to process control block (PCB)
2. **Update process state**: Mark current process as waiting/ready
3. **Select next process**: Scheduler chooses next process to run
4. **Load new process state**: Restore registers, memory mappings, stack pointer from new process's PCB
5. **Switch memory context**: Update page tables/MMU for new process's virtual memory
6. **Resume execution**: Jump to new process's program counter

**How does the kernel manage interrupts?**

- **Interrupt Vector Table (IVT)**: Maps interrupt numbers to handler functions
- **Hardware interrupts**: Devices signal CPU via interrupt lines (disk, network, timer)
- **Software interrupts**: System calls, exceptions (divide by zero, page fault)
- **Interrupt handling**: CPU saves context, disables interrupts, calls handler, restores context
- **Priority levels**: Higher priority interrupts can preempt lower priority ones
- **Bottom half processing**: Heavy work deferred to avoid blocking other interrupts

## Init Questions

**What's the difference between SysV init and systemd?**

**SysV Init:**
- Sequential startup - services start one after another
- Uses shell scripts in /etc/init.d/
- Runlevels (0-6) define system states
- Simple but slow boot process
- Limited dependency management

**Systemd:**
- Parallel startup - services start simultaneously when dependencies are met
- Uses unit files (.service, .target, .mount, etc.)
- Targets replace runlevels (multi-user.target, graphical.target)
- Faster boot times
- Advanced dependency management, socket activation, logging (journald)
- More complex but feature-rich

**What happens if the init process dies?**

If init (PID 1) dies, the system becomes unstable:
- No process can adopt orphaned processes
- System cannot properly shut down or restart services
- Usually triggers a kernel panic: "Attempted to kill init!"
- System typically becomes unresponsive and requires hard reboot
- This is why init must be extremely robust and handle all signals carefully

**How does init handle orphaned processes?**

When a parent process dies before its children:
1. **Orphaned children** are automatically adopted by init (PID 1)
2. **Init becomes the new parent** for these processes
3. **When orphaned processes exit**, they become zombies
4. **Init automatically reaps** these zombie processes by calling wait()
5. **Prevents resource leaks** - zombie processes consume process table entries

**What are runlevels and how do they work?**

Traditional SysV runlevels define system operational states:
- **0**: Halt/shutdown
- **1**: Single-user mode (maintenance)
- **2**: Multi-user without networking
- **3**: Multi-user with networking (text mode)
- **4**: Unused (custom)
- **5**: Multi-user with GUI
- **6**: Reboot

Systemd uses **targets** instead:
- poweroff.target (runlevel 0)
- rescue.target (runlevel 1)
- multi-user.target (runlevel 3)
- graphical.target (runlevel 5)

**How does systemd improve upon traditional init systems?**

- **Parallel startup**: Faster boot times
- **Socket activation**: Services start on-demand when connections arrive
- **Better dependency management**: Sophisticated ordering and requirement handling
- **Process supervision**: Automatic service restart on failure
- **Unified logging**: journald centralized logging system
- **Resource management**: cgroups integration for resource limits
- **Service discovery**: D-Bus integration for service communication

## Combined Questions

**Walk through what happens from power-on to login prompt:**

1. **BIOS/UEFI**: Hardware initialization, POST, bootloader location
2. **Bootloader** (GRUB): Loads kernel image into memory
3. **Kernel initialization**:
   - Memory management setup
   - Hardware detection and driver loading
   - Root filesystem mount
   - Start init process (PID 1)
4. **Init process**:
   - Parse configuration files
   - Mount additional filesystems
   - Start essential services (udev, networking, etc.)
   - Start display manager or getty processes
5. **Login prompt**: getty presents login interface

**How do processes communicate with the kernel?**

- **System calls**: Primary mechanism for requesting kernel services
- **Signals**: Kernel-to-process communication (SIGTERM, SIGKILL, etc.)
- **/proc filesystem**: Read kernel/process information
- **/sys filesystem**: Access to kernel parameters and device information
- **Device files**: /dev/ entries for hardware interaction
- **Memory mapping**: Direct memory access via mmap()
- **Shared memory**: IPC mechanism managed by kernel

**What's the relationship between init and other system processes?**

- **Init is the ultimate parent**: All processes trace ancestry back to init
- **Process hierarchy**: Forms a tree structure with init at the root
- **Orphan adoption**: Init adopts processes whose parents die
- **Service management**: Init (or systemd) controls system services
- **Signal handling**: Init can send signals to control other processes
- **Resource cleanup**: Init ensures proper cleanup of child processes
- **System state management**: Init coordinates system-wide state changes (shutdown, runlevel changes)

These answers demonstrate understanding of both theoretical concepts and practical system administration knowledge that interviewers typically look for.
- What's the relationship between init and other system processes?

Would you like me to dive deeper into any of these areas or discuss specific scenarios you might encounter in an interview?
