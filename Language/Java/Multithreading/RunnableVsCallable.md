## Runnable
- defines a task for thread to execute
- normally its "fire-and-forget", tasks that done need to return result to main thread

## Callable
- defines a task for thread to execute **and return result**
- it can perform task, return a value of any type, or throw a checked exception

```java
import java.util.concurrent.Callable;

public class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // This is the task that will be executed.
        int sum = 0;
        for (int i = 0; i <= 100; i++) {
            sum += i;
        }
        // We return an Integer result here.
        return sum;
    }
}
```
