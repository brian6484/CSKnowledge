## Intro
It is standardised in Java 16. It defines **immutable data carriers** like for DTO purposes. They reduce the need to implement boilerplate code by already providing accessor methods like equals(), hashCode(), toString(), etc.

## How to declare
record keyword -> name of the record class -> parameter list in () like (name,amount) and voila

```java
public record Person(String name, int age) {}
```

This implicitly creates 
1) private final fields for each parameter (final means its value cannot be changed after initialisation)
2) public constructor
like this
```java
public Person(String name, int age) {
    this.name = name;
    this.age = age;
}
```
3) accessor methods for each field (name and age)
4) equals(), hashCode(), toString() methods

## When to use Record class over regular class
When **immutable** data carriers like DTO are needed. Once you declare the object like Person dimit = new Person("dimit",24), you cant change it like dimit.setAge(69) is not allowed.

But choose regular class when you need something more than just simple storage and retrieval.
or its fields like 
