# @Query
## About @Query
With this @Query annotation, the method name does not have to follow conventional naming like JpaRepository's method.
The method can be parameterised by 2 ways - with @Param or identifying parameters by position/name.
We can also pass a nativeQuery=true/false parameter to @Query annotation, but using this reduces the portability of app.

### SpEL
One thing I didn't know is that you can use Spring Expression Language (SpEL) expressions in @Query. For example:
```java
@Query("select e.name from #{entityName} e)
```
this entityName is resolved based on the @Entity annotation. So if we defined a repo interface that extends JPARepository, JPA 
inspects the name of the entity associated with that repo and resolves #{entityName} to that name of entity.

### Sort parameter
You can also pass a Sort parameter to allow us to order the result of this query based on a different criteria. I also
find this v.useful because we ideally want a flexible query where the query does not output a solid sorted result but a result 
that we can take and sort later based on business logic. Obviously for simple sorting like id ASC or DESC can be implemented directly
in our query but for complex logic, it might be useful to pass Sort parameter.

## Examples
```java
## Using placeholder ?1 , ?2
@Query("select count(u) from User u where u.active=?1")
int findNumberOfUsersByActivity(boolean active);

## Using @Param
@Query("select u from User u where u.level = :level and u.active = :active")
List<User> findByLevelAndActive(@Param("level") int level, @Param("active") boolean active);

## NativeQuery parameter
@Query(value = "SELECT COUNT(*) FROM USERS WHERE ACTIVE = ?1", nativeQuery = true)
int findNumberOfUsersByActivityNative(boolean active);

## Using SpeL
@Query("select u.username, LENGTH(u.email) as email_length from #{#entityName} u where u.username like %?1%")
List<Object[]> findByAsArrayAndSort(String text, Sort sort);

```
The last query is quite complex. It returns a list of arrays that contains username and length of email after filtering
by username. The second parameter allows us to fruther sort this query result. For example you can Sort.by("email_length").descending() and
pass that as parameter when calling that query.

such as this
```java
List<Object[]> usersList2 = userRepository.findByAsArrayAndSort("ar", Sort.by("email_length").descending());
```

## A tricky personal example
So I had this @Query where I made BankDetails an Embedded class of User entity and in that BankDetails, I allocated bankAccountNumber and
bankName.

```java
  @Query("SELECT u.bankDetails.bankAccountNumber, u.bankDetails.bankName FROM User u WHERE u.id= :userId")
  Optional<Object[]> findBankDetailsById(@Param("userId") Long userId);
```
If you think this code is correct, you are wrong. The type should be Object[][] because query actually returns an array of array of objects. bankAccount and bankNumber is not returned as an array of objects as I intended but they are within another outer array like
[[123124,"bitchbank]]. I think this is because of the embeddable class implementation. If I just made it as regular fields within User entity, I can just do Object[] but here it is wrong. So correct answer is

```java
  @Query("SELECT u.bankDetails.bankAccountNumber, u.bankDetails.bankName FROM User u WHERE u.id= :userId")
  Optional<Object[][]> findBankDetailsById(@Param("userId") Long userId);
```

And one important thing is you need @Column(nullable= true) for the bankAccount and bankNumber like explicitly annotate it.

## @Query turns SQL result set of a query to entity instances
It first tries to resolve every instance entity with PC but if a data with this particular db identifier cannot be found in PC,
only then does it read the rest of the result set then this entity instance, once found, is populated in PC.

## Errors
### InvalidDataAccessApiUsageException: For queries with named parameters you need to use provide names for method parameters.
Turns out I was using the wrong @Param import. It should be org.springframework.data.repository.query.Param.
