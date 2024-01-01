## Redis
Redis is an in-memory, open source data structure that stores FA data in RAM (Random access Memory), which allows for fast read and 
write operations. It can be used for cache and messaging queue and etc.

![Untitled](https://github.com/brian6484/CSKnowledge/assets/56388433/fae8dc30-51b8-46c6-9faa-23757c30eda4)

### Features
- It is an acronym for Remote Dictionary Server, composed of key and value. It is definitely faster for reading and write than DB
because **instead of writing to physical disk, we are processing the data in memory**. But despite being **in-memory**, Redis provides
options for persistence, allowing data to be saved to disk. This ensures that data is not lost during a system restart. (to be explored in
spring redis but briefly)

2 ways of persistence
1) RDB (Redis DB), also known as snapshotting, takes a snapshot of all data in-memory at a specific time and stores it into a disk.
2) AOF (Append of File), continously logs write and read operations in Redis server to a separate file.


- It supports various data structures like string,list, hash, set, etc.

- It is single-thread, thus allowing Atomic operations, which are a property of ACID that complex operations within a transaction must all
pass or fail as a single unit of work. So race conditions almost dont happen since they depend on the **relative timing** of events.


- It supports server-side replication for increased **read** performance via **master-slave replication**. One master handles all write operations
while the slaves handle all read operations, which distribute read queries among multiple instances.

- It also supports **client-side** replication for increased **write** performance via **sharding**. Sharding is used to horziontally
partition data to distribute load among multiple instances. The application, instead of Redis server, can decide how to partition this data
via a hash function or keys.



