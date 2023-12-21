# Intro
To demonstate the diff, lets call int f(int x) and int g(int x) as synchronous APIs and we are calling both APIs to calculate their sums 
like this

```java
int y = f(x);
int z = g(x);
System.out.println(y + z);
```

but if these APIs take a long time to execute or if you want to execute them in a separate CPU core, the time taken would just be the bigger
executime time of the 2 APIs.

## First way
We can execute the 2 APIs in separate threads. 
```java
class ThreadExample {
    public static void main(String[] args) throws InterruptedException {
        int x = 1337;
        Result result = new Result();

        Thread t1 = new Thread(() -> { result.left = f(x); });
        Thread t2 = new Thread(() -> { result.right = g(x); });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println(result.left + result.right);
    }

    private static class Result {
        private int left;
        private int right;
    }

    private static int f(int x) {
        // Implementation of function f
        return x * x;
    }

    private static int g(int x) {
        // Implementation of function g
        return 2 * x;
    }
}

```

But this complicates the simple code that we had just now. So we can use Future API interface of Runnable as the second way

## Second way
Note when we submit() a task, it deosnt necessarily execute immediately but it returns a *Future* object immediately that represents
the result of computation that **might not be available yet**. You then can use get() method on this *Future* object, which is similar to
Thread's join() method where it is a blocking call that blocks other threads if this result has not been computed yet. It blocks
until it is done calculating.

```java
public class ExecutorServiceExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        int x = 1337;

        ExecutorService executorService = Executors.newFixedThreadPool(2);
        Future<Integer> y = executorService.submit(() -> f(x));
        Future<Integer> z = executorService.submit(() -> g(x));

        System.out.println(y.get() + z.get());

        executorService.shutdown();
    }
}
```

But this has explicit calls to submit() method. To further improve this, we make the APIs as asynchronous via 2 ways that both change
the signatures of APIs - Future-styled API and reactive styled API

# Asynchronouse APIs 
These are simpler and more high-level than explicit thread manipulation via Runnable or Thread class 

## Future-styled API
We change the return type of the functions as Future like

```java
Future<Integer> f(int x);
Future<Integer> g(int x);
```

cuz previously, we had to submit our function as a task to executorservice to get the maybe_not_ready_yet_computed_result as Future object
but if we explicitly declare the functions themselves to return Future object, this is called Future-styled Api.

We can use it like this where Future contains a task that is evaluated within its original body and the result is returned ASAP.
```java
Future<Integer> y = f(x);
Future<Integer> z = g(x);
System.out.println(y.get() + z.get());
```

In bigger programs, it might not be appropriate because
1) other functions that use g needs to return Future as well
2) better to have more and smaller tasks (parallel like Stream)

## Reactive-styled API
We change return typed of functions as *void* like this. Even thought it doesnt return anything, notice we pass a lambada (**callback**) parameter as additional argument. The functions invoke this callback when the result is ready. The callback interface defines a method
called *onComplete* that takes result as argument.

```java
public class CallbackStyleExample {
    public static void main(String[] args) {
        int x = 1337;
        Result result = new Result();

        // Callback for f
        Callback fCallback = (int y) -> {
            result.left = y;
            System.out.println((result.left + result.right));
        };

        // Callback for g
        Callback gCallback = (int z) -> {
            result.right = z;
            System.out.println((result.left + result.right));
        };

        // Call f and g with their respective callbacks
        f(x, fCallback);
        g(x, gCallback);
    }

    // Example f method that takes a callback
    private static void f(int x, Callback callback) {
        // Simulate asynchronous task
        // In a real scenario, this could be an asynchronous operation
        int result = x * 2;

        // Call the callback with the result
        callback.onComplete(result);
    }

    // Example g method that takes a callback
    private static void g(int x, Callback callback) {
        // Simulate another asynchronous task
        int result = x / 2;

        // Call the callback with the result
        callback.onComplete(result);
    }

    // Callback interface
    interface Callback {
        void onComplete(int result);
    }

    // Result class
    static class Result {
        int left;
        int right;
    }
}
```

Problem with this style is that
1) Since there is no locking, it prints the fastest value to complete so it may print the sum twice. So this style is normally for a
*sequence of events*, not for calculating individual results, for which the latter is better dealt via Futures.




