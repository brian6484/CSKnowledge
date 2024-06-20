## Best practice
There are certain best practice rules to declare a utility class. 

1) Final Class: Declaring the class as final ensures that it cannot be subclassed, reinforcing the utility nature of the class.
2) Private Constructor: The private constructor prevents instantiation of the class, meaning it can only be used through its static methods.
3) Static Factory Method: The createObjectMapper method is a static factory method that creates and configures an ObjectMapper instance.

## Example
```java
final class ObjectMapperFactory {
    private ObjectMapperFactory() {
    }

    public static ObjectMapper createObjectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.getFactory().disable(JsonGenerator.Feature.AUTO_CLOSE_TARGET);
        return objectMapper;
    }
}
```
