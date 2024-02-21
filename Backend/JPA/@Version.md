# @Version
## So what is it
```java
@Entity
public class Item {

    @Version
    private long version;

    // other fields and methods...
}
```

Hibernate automatically increments this version number *whenever this entity instance is found to be dirty during flushing the PC*. It is a simple counter.


```java
EntityManager em1 = emf.createEntityManager();
em1.getTransaction().begin();
Item item = em1.find(Item.class, ITEM_ID);
// At this point, 'item' is loaded into the persistence context with a version of 0

assertEquals(0, item.getVersion()); 
item.setName("New Name");
// . . . Another transaction changes the record

// Here, an OptimisticLockException is expected because the version has been updated by another transaction
assertThrows(OptimisticLockException.class, () -> em1.flush());
// update ITEM set NAME = ?, VERSION = 1 where ID = ? and VERSION = 0
```
It can thus be used to implement locks.
