## Redis
Redis is an in-memory, open source data structure that stores FA data in RAM (Random access Memory), which allows for fast read and 
write operations. It can be used for cache and messaging queue and etc.

![Untitled](https://github.com/brian6484/CSKnowledge/assets/56388433/fae8dc30-51b8-46c6-9faa-23757c30eda4)

## Original purpose
It was meant to be a remote dictionary server for saving and giving commonly accessed data

## Diff between Redis and memcached
Redis provides a whole lot of data structures that include Java's LinkedList and HashMap and the basic ones like String and list but memcached only provides one key-value store.

## Diff between Redis and Java's HashMap
HashMap is an in-memory key-value store just like a hashmap. If application is restarted, since both are stored in-memory, whatever is stored
will be wiped clean. But Redis has a persistence solution option like saving snapshots to disk.

In redis, commands are atomic, which is enforced by the single-threaded nature of Redis. This single-threaded nature
means that if there are multiple connections, there is an issue of consistency. To solve this Redis uses an event-driven, non blocking I/O model to handle multiple connections concurrently.


If there is a multi-threaded environment, race condition could occur, which is
a case where behaviour of program depends on the *relative* timing of events like order of execution of concurrent operations. For example if 2 threads
share a common incremental counter, and if the increment method is `not atomic`, then operations can interleave with one another, varying the output. To solve this, Redis uses locks that concurrent operations dont interfere with one another.

## Features
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

## Limitations
1) It is single thread and while it ensures Aciditiy, it limits the potential scability of computers with multiple CPU cores.
2) It cannot do complex queries that are often associated with RDB since it is a simple key-data data structure.
3) For heavy write operations, Redis is limited in I/O performance especially when using **syncrhonous persistence mechanism like
AOF**.

## Reasons for Redis than just websocket or STOMP
- When server is reset, chatroom and messages are reset
- pub/sub system works only for 1 server cuz the *topic* that is generated in that server is not visible to other servers



