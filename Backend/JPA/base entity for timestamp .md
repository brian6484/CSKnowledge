## Utilising Base Entity superclass for time stamps
![download (3)](https://github.com/brian6484/CSKnowledge/assets/56388433/8bc02a3e-db18-488b-976a-8ba61ff5c2b7)

Too many times I have seen null values in my create and update fields so I wish to arrange this note. That is normally
due to **not placing @EnableJpaAuditing annotation in our main method. But more on that later

## BaseEntity
The usual stuff
```java
@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public class BaseTime {

    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;
}
```
### MappedSuperclass
JPA Entity classses that inherit this superclass will inherit those superclass fields too.

One important thing is that this annotation tells JPA that it is **not an entity** so it is just like an abstract class.

### EntityListener
The official description is "Specifies the callback listener classes to be used for an entity or mapped superclass. This annotation may be applied to an entity class for mapped superclass.".
So by annotating a class or superclass with this, it effectively designates a callback listener, which is a class or a method
that "listens" to specific events related to lifecycle of a JPA Entity - persist, detach, etc.

It itself **isnt a callback listener**. It just associate one or more callback listeners with a JPA entity, in this case,
AuditingEntityListener.

## AuditingEntityListener
![download (4)](https://github.com/brian6484/CSKnowledge/assets/56388433/37ea8f4b-d8b1-4019-8f0d-4bb304bb1202)

It is a global callback listener. Lets look at these 2 methods in that class, which sets the create and update time.
The description is “Set modification and creation date and auditor on the target object in case it implements Auditable on update events.”

Unlike entity-specific callbacks, which are **defined within the entity class itself** and are for specific stages of entity's
life cycle like @PrePersist, @PostPersist, @PreUpdate, @PostUpdate, this is just for more just general auditing purpose.

## Don't forget to audit main method!!
![download (5)](https://github.com/brian6484/CSKnowledge/assets/56388433/148c3d4f-b75c-455a-85e4-58be0bb7def3)

This @EnableJpaAuditing annotation activates JPA auditing function and without it, timestamps will not work. 


