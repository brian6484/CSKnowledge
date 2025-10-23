1) BIOS/UEFI initialisation

when u press power button, PC's BIOS (Basic input/output system) or a modern version like UEFI (Unified Extensible Firmware
Interface) is the first software to run. It performs **POST (Power-on self-test)** to check essential hardware like cpu,memory
is working correctly. It also scans bootable devices (like Hard drive, SSD) for **bootloader**

2) Bootloader
Bootloader is small computer program that is executed by system's firmware. Firmware is software embedded directly into a piece of hardware to provide **low-level control** for the device. Its like **software for hardware** like a microchip.

Bootloader's task is to load OS kernel, which controls all programs and allocates and manages memory, into memory. It
passes boot parameters like root filesystem, init process, etc

3) kernel initialisation

Kernel image is decompressed and sets up core system components like memory, interrupts, device drivers that are needed for hardware
to interact with. It also mounts root filesystem (read-only)

4) init/systemd

Starts user-space processes and services and brings system to a usable state.

5) login prompt

provides login interface to user. If system is configured for CLI, it presents **tty(teletype)** login prompt. If graphical GUI,
system displays GDM(Gnome display manager) or LightDM.
