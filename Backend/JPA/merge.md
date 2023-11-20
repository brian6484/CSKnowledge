# merge()
If we wanna retrieve a detached entity instance from the previous PC and wanna modify its property, 

```java
detachedUser.setUsername("johndoe");
em = emf.createEntityManager();
em.getTransaction().begin();
User mergedUser = em.merge(detachedUser);
mergedUser.setUsername("doejohn");
em.getTransaction().commit();
em.close();
```

## Logic
![Screenshot 2023-11-20 234956](https://github.com/brian6484/CSKnowledge/assets/56388433/2e9ff438-1e66-4e6a-bef4-b325f02e5c43)

When we first call merge() to bring that entity instance from detached to persistent state, Hibernate first checks if a persistent instance 
with the same db identifier (pk value) is in the PC. In this example, nope cuz PC is restarted so Hibernate loads an instance with this db 
identifier from the DB. merg() then copies this detached instance *onto** this loaded persistent instance. If instance with that db id cannot
be found *even from db*, then Hibernate instantiates a new instance, copies the values of the transient instance and makes it persistent.

The old reference is discarded to garabage. A single UPDATE query will be made by Hibernate when it flushes the PC during commit.

If the entity is not detached but is transient, Hibernate instantiates a new instance, copies the values of this transient instance and makes it
persistent.
