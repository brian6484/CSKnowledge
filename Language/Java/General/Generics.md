# Generics
## What is generics?
Well the pure English meaning that we know is 'general', which hints that this value can have various data types **without depending on the data format**.
It also means it is specified *outside the class* rather than internally.

## Advantages
### diff data type but same function
If there are 2 or more classes that have different data types (e.g. String, float, etc) but have same operating function, then they can be
grouped into 1 single common class. This improves reusability

### able to check for errors at compile time
Prior to generics introduced in Java 5, the *Object class* was used to generally take a value from the user like this
```java
Object object = new Object();
object.setName(123);
object.setNmae("hello");
```
If we create an Object instance with a String parameter and forcibly convert the String value to Integer, a ClasCastException (runtime exception) occurs.

But if we use generics, we are able to check for type mismatch at *compile time* instead of *runtime* like below

```java
// Generic class with a type parameter T
class Box<T> {
    private T value;

    // Constructor
    public Box(T value) {
        this.value = value;
    }

    // Getter method
    public T getValue() {
        return value;
    }

    // Setter method
    public void setValue(T value) {
        this.value = value;
    }
}

Box<Integer> integerBox = new Box<>(10);

// Creating a Box of String
Box<String> stringBox = new Box<>("Hello, Generics!");

// Attempting to assign a String to a Box of Integer (compile-time error)
// This line will generate a compilation error
// integerBox = stringBox;  // Uncommenting this line will result in a compilation error

// Trying to assign a non-Integer value to a Box of Integer (compile-time error)
// This line will also generate a compilation error
// integerBox.setValue("Invalid Value");  // Uncommenting this line will result in a compilation error
```




