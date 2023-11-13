# Identity and Equality and Java
## Object identity (==)
2 references are the same if they point to the same memory location in JVM.

## Object equality (equals)
Equivalence differs from identity. It means whether 2 diff instances have the same value - same state.
We use equals() method to determine this. The properties are symmetry, reflexivity and transivity.

### Internal logic of equals()
All java classes inherit equals() method of Object. This equals() method by Object class uses object identity (==) 
whether 2 ref. refer to same in-memory instance on the Java heap. So this method has to be overriden to truely compare the object *equality*.

## Database identity (same pk value)
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

## JVM
When we get the same PK value, the 2 references point to the same in-memory instance on the JVM heap.
If instance is in detached state and is made persistent again, the reference is no longer the same cuz the new
reference points to a different in-memory instance on the JVM heap. But the db identity and object eqaulity is still the same.
For example

```java
em = emf.createEntityManager();
em.getTransaction().begin();
Item a = em.find(Item.class, ITEM_ID);
Item b = em.find(Item.class, ITEM_ID);
assertTrue(a == b);
assertTrue(a.equals(b));
assertEquals(a.getId(), b.getId());
em.getTransaction().commit();
em.close();

em = emf.createEntityManager();
em.getTransaction().begin();
Item c = em.find(Item.class, ITEM_ID);
assertTrue(a != c);
assertFalse(a.equals(c));
assertEquals(a.getId(), c.getId());
em.getTransaction().commit();
em.close();

```
