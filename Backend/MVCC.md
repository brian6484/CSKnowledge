# Controlling concurrent access 
## Transaction isolation
So I in ACID tells us that transactions should not be visible from other transactions but how? Back in the days, transactions used to perform isolation with locks.


## MVCC
Now transaction isolation is possible with multi-version concurrency control (MVCC), which is more scalable.

