Connection pool exhaustion doesn't directly cause OOM, but it leads to it. When the pool is exhausted, incoming requests wait for connections. These waiting threads pile up, each consuming memory for their stack and local variables. With hundreds of threads waiting, memory fills up and causes OutOfMemoryError. Similarly, too many concurrent users means more sessions, cached data, and active threads—all consuming heap memory until it's exhausted.
```
1. How Concurrent Users/Connection Pool → OOM Error
Connection Pool Exhaustion ≠ Direct OOM
Connection pool exhaustion causes:

Users can't get database connections
Requests queue up and wait
Threads pile up waiting for connections
Each thread consumes memory
Eventually → OutOfMemoryError

Flow:
1. Pool has 20 connections, all in use
2. User 21 requests connection → waits
3. User 22, 23, 24... all wait
4. 100 threads now waiting, each using memory
5. Heap fills up → OOM Error
Too Many Concurrent Users → OOM
Each user session consumes:

Session data in memory
Cached objects
Threads processing their requests
Temporary objects

1000 concurrent users × 5MB per session = 5GB memory
```
