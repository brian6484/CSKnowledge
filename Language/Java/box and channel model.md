## Intro into why CompletableFuture is more app. in large-scale systems than Future
### Box and channel model
![Screenshot 2023-12-28 132703](https://github.com/brian6484/CSKnowledge/assets/56388433/4ab71e67-71da-4692-978e-d81a212340b3)

So function p requires x parameter and we pass result of p to q1 and q2. This q1 and q2 are then passed as parameters to r.

#### Without exploiting hardware parallelism
```java
int t = p(x);
System.out.println( r(q1(t), q2(t)) );
```

But calling r will call q1 and q2 in order (like process q1 and then q2) so this is not parallelism

#### in parallel with Futures
```java
int t = p(x);
Future<Integer> a1 = executorService.submit(() -> q1(t));
Future<Integer> a2 = executorService.submit(() -> q2(t));
System.out.println( r(a1.get(),a2.get()));
```

This evaluates q1 and q2 in parallel with Futures, something which we have seen before. Note we didnt wrap p and r cuz of the **shape of box and channel model**.
p has to be done before everything else and r after everything else.

### Problem
This solution works well if total amount of concurrency in system is small. But what if it is large, with many box and channel models, and some boxes having 
internal boxes within themselves? If we assign each with Future.get() to complete, many tasks will wait and this is the oppositie of what parallelism is intended to do.

### Solution
CompleteableFuture!
