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

#### Problem
Normally this kind of blocking operation is harmful. Let see this example
```java
work1();
Thread.sleep(10000); 
work2();
```

versus this
```java
public class ScheduledExecutorServiceExample {
    public static void main(String[] args) {
        ScheduledExecutorService scheduledExecutorService =
                Executors.newScheduledThreadPool(1);

        work1();

        scheduledExecutorService.schedule(
                ScheduledExecutorServiceExample::work2, 10, TimeUnit.SECONDS);

        scheduledExecutorService.shutdown();
    }

    public static void work1() {
        System.out.println("Hello from Work1!");
    }

    public static void work2() {
    
```
First code means that halfway through work1, it sleeps and blocks, occupying a whole worker thread who needs to do work2 too for 10s before
continuing with work1. But Second code executes work1 and **finishes it** AND THEN terminates after queueing work2 task to execute after 10s. So while first
occupies a thread while sleeping, the second queues another task. So instead of blocking, a task should terminate itself after submitting
a follow-up task.


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
