## Intro
Coming from [CompleteableFuture example](https://github.com/brian6484/CSKnowledge/blob/main/Language/Java/CompleteableFuture%20example%20.md),
I was wondering what is the diff between that example and Spring's convenient annotation to achieve parallelism - @Async.
I looked at this useful [ref](https://velog.io/@think2wice/Spring-Async-Thread-Pool%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC-Async).

## Setting up @Async
```java
@EnableAsync
@SpringBootApplication
public class MySpringApplication {
	...
}

public class AsyncService {
	@Async
    public void asyncMethod(){
    	...
    }
}
```

But if we dont specify a Thread Executor, this simple impl uses a default thread executor called SimpleAsyncTaskExecutor. This SimpleAsyncTaskExecutor **doesnt reuse threads** but instead for every new task, creates a new thread to handle this task. While there is a limit property called **concurrencyLimit** to limit the total number of tasks, it **still doesnt reuse threads**. This is very bad practice, especially when there are many short-duration tasks.

To declare custom thread executor, look [here](https://velog.io/@think2wice/Spring-Async-Thread-Pool%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC)

## @Async works via AOP
Self-invocation (inner method in the same class) will not activate AOP, as mentioned and personally experienced painfully via [here](https://github.com/brian6484/CSKnowledge/blob/main/Backend/Spring/AOP/AOP%26Transactional%20pitfall.md).
So you need 2 classes - 1 for logic and another to call those methods. 

Another point to watch out is methods **have to be public**, as with AOP. Why? Lets look below for the answer

## @Async with AOP
<img width="385" alt="image (6)" src="https://github.com/brian6484/CSKnowledge/assets/56388433/6c6e8eb9-4403-43b4-b92d-7996f7d18432">

Spring creates a proxy object that wraps our the original bean containing the @Async method. This proxy object **intercepts** the @Async method invocation by pretending that it is the original bean. It runs the task on a separate thread.

### private method
If method is private, it is inaccessible outside the class. So proxy object cannot access this method and modify it when it is being created, defeating the purpose of AOP.

### self-invocation
If the async method calls itself directly, it **bypasses the proxy object**. So AOP is not triggered

## @Async with AOP example
look at the reference on top for the example and how to properly call without self invocation and using public methods


