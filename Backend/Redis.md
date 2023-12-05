# Redis
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

