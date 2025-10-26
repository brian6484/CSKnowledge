## pid 1 process
In Linux, the **PID 1 process** is the **`init` process** (or its modern equivalent, like `systemd` on most distributions). It is the **first user-space process** started by the kernel during the boot process. Its **primary roles** are:

1. **System initialization:**
   It sets up the user-space environment after the kernel has finished its initialization — such as mounting filesystems, starting background services, and bringing the system to the desired runlevel or target.

2. **Process management:**
   It acts as the **ancestor (parent)** of all other user-space processes. Any process whose parent terminates without calling `wait()` becomes an **orphan**, and `init` (PID 1) **adopts and reaps** those orphaned child processes to prevent **zombie processes**.

3. **System shutdown and reboot:**
   It handles clean system shutdown, stopping services in the correct order, and ensuring a consistent system state before power-off or reboot.

---

**Example concise version (for interview):**

> “PID 1 in Linux is the `init` process — the first user-space process started by the kernel. It initializes the system, starts essential services, becomes the parent of all other processes, and reaps orphaned processes to prevent zombies.”

