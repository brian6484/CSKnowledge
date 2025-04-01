## Converter
It converts entity attributes accordingly

## Example
This converts boolean attribute of JPA entity to a String value of Y/N into the DB. When JPA entity is fetched from DB, it is vice versa where the string
values in DB are converted to boolean
```java
@Converter
public class BooleanToYNConverter implements AttributeConverter<Boolean, String> {

    /** Boolean 값을 Y 또는 N 으로 컨버트 */
    @Override
    public String convertToDatabaseColumn(Boolean attribute) {
        return (attribute != null && attribute) ? "Y" : "N";
    }

    /** Y 또는 N 을 Boolean 으로 컨버트 */
    @Override
    public Boolean convertToEntityAttribute(String yn) {
        return "Y".equalsIgnoreCase(yn);
    }
}

```
