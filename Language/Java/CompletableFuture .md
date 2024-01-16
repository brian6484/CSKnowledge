## CompletableFuture
Coming back from the box and channel model post, let's explore completeablefuture. 

The problem with Future is that it is an **interface**, so it is limited in composition (chaining multiple async tasks tgt),
error handling, lack of cancellation, etc.

So why is it called CompletableFuture? Whilst Future object is created via *Callable* and result is gotten via get(), CompletableFuture 
can create a Future object without associating it with a sepcific computation. So this allows greater complexibility cuz
we can compose and combine async operations. We can use complete() to manually complete a `CompletableFuture` with a value. CF is like
to a plain `Future` just like Stream is to a Collection.

for example:
```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture<String> completableFuture = new CompletableFuture<>();

        // Simulate asynchronous computation
        new Thread(() -> {
            // Perform some computation
            String result = "Hello, CompletableFuture!";
            
            // Complete the CompletableFuture with the result
            completableFuture.complete(result);
        }).start();

        // Get the result when ready
        try {
            String result = completableFuture.get();
            System.out.println(result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Here, CF is created without immediately associating it with a specific computation (unlike a Future object). The computation is
done in a separate thread and `complete()` method provides the result. The main thread waits and gets this result via get().

## CF > Future
With CF's thenCombine(), which is 

```java
CompletableFuture<V> thenCombine(CompletableFuture<U> other,
 BiFunction<T, U, V> fn)
```
result type T and U are taken as parameters and a new result of type V is created. When the first 2 completes, the results are
taken and this fn function is applied to both results and *completes* the resulting future **without blocking**.

Like in this example, y and z are just variable names in a lambda expression. Results of CF a and b are taken and then combined together.

```java
public class CFCombine {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        int x = 1337;
        
        CompletableFuture<Integer> a = new CompletableFuture<>();
        CompletableFuture<Integer> b = new CompletableFuture<>();
        CompletableFuture<Integer> c = a.thenCombine(b, (y, z) -> y + z);
        
        executorService.submit(() -> a.complete(f(x)));
        executorService.submit(() -> b.complete(g(x)));
        
        System.out.println(c.get());
        
        executorService.shutdown();
    }

    private static int f(int x) {
        // Some computation
        return x * 2;
    }

    private static int g(int x) {
        // Some computation
        return x + 5;
    }
}
```

This thenCombine is really good cuz the third computation (c) doesnt run on a thread until the 2 computations of have completed, **rather
than starting to execute early and block**. So no wait operation is performed.

But if you have somethjing like
```java
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        int x = 1337;
        CompletableFuture<Integer> b = new CompletableFuture<>();
        
        executorService.submit(() -> b.complete(g(x)));
        
        int aResult = f(x);
        System.out.println(aResult + b.get());
        
        executorService.shutdown();
    }
```
You have 2 problems:
1) This blocks the main thread until result b is available with get() if computing g(x) takes too long.
2) Do you remember that if we shutdown executorservice while there is still a task that is not completed yet, it can be fucked?
Its essential that **all submitted tasks should be completed before shutting down executorservice**. Always remember of the consequence
when you shut executorservice prematurely.
