## Persistece Context
It is a service that remembers all modifications and state changes made to some data in a particular unit of work.
PC is created when EMF.createEM() is invoked. And this PC is closed when EM.close() is invoked. 
In JPA, it is an *application-managed* PC, which means our app defines the boundaries of PC, which subsequently demarcates the unit of work.

In detail, PC monitors and manages all entities in persistent state. It also performs *dirty checking*, detecting modifications of entity.
The change is then synchronised with db - either on demand or automatically. When unit of work ends, PC pushes changes stored in memory to DB via
DML like UPDATE, INSERT or DELETE. This **flushing** can also be called explicity before unit of work to force the changes into db before execution of query.

It also acts as first-level cache, remembering all entity instances in that unit of work so that we don't repeatedly send queries to db for the same instance.

PC also helps solve the object/relational mismatch problem of (==). DB checks equality by pk values in rows while OOP uses ref(==). PC 
solves this by ensuring that if any instances represent the same DB row in the **same PC**, then their DB ids will be the same like

```java
entityA.getId().equals(entityB.getId())
```
