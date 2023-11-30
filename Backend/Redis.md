# Redis
## Diff between Redis and Java's HashMap
HashMap is an in-memory key-value store just like a hashmap. If application is restarted, since both are stored in-memory, whatever is stored 
will be wiped clean. But Redis ahs a persistence solution option like saving snapshots to disk.

Also if there are multiple servers, there is an issue of consistency or if there is a multi-threaded environment, race condition could occur, which is 
a case where behaviour of program depends on the *relative* timing of events like order of execution of concurrent operations. For example if 2 threads
share a common incremental counter, and if the increment method is `not atomic`, then operations can interleave with one another, varying the output.

Redis can solve this problem cuz it is single-threaded and atomic, which means its operation is executed as one single indivisible unit. This atomicity 
prevents race condition by preventing other threads from interfering.

Redis also supports transactions and ensures synchronisation of read and write operations in transactions.

