# Spring Data's Streamable 
## Example
Our query methods that return more than a single object can use Java interfaces like Iterable, List, Set. 
Spring Data also supports Streamable, which you can use to substitute an Iterable or other collection types.
You can concatentae Streamables (combine and chain streamables tgt to create a single continuous stream of elements), filter and map over elements.

e.g. in your repo you can declare
```java
Streamable<User> findByEmailContaining(String text);
Streamable<User> findByLevel(int level);
```

and use it like this. Not just for a test case but concatenating Streamables might be useful in Service layer.
```java
void testStreamable() {
    try (Stream<User> result = 
            userRepository.findByEmailContaining("someother")
                    .and(userRepository.findByLevel(2))
                    .stream().distinct()) {
        assertEquals(6, result.count());
    }
}

```
