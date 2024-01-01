
## Cache
Before we explore Redis, we should explore Cache. Cache is storing frequently-accessed data so that requests dont have to 
reference an API or DB. There is also Pareto's law where 80% of result is caused by 20% of an original fault. In terms of cache, you can say
20% of data is responsible for 80% of cache access. 

Normally the architecture is liek WS- WAS - DB but when users increase exponentially there is significant stress in DB so whilst we can scale
horizontally or vertically, we can add another additional cache layer. There is strain on DB cuz DB writes data on an actual physical disk 
so for each transaction, we have to access this disk, decreasing its performance. This is called disk I/O.

### Cache hit
It is when there is a request, the server searches the cache first before DB and if there is that data, we return that data.

### Cache miss
When there is a request, and when server cannot find the data in the cache and needs to look at DB, this is cache miss.

## Caching concepts
### Look aside cache
This cache concept is **independent** of the main storage system (DB), which means cache stores an independent copy of the FA data of DB.
Whenever there is cache hit or miss, the data is stored into cache. Note we can add or remove this cache without affecting the dB

### Write cache
This is **dependent** of DB, where cache temporarily stores **all write operations** before they are written to main storage. This 
*asynchronous* operation can improve the performance of write operations, improving write latency.

While the write operations are decreased, if the DB is down when these write operations are about to be flushed to DB, then it is a big prob.







