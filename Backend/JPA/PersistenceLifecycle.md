# Persistent Lifecycle
## Persistece Context
It is a service that remembers all modifications and state changes made to some data in a particular unit of work

## Entity instace state
![Screenshot 2023-11-10 145007](https://github.com/brian6484/CSKnowledge/assets/56388433/25e1c3f6-7816-4eb3-8d9f-91cbfa126439)

### Transient state
Transient state - Instances created with the *new* Java operator are transient, which means if they are no longer referenced,
their state is lost and garbage collected by JVM immediately. For e.g. new Item() creates a transient instance of Item class,
new Long() or new BigDecimal() too. Hibernate does not provide any rollback methods for transient instances so if we modify them,
that is it we can't change it.

To transit from transient to persistent, it requires either
1) a call to EntityManager.persist() method
2) creation of a reference from already-persistent instance to this transient instance by setting association field (parent.setChild(transient_child))
and then enable cascading for the association between the 2 like @OneToOne(CascadeType.PERSIST). Then you persist the *persistent instance**.

E.g. 
```java
@Entity
public class ParentEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToOne(cascade = CascadeType.PERSIST)
    private ChildEntity childEntity;

    // other fields and methods...
}

@Entity
public class ChildEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // other fields and methods...
}

// Usage
ParentEntity parentEntity = new ParentEntity();
ChildEntity childEntity = new ChildEntity();

parentEntity.setChildEntity(childEntity); // Reference from persistent to transient
entityManager.persist(parentEntity);      // Cascading persistence
```

## Persistent state
It has a representation in db, which means it is already stored in db or either gonna be stored in db after unit of work is completed.
It is an instance with a db identity, which is set to PK value of db representation.
Instances are persistent cuz entitymanager persisted it or app created a reference to that object from another persistent instance like 

```java
// Assume entityManager is an instance of EntityManager

// Retrieve a persistent Department from the database
Department existingDepartment = entityManager.find(Department.class, 1L);

// Create a new transient Employee
Employee newEmployee = new Employee();
newEmployee.setName("John Doe");

// Associate the new employee with the existing department
newEmployee.setDepartment(existingDepartment);

// At this point, newEmployee is transient

// Persist the new employee by saving the reference from a persistent Department
existingDepartment.getEmployees().add(newEmployee);

// At this point, newEmployee becomes persistent
```

