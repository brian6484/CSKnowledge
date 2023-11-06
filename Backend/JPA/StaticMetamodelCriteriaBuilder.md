# Static metamodel and criteriaBuilder
## Metamodel
Firstly, what is a metamodel? Metamodel can gives us info about our persistent classes and there are 2 ways to access it - dynamic or static.
Dynamic is similar to Java reflection where you deduce the persistent classes **during runtime** and you can use it for custom validation or generic UI code.
However it is not type-safe and not commonly used so let's look at static one

## static metamodel
### dependency
You need to add jpamodelgen as a dependency in your build.gradle
It will generate a metadata class of all your persistent classes , which list all the attributes of your persistent class.
Their names will have an underscore behind the class name like Item_

For example:
```java
@Generated(value = "org.hibernate.jpamodelgen.JPAMetaModelEntityProcessor")
@StaticMetamodel(Item.class)
public abstract class Item_ {
    public static volatile SingularAttribute<Item, Date> auctionEnd;
    public static volatile SingularAttribute<Item, String> name;
    public static volatile SingularAttribute<Item, Long> id;
    public static final String AUCTION_END = "auctionEnd";
    public static final String NAME = "name";
    public static final String ID = "id";
}
```

Now we can use these metadata classes and fields as if using persistent classes in your CriteriaBuilder.
If the fields are of wrong intented type or if we try to access
a nonexistent field, it will raise an error **at compiletime** instead of runtime.

as such
```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Item> query = cb.createQuery(Item.class);
Root<Item> fromItem = query.from(Item.class);
query.where(
    cb.like(
        fromItem.get(Item_.name),
        cb.parameter(String.class, "pattern")
    )
);
```

## why do we use this static metamodel instead of Spring Data Jpa?
In that above example, we can very easily do that in Spring Data JPA like jparepository.findByName(). 
So why do we even need this metaclass??

I asked ChatGPT but im still not convinced. tbc

### Type-safety
It offers more type-safety 
### Complex queries
It may allow more complex queries 
### Custom queries
May allow more custom queries
### Compatibility
I agree with this one. This metamodel can be used with any JPA provider, not just Spring Data JPA.
So if you decide to change the provider, it is still valid.

