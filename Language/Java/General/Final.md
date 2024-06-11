## Final
## Variable
Its value cannot be changed after initialisation. 

For example, this declares a constant MAX_VALUE whose value cannot be modified.
```java
final int MAX_VALUE = 100; 
```

### Primitive data types
Once value is assigned, value cannot be changed

### Reference data types
Once object is assigned to that reference object, that reference object cannot be **reassigned** to another object.

For example if it is NOT final
```java
// Creating a new Person object
Person person1 = new Person("Alice"); // person1 is a reference to a Person object

// Creating another reference to the same object
Person person2 = person1; // person2 now references the same object as person1

// Modifying the object via one reference
person2.setName("Bob"); // Changing the name using person2
```

### Thread-safety
By declaring an object as final, since the reference cannot be changed, it provides stability and prevents unintended
behaviour of object due to unintentional reassignment. **HOWEVER**, it is not enough to ensure total thread safety
cuz the methods to modify the object or its contents may not be thread-safe. 

So use a thread-safe implementation like ConcurrentHashMap.

## Class
Final class ensures that it cannot be **subclassed**. 
