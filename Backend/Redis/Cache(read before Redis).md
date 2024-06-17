## Cache
Before we explore Redis, we should explore Cache. Cache is storing frequently-accessed data so that requests dont have to 
reference an API or DB. There is also Pareto's law where 80% of result is caused by 20% of an original fault. In terms of cache, you can say
20% of data in cache is responsible for 80% of cache access. 

Normally the architecture is like WS- WAS - DB but when users increase exponentially there is significant stress in DB so whilst we can scale
horizontally or vertically, we can add another additional cache layer. There is strain on DB cuz DB writes data on an actual physical disk 
so for each transaction, we have to access this disk, decreasing its performance. This is called disk I/O.


When you store data in a cache, you associate it with a **key**, which can be used later to retrieve that data. This key can be hashed via hash function and that hash value can be used for calculation to which cache server to be distributed. For example like this function hash % n (number of cache servers) determines which cache server this key is gonna go to.

### Cache hit
It is when there is a request, the server searches the cache first before DB and if there is that data, we return that data.

### Cache miss
Definition is the frequency at which data is not found in the cache, necessitating a fetch from the slower DB.
When there is a request, and when server cannot find the data in the cache and needs to look at DB, this is cache miss. 

In news feed like Insta, most users are only interested in the **latest posts** so cache miss is relatively low as frequently accessed data
is stored in cache.

## Diff between cache and CDN
cache stores result of **expensive responses** or Frequently accessed data whereas content delivery network stores **static content**.

## Caching concepts
### Look aside cache
This cache concept is **independent** of the main storage system (DB), which means cache stores an independent copy of the FA data of DB.
Whenever there is cache hit or miss, the data is stored into cache. Note we can add or remove this cache without affecting the dB

### Write cache
This is **dependent** of DB, where cache temporarily stores **all write operations** before they are written to main storage. This 
*asynchronous* operation can improve the performance of write operations, improving write latency.

While the write operations are decreased, if the DB is down when these write operations are about to be flushed to DB, then it is a big prob.







