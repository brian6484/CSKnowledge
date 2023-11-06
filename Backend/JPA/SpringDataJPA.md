# Spring DATA JPA

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
