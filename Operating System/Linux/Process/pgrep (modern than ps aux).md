## What it shows:

**`pgrep`** is a better alternative to using `ps aux | grep` because:

1. **More efficient** - It's purpose-built for searching processes, so it doesn't spawn an extra `grep` process
2. **Cleaner output** - Returns just the PIDs, not the grep command itself in results
3. **Faster** - Direct process search without piping

## The examples:

```bash
pgrep bash
```
but this is when u know the process name, not the pid.

- Finds all processes named "bash" and returns their PIDs (12345 and 67890 in this case)

if u know the pid and would want some details on that process ,u should still use ps.
```bash
ps -fp 12345
```
- Shows detailed information about a specific PID
- `-f` = full format (shows full command line)
- `-p` = select by PID

## Why `pgrep` > `ps | grep`:

When you do `ps aux | grep bash`, you get:
- The actual bash processes
- **Plus** the `grep bash` process itself (annoying!)

With `pgrep bash`, you only get what you actually want - no grep noise.

**Pro tip:** Use `pkill` (pgrep's sibling) to kill processes by name: `pkill firefox`
