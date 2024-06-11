## Intro
It is standardised in Java 16. It defines **immutable data carriers** like for DTO purposes. They reduce the need to implement boilerplate code
by already providing accessor methods like equals(), hashCode(), toString(), etc.

## How to declare
record keyword -> name of the record class -> parameter list in () like (name,amount) and voila

```java
public record Person(String name, int age) {}
```

This implicitly creates 
1) private final fields for each parameter (final means its value cannot be changed after initialisation)
2) public constructor
3) accessor methods for each field (name and age)
4) equals(), hashCode(), toString() methods
