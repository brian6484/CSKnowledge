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

## Future-styled API





