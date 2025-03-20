## What is it
It just adds metadata to ur code but **dont contain executable instructions themselves**. So you need to process it.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyCustomAnnotation {
    String value() default "default value";
    int count() default 0;
}
```

@Target specifies where the annotation u wann apply. It could be ElementType.METHOD (method), ElementType.TYPE (classes), ElementType.FIELD (fields), etc.

@@Retention: Specifies how long the annotation should be retained.
RetentionPolicy.RUNTIME: Indicates the annotation should be available at runtime (which is crucial for reflection).
Other options include RetentionPolicy.SOURCE (discarded by the compiler) and RetentionPolicy.CLASS (available during compilation but not at runtime).

## Example
```java

```
