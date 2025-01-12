## Enum
It allows a variable to be a set of predefined variables. It is useful to represent certain fixed common values.

It can also have constructors, getter methods, etc.
This constructor is especially useful but why? Enum, or basically a constant, on its own do not contain much information.
Maybe we create a day ENUM like Monday,Tuesday, etc but we wanna add additional info like whether it is weekday/weekend.
To do that, we can create a constructor to contain this add info. Soon, we will see how this is useful in handling exceptions.

```java
public enum Day {
    MONDAY("Weekday"),
    SATURDAY("Weekend");

    private String type;  // Extra information (Weekday or Weekend)

    // Constructor
    private Day(String type) {
        this.type = type;  // Assign the extra info
    }

    public String getType() {
        return type;  // Method to get the extra info
    }
}
```
