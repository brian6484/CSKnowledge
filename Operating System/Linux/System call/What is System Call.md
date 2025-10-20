## System call
It is interface between kernel and user programs. Its a *controlled* way for user-space programs to request kernel services and is the **only way** for programs to access i/o (read(), write()), hardware, files(open(), close()), network, process control(fork(), exec())
from user space. 

A system call is a request a user program makes to the operating system kernel for a service. A mode switch is the technical process of changing the CPU's privilege level (from User Mode to Kernel Mode) to handle that request.

In simple terms, a system call is the "why"—the reason for the request—while a mode switch is the "how"—the physical mechanism the CPU uses to fulfill it.

## CPU Protection ring
Protection ring is hardware-enforced security mechanism built into the CPU that control what the code can do based on its privilege level. Its like airport security clearance level
for software. It prevents programs from corrupting kernel memory or resources.

### The Ring Structure
```
┌─────────────────────────────────────┐
│           Ring 0 (Kernel)           │  ← Highest Privilege
│  ┌───────────────────────────────┐  │
│  │        Ring 1 (Unused)        │  │
│  │  ┌─────────────────────────┐  │  │
│  │  │     Ring 2 (Unused)     │  │  │
│  │  │  ┌─────────────────┐    │  │  │
│  │  │  │ Ring 3 (User)   │    │  │  │  ← Lowest Privilege
│  │  │  │                 │    │  │  │
│  │  │  └─────────────────┘    │  │  │
│  │  └─────────────────────────┘  │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

### ring privilege levels
#### Ring 0 (Kernel Mode/Supervisor Mode)
It is highest privilege level where OS kernel and [device drivers](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/System%20call/Device%20Driver.md) run. They execute CPU instructions, access memory locations, control hardware, execute I/O executions, etc.

## Ring 1 and 2
They are raraely used in modern OS where it was originally designed for device drivers or system services. But most OSes use 0 or 3.

## Ring 3 (User mode)
It is **lowest privilege level** where user applications and programs run. They are restricted from executing privileged CPU isntructions, direct hardware access or basically anything that Ring 0 can do. It **MUST USE SYSTEM CALLS** to request kernel services.

## the way it works
So CPU tracks current instruction's privilege level in a reigster and **[every instruction is checked against current ring](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/System%20call/Register.md)**. If it attempts to do a privileged operation from a wrong ring, it will cause *CPU exception/fault*.

### User to Kernel (Ring 3 to Ring 0) 🛡️
This is the process of escalating privileges from a normal user application to the highly privileged operating system kernel. This is only allowed through a few, tightly controlled methods to prevent applications from gaining unauthorized access and potentially harming the system.

System Calls (Software Interrupts): This is the most common and deliberate way. A user program needs a kernel service, such as reading or writing a file. It doesn't do this itself; instead, it executes a special instruction that triggers a software interrupt. This interrupt tells the CPU to stop executing the user code and jump to a pre-defined kernel routine (function in kernel to execute some task) to handle the request.

Hardware Interrupts (Timer, Keyboard, etc.): These are triggered by hardware devices. For example, when you press a key on the keyboard, it sends an interrupt signal to the CPU. The CPU then stops what it's doing (even if it's running user code) and switches to kernel mode to execute a routine that handles the keyboard input.

CPU Exceptions (Page Faults, Divide by Zero): These are unexpected events caused by a program's behavior. If a program tries to access a memory address it doesn't have permission for (a page fault) or divides a number by zero, the CPU hardware automatically triggers an exception. The CPU then switches to kernel mode to execute a special error-handling routine.

### Kernel to User (Ring 0 to Ring 3) 🔽
This is the process of de-escalating privileges, returning control from the kernel back to a user application.

When Kernel Finishes Handling a Request: After the kernel has completed a system call (like reading a file) or handled an interrupt, it uses a special instruction to return control to the user program. The CPU then switches its privilege level back to Ring 3.

This special instruction:
The **instruction** is a specific CPU command that switches the processor back from kernel mode (Ring 0) to user mode (Ring 3) and resumes executing the user program where it left off.

## Common examples on different architectures:

**On x86/x64 CPUs:**
- `IRET` (Interrupt Return) - for hardware interrupts
- `SYSRET` - specifically for returning from system calls (faster, modern method)
- `IRETQ` - 64-bit version of IRET

**On ARM CPUs:**
- `ERET` (Exception Return)

**On RISC-V:**
- `SRET` (Supervisor Return) or `MRET` (Machine Return)

## What these instructions actually do:

When executed, they perform several critical actions **atomically** (all at once):

1. **Restore the privilege level** - Switch CPU back to Ring 3
2. **Restore the instruction pointer** - Tell CPU where to continue in user code
3. **Restore the stack pointer** - Switch back to the user's stack
4. **Restore CPU flags/status** - Return the CPU state to what it was before

Think of it like this: When the system call happened, the kernel "saved" the entire state of your program (like pausing a video game and saving your progress). The return instruction "loads" that saved state and resumes exactly where you left off.

**Example flow:**
```
User code: result = read(file, buffer, 100);
           ↓ (triggers interrupt, saves state)
Kernel:    sys_read() { ... }
           ↓ (executes SYSRET with saved state)
User code: // continues here with result
```

The instruction is the CPU-level command that makes that final jump back to user mode possible.

Process Scheduling (Context Switches): The operating system kernel is responsible for managing multiple running programs. When it's time to switch from one program to another, the kernel saves the state of the current process and loads the state of the next one. This switch involves the kernel, which means a temporary privilege escalation to Ring 0. The kernel then returns to the new user process, dropping the privilege level back to Ring 3.

## Real-World Example
What happens when you run cat file.txt:

Ring 3: cat program starts in user mode

Ring 3: Program calls open("file.txt")

Ring switch: System call triggers Ring 3 → Ring 0

Ring 0: Kernel validates request, opens file

Ring switch: Kernel returns to Ring 3 → Ring 0

Ring 3: Program gets file descriptor

Repeat for read() and write() calls

## Interview q
Q: "Why can't user programs disable interrupts?"
A: Disabling interrupts is a privileged instruction only available in Ring 0. If user programs could do this, they could freeze the entire system.

Q: "What happens if a Ring 3 program tries to execute a privileged instruction?"
A: CPU generates a protection fault/general protection exception, typically killing the program.

Q: "How does the kernel protect itself from user programs?"
A: Protection rings ensure user programs (Ring 3) cannot execute privileged instructions or access kernel memory (Ring 0 protected).

Q: "What's the performance cost of protection rings?"
A: Ring switches (mode switches) have overhead due to context saving/restoring, but this is essential for system security and stability.

## Ring -1 (minus one) Hypervisor
The hypervisor is software that creates and manages virtual machines (VMs). It sits below the operating system and has ultimate control over the hardware. The OS thinks its in Ring 0 but it is actually running in ring 0 **inside a virtualised env**. Hypervisor intercepts and controls what this guest 0S does.
