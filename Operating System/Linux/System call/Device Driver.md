## Device Driver
device drivers are **specialised software programs** that translate between OS and hardware devices. They
are like translators who can speak both OS and hardware lang

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Application   │    │   Application   │    │   Application   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
              ┌─────────────────────────────────┐
              │      Operating System          │
              │        (Kernel)                │
              └─────────────────────────────────┘
                                 │
              ┌─────────────────────────────────┐
              │       Device Drivers            │  ← Ring 0
              │  ┌─────┐ ┌─────┐ ┌─────┐      │
              │  │ GPU │ │ NIC │ │Disk │ ...  │
              └─────────────────────────────────┘
                                 │
              ┌─────────────────────────────────┐
              │          Hardware               │
              │  ┌─────┐ ┌─────┐ ┌─────┐      │
              │  │ GPU │ │ NIC │ │Disk │ ...  │
              └─────────────────────────────────┘
```

## role
It acts as hardware abstraction where it converts generic OS requests to device-specific commands.
It is a uniform interface for devices and hides hardware complexity from applications.

```
Application: "Save this data to file"
     ↓
OS: "Write blocks to storage device"
     ↓  
Driver: "Send SATA commands to specific SSD controller"
     ↓
Hardware: Physical write operations
```

## example
Storage drivers: Hard drives, SSDs, USB drives

Network drivers: Ethernet, WiFi, Bluetooth

Graphics drivers: GPU, display controllers

Input drivers: Keyboard, mouse, touchpad

Audio drivers: Sound cards, speakers

Printer drivers: Various printer models

## Why drivers are in ring 0 kernel mode
1) Direct hardware access is required. 
2) Hardware devices are often mapped to **specific memory address space in CPU** that correspond
to [physical hardware registers](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/System%20call/Register.md) & memory.

What does this mean?

## What "Hardware mapped to memory address space" means

Think of your computer's memory (RAM) as a giant array of mailboxes, each with a unique address. Normally, these addresses point to actual RAM where you store data.

**But here's the key**: The CPU reserves certain address ranges that don't actually point to RAM—they point directly to hardware devices instead. When you read from or write to these special addresses, you're directly controlling hardware.

**Example:**
- Let's say addresses `0x3F8` to `0x3FF` are mapped to a serial port controller
- When a driver writes the byte `0x41` (the letter 'A') to address `0x3F8`, it's not storing data in RAM
- Instead, the CPU routes that write operation directly to the serial port hardware, which then transmits 'A' over the serial cable

This is called **memory-mapped I/O**. The hardware "pretends" to be memory, making it easy for the CPU to communicate with devices using the same instructions it uses for regular memory access.

