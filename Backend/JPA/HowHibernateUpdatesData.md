# How Hibernate updates data
## It updates all columns, regardless of whether you changed it or not
Let's say only the description of an Item entity that is queried from DB is modified.
I didnt know this but Hibernate actually updates all columns, not just description, of that specific
persistent instance. So the update query looks like

```java
UPDATE ITEM SET name = ?, description = ?, price = ? WHERE id = ?
```

## Reason
Hibernate does this to generate these basic SQL statements at startup compile time, not runtime.
Chatgpt also said it is for concurrency, dirty checking opti, etc

## Dynamic SQL generation
If you want to modify specific fields, you have to use dynamic sql generation.
