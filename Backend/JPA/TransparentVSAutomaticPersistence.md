# TransparentVSAutomaticPersistence.md
Chapter 3.2.2

There are actually 2 types of persistence - transparent and automatic.

## Transparent Persistence
This is when entities do not know the underlying mechanism of how they are gonna be persisted into the db. 
So they don't have any dependence on the mechanism. This means that it allows a complete separation of concerns between
persistence *classes* of the domain model and the persistence *layer*.

## Automatic Persistence
This refers to an acutal persistence solution like JPA/Hibernate that handles all the low-level mechanism details like
writing SQL statements, opening/closing DB conncetions, etc.


