**Signal handler** = a function/code that defines **what a process does when it receives a signal**.

**How it works:**

```
Signal arrives at process
    ↓
Process checks: "Do I have a handler for this signal?"
    ↓
If yes: Execute the handler code
If no: Use default behavior (usually terminate)
```

**Example:**

```bash
# Default behavior (no handler)
kill -15 <PID>
→ Process terminates immediately

# With signal handler
kill -15 <PID>
→ Handler runs: "Save files, close connections, cleanup"
→ Then process terminates gracefully
```

**In code (C example):**

```c
#include <signal.h>

void my_handler(int sig) {
    printf("Received signal %d\n", sig);
    // Cleanup code here
    exit(0);
}

int main() {
    signal(SIGTERM, my_handler);  // Set up handler
    // Rest of program
}
```

When SIGTERM arrives, `my_handler()` runs instead of just terminating.

**Key point:**
- **SIGTERM** can be caught/ignored by a signal handler
- **SIGKILL** cannot—no handler can catch it, process always dies immediately

**So signal handler = "what the process does when it gets a signal"**

That's why graceful shutdown works—the process has a handler that cleans up before exiting!
