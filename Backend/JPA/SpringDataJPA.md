# Spring Data JPA

## Keywords

| Keyword | Example | Generated JPQL |
| :---: | :---: | :---: |
| Is, Equals | findByUsername, findByUsernameIs, findByUsernameEquals | ... where e.username = ?1 |
| And | findByUsernameAndDate | ... where e.username = ?1 and e.date = ?2 | 
| Or | findByUsernameOrDate | ... where e.username = ?1 or e.date = ?2 | 
| LessThan | findByDateLessThan | ... where e.date < ?1 |
| LessThanEqual | findByDateLessThanEqual | ... where e.date <= ?1 |
| GreaterThan | findByDateGreaterThan | ...where e.date > ?1 |
| GreaterThanEqual | findByDateGreaterThanEqual | ...where e.date >= ?1 |
| Between | findByDateBetween | ... where e.date between ?1 and ?2 |
| OrderBy | findByDateOrderByUsernameDesc | ...where e.date = ?1 order by e.username DESC |
| Like | findByUsernameLike (some pattern) | ... where e.username like ?1 |
| NotLike | findByUsernameNotLike (some pattern) | ... where e.username not like ?1 |
| Before | findByDateBefore | ... where e.date < ?1 |
| After | findByDateAfter | ... where.edate > ?1 |
| Null, IsNull | findByDate(Is)Null | ... where e.date is null | 
| NotNull , IsNotNull | findByDate(Is)NotNull | ... where e.date is not null | 
| Not | findByUsernameNot | ... where e.username <>?1 |

A note is than LessThan and Before both filters entities on numeric fields and date but LessThan
is more general whilst Before is specifically for date fields.

## Generify
In the above repo methods, the methods know exactly what the return types are from compile time.
But we can generify the return type of repo methods, making them dynamic.

For example,
```java
<T> List<T> findByEmail(String username, Class<T> type);

## most likely you are gonna use dto instead of projection
List<Projection.UsernameOnly> usernames =
    userRepository.findByEmail("mike@somedomain.com", Projection.UsernameOnly.class);
    
List<User> users =
    userRepository.findByEmail("mike@somedomain.com", User.class);
```
