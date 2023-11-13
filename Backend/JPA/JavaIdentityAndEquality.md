# Identity and Equality and Java
## Object identity (==)
2 references are the same if they point to the same memory location in JVM.

## Object equality
Equivalence differs from identity. It means whether 2 diff instances have the same value - same state.
We use equals() method to determine this. The properties are symmetry, reflexivity and transivity.

## Database identity
Persistence instance is an in-memory representation of a row/rows of a db table. They are the same if they share the same
table and PK value.

## How PC ensures object equality guarantee
It enables repeatable reads of entity instance and guarantees object-identity equality.

For example the 3 equalities are guaranteed
```java
Item itemA = em.find(Item.class, ITEM_ID);
Item itemB = em.find(Item.class, ITEM_ID);

assertTrue(itemA == itemB);
assertTrue(itemA.equals(itemB));
assertTrue(itemA.getId().equals(itemB.getId()));
```
