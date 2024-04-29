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

## How about adding where methods?
So we explicitly set the values in our DTO result list instead of doing subquery with Querydsl. Then, how do we set
where conditions? We can't do where(person.name.equals(condition.getName())) cuz we didnt select person.name with Querydsl in the first place.

I thought about it and there is a simple solution. If we are sending in "" or null values in our search conditions 
when they are emtpy, and some values when we wanna search something, we can set those values in our condition DTO in the controller step. And then, we do this search logic in the final step via collections.filter(), where we go through each result and see if a field in our result matches this search condition.

```java
//after querydsl and explicitly setting the values via setter methods

List<FinalDto> collect = content.stream()
  .filter(FinalDto ->
    //when search field comes in as null (in my case it was "")
    boolean match1 = Objects.equals(FinalDto.getName,"") ||
    //when search field indeed has a value
    FinalDto.getName.equalsIgnoreCase(condition.getName());
  
  //other boolean search conditions
  
  return match1 && match2 && ...
})
.collect(Collectors.toList());
```



```java
//main query
...where(entityC.id.eq(entityBId))

//setter method
dto.setCount(count);
```
