# Ansi isolation levels
We have seen 4 transaction isolation problems [here](https://github.com/brian6484/CSKnowledge/blob/main/Backend/Transaction%20isolation%20problems.md)
so we are given 4 solutions to tackle each.

![Screenshot 2023-11-21 164243](https://github.com/brian6484/CSKnowledge/assets/56388433/55bb437c-c373-48a1-ae91-4cc3e9f8ebbf)


## read uncommited isolation
This solves **lost update**. 

A transaction may not write to a row if a *uncommitted* transaction has already written to it (exclusive write locks).

This is ALMOST never chosen because it is really unsafe. We shouldnt allow transaction's uncommitted transactions to be seen by other transactions.

## read committed isolation (JPA's default)
This solves unrepeatble reads and phantom reads.

Done via shared read locks and exclusive write locks. Read locks dont block other transactions from accessing a row but 
*uncommitted write transaction* blocks all transactions.

PC and versioning helps do this. If we retrieve same entity instance twice in same unit of work, the second lookup will be from PC, not in DB. So our read is repeatable - we won't see conflicted committed data. Versioning is also *first commit wins*.

## repeatable read isolation 
solves all but phantom read

read transactions block all write transactions but allow read transactions while write operations block all other transactions.  Since a read transaction blocks
all write transactions by acquiring lock on the data, it prevents other transactions from modifying or deleting it.

but phantom read is not solved cuz it doesnt prevent insertion of new rows that *meet the criteria* of a query.

## serialisable isolation
This is less of concurrency but transactions are fully isolated from each other so it runs 1 by 1.

This too, like read uncommitted, is not really needed.

# Transaction 
