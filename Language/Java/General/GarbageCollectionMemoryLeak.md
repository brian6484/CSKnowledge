## GC
It is an automatic memory management system of JVM that frees up space in memory of objects that are not used (referenced to be more technical).

## How GC works
To understand this, we first need to understand how objects are created. 
1. Objects are created in **heap**, where memory is allocated. They are in memory as long as they are referenced by other objects/variables.
2. JVM tracks these references and if there are no active references, JVM removes them.
3. This removal is 3 steps of mark, sweep, compact. Mark first marks objects with no active refs, sweep removes them and compact literally compacts the heap memory to make some space for this removed memory.

## Memory leak
Objects (memory) that is no longer in use is not properly dereferenced, so JVM cannot remove it, leading the application to consume more and more memory. This can lead to lower performance and even
make appliction run out of memory.

A typical example is a static field holding reference to an object. So static belongs to the class, not an instance of the class. So as long as the class is loaded by the application,
if a static variable holds a reference to an object, it is not eligbile for GC.

```java
class MemoryLeakExample {
    static SomeObject sharedObject;  // Static field

    public static void createLeak() {
        sharedObject = new SomeObject();  // Object is held by the static field
    }
}

public class Main {
    public static void main(String[] args) {
        MemoryLeakExample.createLeak();
        // Even after we're done with the object, sharedObject still holds the reference
        // The object won't be garbage collected as long as the class is loaded.
    }
}
```

A solution would be to set a static reference to null whenever it is not needed, or avoid these static references in the first place.

