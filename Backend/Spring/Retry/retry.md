## Spring Retry
I have never seen this functionality but it is used to retry a certain function multiple times and you can set parameters like
`value` that makes it retry if this exception is met, `exclude` that stops this triggering mechanism when a certain exception is met and
`backoff` parameter that sets delay time for retrying.

## very useful ref
https://velog.io/@backtony/Spring-Retry
https://github.com/spring-projects/spring-retry
^ that is the official github doc

## @Retryable
```java
@Slf4j
@Service
public class RetryService {

    private int count;

    @Retryable(
            value = RuntimeException.class, // retries if this particular exception is met
            maxAttempts = 3, // 3회 시도
            backoff = @Backoff(delay = 2000) // 재시도 시 2초 후 시도 (in milliseconds)
    )
    public int retrySuccess(){
        count++;
        if (count % 2 == 0){
            return count;
        }
        throw new RuntimeException("runtime exception");
    }
}
```

## @Recover
```java
@Slf4j
@Service
public class RetryService {

    @Retryable(
            value = RuntimeException.class, // retries if this particular exception is met
            maxAttempts = 3, // 3회 시도
            backoff = @Backoff(delay = 2000) // 재시도 시 2초 후 시도
    )
    public int retryFail(String name){
        throw new RuntimeException("runtime exception");
    }

    // 1) return type MUST  be the same as @Retryable method
    // 2) first parameter MUST be the exception, second parameter must be same as @Retryable's parameter
    @Recover
    public int recover(RuntimeException e, String name) {
        log.info("{}", name);
        return -1;
    }
}
```

Even after the retries by that @Retryable method have been exhausted and they stil all fail,
you can deal with @Recover method. But the rules are that
1) return type MUST be the same as that @Retryable method
2) the first parameter must be the exception of the @Retryable method. This is cuz when that method fails with that exception, this method
with @Recover will catch this exception (kinda like GlobalExceptionHandler). And the **second parameter** must be the same as @Retryable's
parameter.

## RetryTemplate
for a better and cleaner impl, you can use RetryTemplate. More on that later
