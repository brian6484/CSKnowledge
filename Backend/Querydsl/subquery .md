## Intro
The bain of Querydsl is not being able to freely use subqueries unlike Mybatis. While Querydsl allows some simple
subqueries in the select and where clause, using it in the from clause is **not allowed**. All the responses from Gemini and
chatGPT were futile so I struggled to come up with a solution but here it is

## What is subquery in the first place?
What and why is subquery used in the first place? Normally we extract certain fields that are gonna be compared with the main
query. So if we simple execute that small subquery first and extract those fields that we want and **store them in a collection** 
like a list.

## Solution
Once the fields are mapped to a DTO object and stored into a collection, we iterate through each object and its fields.
We can get each field via

```java
for (Tuple tuple : list){
  Long entityBId = tuple.get(entityA.entityB.id);
  String entityAName = tuple.get(entityA.name);
  Long count = tuple.get(entityA.count().as("count"));
```

a small note is that I used as("count") because the name of the field is called count. If I didnt use that, the value
will not be mapped properly and prob a NullPointerException will occur when I try to use that count variable.

Anyway, once we have those fields, then we can use those fields accordingly in our main query OR set the fields in the result 
DTO with setter method.

```java
//main query
...where(entityC.id.eq(entityBId))

//setter method
dto.setCount(count);
```
