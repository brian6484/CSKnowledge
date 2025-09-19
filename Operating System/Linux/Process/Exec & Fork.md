## Context
Actually every terminal command like (ls), the bash is actually making a copy of itself to execute this ls command. This is cuz **Multiple processes can EXIST at the same time, but only ONE can RUN at a time (per CPU core)**!!.

So when we run ls
1) bash process: exists in memory BUT is sleeping/waiting (not running)
2) ls process: exists in memory and is actively running

## Fork
it creates a new process by duplicating the current process. The original becomes the parent and the new one becomes the child.

Returns PID of child to parent, 0 to child, -1 on error
Child gets copy of parent's memory space (copy-on-write optimization)
Both processes continue execution from the fork point

## Exec
replaces the current process image with new program.

execve(), execl(), execp() etc.
Replaces memory image but keeps same PID
Commonly used after fork to run different programs

Typical pattern: fork() → exec() to create new processes running different programs.

## The Detailed Timeline with Fork/Exec:

1. **Bash running** → you type `ls`

2. **Bash calls fork()** 
   - Creates identical copy of bash process
   - Now you have: Original bash + Copy of bash (both in memory)

3. **In the copy: exec() happens**
   - The copy replaces itself with `ls` program
   - Now you have: Original bash + ls process

4. **Original bash goes to sleep** (calls wait())
   - Bash exists but isn't using CPU
   - ls is now the one using CPU

5. **ls finishes and exits**
   - ls process disappears from memory
   - This wakes up the sleeping bash

6. **Bash wakes up** and gives you prompt again

## The Key Moments:

**Fork moment**: 
- Before: 1 bash process
- After: 2 identical bash processes

**Exec moment**: 
- Before: Original bash + Copy of bash  
- After: Original bash + ls process

**Wait moment**:
- Original bash stops running (goes to sleep)
- Only ls is running now

## Why Both Are Needed:

**Fork**: Creates the copy that can be safely replaced
**Exec**: Replaces the copy with the ls program  
**Wait**: Original bash sleeps until ls finishes

Without fork: bash would exec and replace itself → bash gone forever
Without exec: you'd just have two bash processes, no ls
Without wait: bash would keep running while ls runs (background job)

So fork+exec+wait is the complete pattern for running a command and getting your shell back!
