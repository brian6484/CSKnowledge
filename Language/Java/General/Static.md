## Static
### 1-liner
Static belongs to the class, not the instances of the class. Since it is shared amongst all the class **instances**, it means it is used to declare something that is not meant to alter depending on each instance object so literally something "static".

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

## Why they can be used without an instance of a class
Static members are allocated a special area of memory called **method area** (there is stack and heap and method area
generally in memory of java). This area is allocated once class is loaded by JVM and the static members are stored there. Since this method area is shared by all instances of the class, we don't have to instantiate an object of that class to access these
static members. 

Static also dont have access to instance-specific members( variables and methods) unless you create an instance from that class.

### Static class
class that is associated with the class, rather than the **instances of the class**. Normally it is static inner class 

advantages are that we dont need an instance of the outer class to use this static inner class. For example if outer class is StringValidator and
static inner class is Validator, we dont need to create an instance of stringvalidator to use validator's static methods cuz we can do 
StringProcessor.Validator.isValidEmail()

also helps with encapsulation and being logically grouped with outer class
```java
class StringProcessor {
    public static class Validator {
        public static boolean isValidEmail(String email) {
            return email != null && email.contains("@");
        }

        public static boolean isNotEmpty(String str) {
            return str != null && !str.trim().isEmpty();
        }
    }

    private String text;

    public StringProcessor(String text) {
        this.text = text;
    }

    public boolean isEmailValid() {
        return Validator.isValidEmail(this.text); // Using the static nested utility
    }

    public boolean isTextNotEmpty() {
        return Validator.isNotEmpty(this.text);   // Using the static nested utility
    }

    public static void main(String[] args) {
        StringProcessor processor = new StringProcessor("test@example.com");
        System.out.println("Is email valid? " + processor.isEmailValid()); // true
        System.out.println("Is text not empty? " + processor.isTextNotEmpty()); // true

        System.out.println("Is another email valid? " + StringProcessor.Validator.isValidEmail("invalid")); // false
    }
}
```
