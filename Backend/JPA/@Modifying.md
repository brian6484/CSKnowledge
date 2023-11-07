# @Modifying
## About
As we have seen with @Query, we can do some custom, complex and parameterised queries that are mainly a SELECT property.
Now with @Modifying placed on top of @Query, you can do DDL statements like INSERT, UPDATE and DELETE queries that **modify the db**.
A precaution is that our methods now have to be marked with @Transactional or be run from a managed transaction.

## Advantage
Our modifying queries can directly target *which columns of table* to do ddl on like maybe User.active. They can also
include conditions like using WHERE.

Most importantly, the reason why you would use this over Spring Data JPA's methods like delete can be seen in the below example.
It is mainly cuz changing a limited number of columns is more time-efficient.

## Example (very important diff between 2nd and 3rd query)
```java
@Modifying
@Transactional
@Query("update User u set u.level = ?2 where u.level = ?1")
int updateLevel(int oldLevel, int newLevel);

@Transactional
int deleteByLevel(int level);

@Transactional
@Modifying
@Query("delete from User u where u.level = ?1")
int deleteBulkByLevel(int level);
```
Now the second query is by Spring Data JPA so the annotations of @Query and @Modifying are needed 
while the third query we made the query ourselves so we need them.

## **Very important** Diff between JPA generated and manual @Query 
Let's look at the first automatically-generated query. The sql generated is almost identical (actually exactly the same just that 
it didnt use alias) but is way different as to how it works.

```sql
DELETE FROM user WHERE level = ?
```

It first identifies all records that match 
the criteria (level). Then, it retrieves the user **instances** one by one in memory from db. For each user **instance**, 
if there are lifecycle methods (the 6 like @PrePersist, @PostRemov, etc) linked with those instances, they are executed too.
Once they are executed, only then are they removed from db. So in 1 line, it deletes record 1 by 1 while executing lifecycle methods 
if they are present.

Ok let's look at the second query.
It is able to remove users in bulk in 1 single JPQL query because it **doesnt retrieve individual User instances into memory**.
The query executes at database level so since they are not loaded into memory, it won't trigger any lifecycle methods. So it is more
efficient for batch deletions.


