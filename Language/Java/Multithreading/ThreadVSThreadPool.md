# Thread in Java
## What is thread?
Thread is the smallest unit of execution within a process, representing a sequence of instructions that can be scheduled to be 
executed by the OS. In Java, the main thread is started by executing the main() method. In multi-threading, even when the main thread is terminated, if there is another thread running, app does not terminate.

## How to create threads
Btw if not set explicit names via setName(), the name is like Thread-n where n is number.

### Runnable interface
We can extend the runnable interface to create a thread and *override the run() method*. An instance of this class that extended the runnable interface
can be passed as parameter to the **thread constructor()** and then start() is invoked to execute the thread. This thread can run **separately from the main thread**.

```java
class MyRunnable implements Runnable {
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println("Runnable Thread: " + i);
            try {
                Thread.sleep(500);  // Simulate some work being done
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class RunnableExample {
    public static void main(String[] args) {
        // Create an instance of the class implementing Runnable
        MyRunnable myRunnable = new MyRunnable();

        // Create a Thread using the Runnable instance
        Thread myThread = new Thread(myRunnable);

        // Start the thread
        myThread.start();

        // Main thread continues its work
        for (int i = 1; i <= 5; i++) {
            System.out.println("Main Thread: " + i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

```

or the lambda way
```java
// Using lambda expression to create a Runnable
Runnable myRunnable = () -> {
    for (int i = 1; i <= 5; i++) {
        System.out.println("Runnable Thread: " + i);
        try {
            Thread.sleep(500);  // Simulate some work being done
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
};

// Create a Thread using the Runnable lambda expression
Thread myThread = new Thread(myRunnable);

// Start the thread
myThread.start();
```

### Thread class
We can *extend* Java's Thread class with inheritance instead of implementing a Runnable interface. By extending Thread class and overriding the run() method,
threads run concurrently

```java
class MyThread extends Thread {
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println("Thread Class Thread: " + i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class ThreadClassExample {
    public static void main(String[] args) {
        // Create an instance of the class extending Thread
        MyThread myThread = new MyThread();

        // Start the thread
        myThread.start();

        // Main thread continues its work
        for (int i = 1; i <= 5; i++) {
            System.out.println("Main Thread: " + i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

```

### Executor framework
We can use java.util.concurrent's executor to manage threads and is a more higher-level solution than previous solutions. We can have features like thread pooling and scheduling.

```java
Executor executor = Executors.newFixedThreadPool(5);
executor.execute(new MyRunnable());

```

### Thread pool
While creating individual thread objects gives u some fine-grained control, it can be inefficient and resource-intensive, especially when
we have many small concurrent tasks to run. This is when thread pool comes in.

Thread pool is a *managed* collection of worker threads that are kept alive to execute submitted tasks. So instead of creating threads to 
run your tasks, we instead submit the tasks to this thread and **the pool assigns an available thread to do this task** (task queuing). This reduces the overhead that comes with thread creation and destruction cuz thread pool **manages the lifecycles of threads** for us automatically. There is also improved responsiveness as tasks execute sooner because they dont have to wait for a new thread to be
made by the OS.

### Callable interface and Future interface (and how they work with Thread Pools)
Callable is similar to Runnable interface but it can **return a result and throw exceptions**.
Future interface can give a result of asynchronous computation and provides a *reference* to that result when the computation is complete, and can retrieve that result via get() method.

![Screenshot 2024-01-01 233051](https://github.com/brian6484/CSKnowledge/assets/56388433/445b3408-a3f3-40dc-a2ac-f0e4b545e032)

If the doSomeLongComputation takes longer than 1s, it blocks my thread until the result is made available by ExecutorService.

![Screenshot 2024-01-01 233100](https://github.com/brian6484/CSKnowledge/assets/56388433/2be05208-7c36-4941-9b5f-73cbf0865e77)

So in simple words, a thread pool (represented by ExecutorService) is where you execute Callable tasks, and the Future object is how you retrieve the result of those Callable tasks from the pool.

#### A deeper explanation of below example
We first *submit* a Callable instance to the *ExecutorService* using submit() method, which returns a `Future` object returning the computed value. This *submit*
is a method to submit a task for execution **asynchronously** and can be represented via either a `Runnable` or `Callable` object.

Then, while the `Callable` instance is concurrently being executed in a separate thread (lets call it a worker thread), the main thread can continue its own work.

Now this future.get() method is important. It checks if the Callable has finished computing and if not, get() will actually **block the current running thread** 
(if it is main thread, it blocks main thread. If current running thread is worker thread 5, it blocks that thread 5) until this result is ready. Exceptions for this
get() method needs to be handled accordingly.

Example
```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

class MyCallable implements Callable<Integer> {
    public Integer call() throws Exception {
        int result = 0;
        for (int i = 1; i <= 5; i++) {
            System.out.println("Callable Thread: " + i);
            result += i;
            Thread.sleep(500);  // Simulate some work being done
        }
        return result;
    }
}

public class CallableExample {
    public static void main(String[] args) {
        // Create an instance of the class implementing Callable
        MyCallable myCallable = new MyCallable();

        // Create an ExecutorService to manage thread execution
        ExecutorService executorService = Executors.newSingleThreadExecutor();

        try {
            // Submit the Callable to the ExecutorService and get a Future object
            Future<Integer> future = executorService.submit(myCallable);

            // Main thread continues its work
            for (int i = 1; i <= 5; i++) {
                System.out.println("Main Thread: " + i);
                Thread.sleep(500);
            }

            // Retrieve the result from the Future (blocks until the result is available)
            Integer result = future.get();
            System.out.println("Callable Result: " + result);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Shutdown the ExecutorService
            executorService.shutdown();
        }
    }
}

```


