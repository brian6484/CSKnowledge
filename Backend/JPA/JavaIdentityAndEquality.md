# Identity and Equality and Java
## Object identity (==)
2 references are the same if they point to the same memory location in JVM.

## Object equality
Equivalence differs from identity. It means whether 2 diff instances have the same value - same state.
We use equals() method to determine this. The properties are symmetry, reflexivity and transivity.

## Database identity
Persistence instance is an in-memory representation of a row/rows of a db table. They are the same if they share the same
table and PK value.

