Processes that are **not attached to a terminal** are known as **daemon processes** (or background services).

### What They Are
A daemon is a computer program that runs continuously in the background, rather than under the direct control of a user. It detaches from the terminal (or controlling session) because its job is to perform system-wide tasks, handle requests, or wait for events without requiring any interactive input from a user on a keyboard. 

### Key Characteristics
* **No Controlling Terminal (TTY/PTY):** They run without a connection to a display or command-line interface. This is why the `x` flag in `ps aux` is used—to include processes not associated with any terminal.
* **System Services:** Daemons typically handle essential, ongoing operating system functions. Examples include:
    * **Web servers** (like Apache or Nginx) listening for incoming connections.
    * **Mail servers** waiting to send or receive email.
    * **Logging services** (like `syslogd`) recording system events.
    * **Scheduling services** (like `cron`) running scheduled jobs.
* **Parent Process ID (PPID):** A well-behaved daemon is usually reparented to the **init process** (or `systemd` in modern Linux systems, which typically has **PID 1**) after it starts up and detaches from the terminal. This ensures that the daemon keeps running even after the original terminal session or shell is closed.
Oh so that makes sense cuz daemon runs in the background and doesnt get reaped when parent process like bash dies

Because the daemon's parent is now the robust, ever-running PID 1, it ensures two things:

1) he daemon is not terminated when the user logs out or the original Bash shell dies.

2) If the daemon itself crashes, PID 1 is available to properly reap its status, preventing it from turning into a zombie process.

