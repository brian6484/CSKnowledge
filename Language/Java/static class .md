## Static
### Static variable
Variable belongs to the *class*, rather than the *instance of the class*. It is thus shared by **all instances of
the class** and can be accessed via the class name like User.staticVariable.

Declare variable as static when you know that this particular field or property is not gonna change and is same for all instances
of the class (static)

```java
public class Example {
    // Static variable
    public static int staticVariable;

    // Other instance variables
    public int instanceVariable;

    public Example(int instanceVariable) {
        this.instanceVariable = instanceVariable;
    }

    public static void main(String[] args) {
        // Accessing static variable using class name
        Example.staticVariable = 10;

        // Creating instances of the class
        Example obj1 = new Example(20);
        Example obj2 = new Example(30);

        // Accessing static variable using an instance (not recommended)
        obj1.staticVariable = 100;

        // Accessing instance variable
        System.out.println("obj1.instanceVariable: " + obj1.instanceVariable);
        System.out.println("obj2.instanceVariable: " + obj2.instanceVariable);

        // Accessing static variable using class name
        System.out.println("Example.staticVariable: " + Example.staticVariable);
    }
}
```

### Static method
method that belongs to the *class*, rather than the **instances of the class**.
We have seen this a lot where we call a static method with the class name **without needing to create an instance of the class** like
UserDto.toEntity().

```java
public static ProfitSettlementLogDto toDto(ProfitSettlementLog profitSettlementLog){
    return new ProfitSettlementLogDto(profitSettlementLog.getProfitSettlementLogStatus(),
            profitSettlementLog.getAmount());
}
```

We also dont have access to instance-specific members( variables and methods) unless you create an instance from that class.



### Static class
class that is associated with the class, rather than the **instances of the class**.
