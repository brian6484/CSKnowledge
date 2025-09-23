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
3) 
