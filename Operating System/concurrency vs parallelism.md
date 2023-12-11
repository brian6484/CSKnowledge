## But why consider concurrency in the first place?
1) hardware trend where multicore processors are becoming more prevalent so we should exploit them to make our app as eff. as possible
2) increasing usage of Internet services. For example, microservice instead of monolithic depends on increased network communication for
coordination. Also, if public APIs like Google's localisation info or Twitter's news are likely incorporated into our service. If these
external services are slow to respond, we want to show at least some partial results and not a blank page until we get a response back.
So we dont want to waste our precious clock cycles of our CPU while waiting.

![Screenshot 2023-12-11 233449](https://github.com/brian6484/CSKnowledge/assets/56388433/46a373a4-dd13-4b76-b6a9-03ce9a4677b9)


For example, if we are relying on Facebook and Twitter's API to get data, we shouldnt wait for data from Facebook before processing data
from Twitter. It can happen concurrently.


## Diff
In multi-threaded env, it either runs via concurrency or parallelism.
![Screenshot 2023-12-11 232403](https://github.com/brian6484/CSKnowledge/assets/56388433/4c68d711-580e-4bbd-9f89-9b3311031e61)

## parallelism
When threads are running in parallel, they both **run at the same time**.
```java
CPU 1: A ------------------------->

CPU 2: B ------------------------->
```

## concurrency
the threads overlap in 2 ways. Either they run in parallel like above so executing at the same time, or they interleave on the processor like
this. Concurrency can occur even on a single core, as seen in the image right above
```java
CPU 1: A -----------> B ----------> A -----------> B ---------->
```

In what order will the threads be executed concurrently depends on thread scheduling. With thread scheduling, threads take turns executing their work via run() little
by little in a very short period of time. Scheduling is [explained more here](https://github.com/brian6484/CSKnowledge/blob/main/thread%20scheduling.md)

