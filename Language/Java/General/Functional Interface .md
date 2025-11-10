## Functional Interface
An interface with exactly one abstract method (SAM - Single Abstract Method). Can have multiple default/static methods.
```java
@FunctionalInterface  // Optional but recommended
public interface MyFunction {
    void execute();  // Only ONE abstract method
    
    // Can have default methods
    default void log() {
        System.out.println("Logging...");
    }
    
    // Can have static methods
    static void info() {
        System.out.println("Info");
    }
}
```

