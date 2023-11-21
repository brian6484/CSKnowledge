# ACID
## Atomicity
All operations in a transaction should execute as 1 unit of work (1 atom if you will). 

## Consistency
Transactions should allow multiple users to work (read,update,etc) concurrently on same piece of data without compromising the consistency of data.
Consistency is moving from 1 valid state to another valid state by DB. Valid state means the predefined integrity constraints of db like unique key constraints.
For example, if email column of user has unique constraint and this particular transaction tries to violate this constraint, it should be rolled back with error.

## Isolation
Each transaction should not be visible to other transactions that are concurrently running

## Durable
Changes made in transaction should be persisted to db - even in system failure or power outage
