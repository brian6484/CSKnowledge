# Diff
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

