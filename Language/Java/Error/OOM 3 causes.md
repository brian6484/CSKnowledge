I thought oom is just caused by heap space shortage but its not just that.

```
OutOfMemoryError: Java heap space
→ Too many objects on the heap
→ Fix: -Xmx (increase heap)

OutOfMemoryError: Metaspace  
→ Too many classes loaded
→ Fix: -XX:MaxMetaspaceSize (increase metaspace)

OutOfMemoryError: unable to create new native thread
→ Too many thread stacks
→ Fix: -Xss (reduce stack size per thread) or create fewer threads
```

## metaspace
Metaspace stores:
```
Class structures and metadata
Method bytecode
Constant pool (string literals, constants)
Static variable definitions (but static objects live on heap)
```
