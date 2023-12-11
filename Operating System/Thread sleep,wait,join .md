## Thread's methods
### sleep()
causes currently executing thread to sleep for a certain time. It is normally to pause the execution of thread and the parameter is milliseconds.
While thread is sleeping, it doesnt release locks which it is holding.

```java
try {
    // Sleep for 1 second (1000 milliseconds)
    Thread.sleep(1000);
} catch (InterruptedException e) {
    e.printStackTrace();
}

```

### wait()
causes current thread to **release lock** on the specified object and *wait* until another thread wakes it up via notify() or notifyAll().
It is normally used with `synchronised`.

```java
synchronized (sharedObject) {
    try {
        // Release the lock and wait until notified
        sharedObject.wait();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}

```

### join()
allows a thread to wait for the completion of another thread. When a thread calls join() on another thread, it waits for that another thread to finish 
before continuing. It is normally used in main thread when it needs to wait for worker threads to finish their tasks.
```java
Thread workerThread = new Thread(() -> {
    // Perform some work
});

workerThread.start();

try {
    // Main thread waits for workerThread to finish
    workerThread.join();
} catch (InterruptedException e) {
    e.printStackTrace();
}

```
