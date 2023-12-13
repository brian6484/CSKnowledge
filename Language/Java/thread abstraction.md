## Why utilise threads in the first place?
If we have a 4 CPU core, and can code so that each core can do some useful work, program theoretically can run 4 times faster than a single core. So if we have something
like this running on a single thread on single core:

## 1st level of abstraction
```java
long sum = 0;

for (int i = 0; i < 1_000_000; i++) {
    sum += stats[i];
}
```

We can instead create 4 threads that do 
```java
long sum0 = 0;
for (int i = 0; i < 250_000; i++) {
    sum0 += stats[i];
}

long sum3 = 0;
for (int i = 750_000; i < 1_000_000; i++) {
    sum3 += stats[i];
}
```

and these 4 threads would be combined by the main program, which would start them off with *start()* and wait for them to complete with *join()* and then
computing sum = sum1+.. sum4. 

## 2nd level of abstraction
But making 4 loops is tedious and error- prone. We can use Java streams instead to **abstract** usage of threads as such:
```java
sum = Arrays.stream(stats).parallel().sum();
```

## 3rd level of abstraction
### OS thread vs Hardware thread
Before talking about 3rd abstraction, let's see this.
Java threads access *OS threads* directly but OS threads are exp to create/destroy and only a limited number of them exists. For example, if we exceed the number
of OS threads, it can cause Java app to crash so rmb not to leave them running while creating new ones.

Another big prob is that the server itself definitely has several multi-core processors than our laptop, so overall it would have more hardware threads than us.
So if program utilises as many hardware threads there are available in server, our laptops definitely wont be able to process this. So the optimal number of threads
depends on the hardware cores available.

### Thread pool with ExecutorService
ExecutorService provides an interface where you can submit tasks and get the results later. The implementation of this interface is using a pool of threads as such:

```java
ExecutorService newFixedThreadPool(int nThreads)
```
which creates n number of worker threads and stores them in a pool. These threads run submitted tasks first-come first-serve basis and are returned to the pool when
their tasks terminate.

Since we are basically restricting number of threads to a hardware-appropriate number, it is cheap to submit thousands of tasks to a thread pool while not going over
the assigned number of threads in the pool.

#### A precaution on thread pool
Thread pool is definitely better than explicitly using threads but we need to be careful

1) Thread pool with k threads can only run max k tasks concurrently

![Screenshot 2023-12-13 152616](https://github.com/brian6484/CSKnowledge/assets/56388433/a58ad9d2-3a2e-4418-b799-a2e5d9d521a4)


That is expected. Tasks will be placed in a queue until some thread is returned to the pool and is free after finishing a task. But we need to care about tasks that
**sleep or wait for I/O or network connections**. This is cuz while threads sleep or wait for I/O or network connections, they are not doing useful work but 
the tasks occupy that worker thread. So try avoiding placing tasks that do these stuff (but it is not always that easy)

2) Good practice to shut down thread pool before exiting program
Java typically waits for **all threads to complete** before returning a value from *main* to avoid killing a thread prematurely that might be doing some task. So
when exiting app, we should shut down thread pool cuz if not worker threads might wait for another task submission  so they need to be terminated explicitly.




