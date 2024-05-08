## Abstract method
It is method without implementation. It is used by abstract class or interface that is intended to be implemented when 
another class extends it (class) or implements it (interface).

## Abstract method in abstract class
```java
public abstract class Animal {
    public abstract void makeSound();  // Abstract method with no implementation

    public void eat() {  // Concrete method with implementation
        System.out.println("Eating...");
    }
}
```

The makeSound method is abstract cuz it has no implementation. So subclasses that extend that abstract class MUST 
implement the abstract method or it needs to be declared abstract itself.

```java
public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");  // Implementing the abstract method
    }
}
```

## Abstract method in abstract interface
Since java 8, interfaces can have abstract methods and static methods 
```java
public interface Vehicle {
    void drive();  // Abstract method

    default void honk() {  // Default method with implementation
        System.out.println("Honk!");
    }
}
```
honk method is a default method that classes which implemented this interface can either
1) override it
2) use it as it is
