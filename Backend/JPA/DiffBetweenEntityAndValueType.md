# Diff Between Entity type And Value Type
## Foreground
For example, you have 2 users - Brian and Kym. An instance of User class represents an independent separate account.
Because we want to CRUD instances independently, User should be an instance.

Now User class has a homeAddress attribute. Should user instances have a runtime reference (object that has a reference to another object
in memory) to the **same** address or should each user instance have a ref. to its own address? 

## Entity type
![Screenshot 2023-11-08 134335](https://github.com/brian6484/CSKnowledge/assets/56388433/1de23d33-e74c-4d6f-ab1a-d68310ac8f17)
The former is that the 2 user instances share a single Address instance. If address is supposed to support shared runtime ref, then Address should
be an entity type. Then this address will have its own life. You can't delete this instance if Kym removes her User account because Brian may still
refer to this address

## Value type (also known as embeddable class/ basic property type)
![Screenshot 2023-11-08 134349](https://github.com/brian6484/CSKnowledge/assets/56388433/b6b3e702-f602-44e2-9d56-27ce72902671)
Here, each User instance has its own reference to its own homeAddress instance. This homeAddress instance is now dependent on a specific User instance, which means it
should be a value type. When Brian deletes his user account, the homeAddress instance can be deleted too.

## Essential distinction
### How are object relationships saved in db?
Before we look at diff between entity type and value type, when you want to store object relationships in db, you are not storing the objects themselves directly. Instead, a reference or identifier to those
objects are saved. These references are usually FKs. For e.g. Order class may have a FK that references Customer class in db.

### Entity tpe
Entity type has a persistent id (@Id) so that it is retrievable using its persistent identity. A reference to an entity instance (pointer in JVM)
is persisted and this reference is a foreign key-constrained value. Entity type also has its own lifecycle, which means it can exist independently of other entity.

### Value type
Value type has no persistent id (no @Id). It soley belongs to an entity instance so its lifespan is dependent on that entity instance. It does not support shared
references.

