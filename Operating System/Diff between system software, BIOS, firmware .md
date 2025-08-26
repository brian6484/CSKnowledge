## diagram 
```
   +---------------------------+
   |   Application Software    |
   | (Word, Browser, Games…)   |
   +---------------------------+
               ↑
   +---------------------------+
   |      System Software       |
   |      (Operating System)    |
   | Windows, Linux, macOS…    |
   +---------------------------+
               ↑
   +---------------------------+
   |           BIOS/UEFI       |
   |  Initializes hardware &   |
   |  loads bootloader/OS      |
   +---------------------------+
               ↑
   +---------------------------+
   |          Firmware         |
   |  Embedded in devices like |
   |  keyboard, router, HDD…   |
   |  Provides low-level control|
   +---------------------------+
               ↑
   +---------------------------+
   |          Hardware         |
   | CPU, Memory, Disk, GPU…   |
   +---------------------------+
```

## flow
1) power on, hardware powerd up
2) firmware -> provides low-level control to hardware
3) BIOS/UEFI -> POST checks hardware, then loads OS bootloader
4) System Software (OS) -> manages hardware resources & provides platform for apps
5) application runs *on top of OS*

## SS
Its an interface that links hardware with user applications. It can not only be OS but device drivers and utilities (antivirus
software) could be considered as SS.

## BIOS
Its special type of firmware on motherboard that initialises hardware during boot process. I explained more [here](https://github.com/brian6484/CSKnowledge/blob/main/Operating%20System/Linux/Boot%20process.md)

## Firmware
Its software permananently programmed into hardware devices that provides low-level control for that device. It bridges hardware
and software and diff between BIOS is that firmware is for any electronic device, not just computer but BIOS is for **booting PCs**.

