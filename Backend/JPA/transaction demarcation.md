# Transaction demarcation
To execute db operations in system transaction, we have to set boundaries of that unit of work. We should decide when to start and commit the transaction, and if error
occurs, we need to roll back to leave data in *consistent state*. This is transaction demarcation. 

Optimally db transactions need to be short cuz open transactions consume resources and potentially prevent **concurrent access** due to locks.

There are optimistic and pessimistic locks of read and write types. TBC
