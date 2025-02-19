## Whats this @ConfigurationProperties?
Suppose you declared some external configuration properties in your application.yml file. You wanna bind them to a Java object in Spring application.
To do that, you need to use @ConfigurationProperties.

## How?
1) Annotate the class with @ConfigurationProperties that you wanna use those yml file values. You dont need to add @Component cuz the ConfigProp
does that for us.
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "auth.oidc")
public class OidcProperties {
    private String clientId;
    private String clientSecret;
    // Other fields and getters/setters
}
```
2) Add @EnableConfigurationProperties to ur main class app
```java
@SpringBootApplication
@EnableConfigurationProperties
public class MyApplication extends SpringBootServletInitializer {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

## Parameter Binding Error
So the fields of my class are not being properly assigned the values from my application.yml file.
The error is with @RequiredArgsConstructor

Previous code
```java
@Getter
@Validated
@RequiredArgsConstructor
@ConfigurationProperties(prefix = "auth.oidc")
public class OIDCProperties {

    @NotNull(message = "해당값은 필수 값입니다")
    private final Google google;
//    @NotNull(message = "해당값은 필수 값입니다")
//    private final Apple apple;
//    @NotNull(message = "해당값은 필수 값입니다")
//    private final Facebook facebook;
//
    @Getter
    @RequiredArgsConstructor
    public static class Google {

        @NotNull(message = "해당값은 필수 값입니다")
        private final List<String> audiences;
        @NotBlank(message = "해당값은 필수 값입니다")
        private final String authUrl;
    }
//
//    @Getter
//    @RequiredArgsConstructor
//    public static class Apple {
//
//        @NotNull(message = "해당값은 필수 값입니다")
//        private final List<String> audiences;
//        @NotBlank(message = "해당값은 필수 값입니다")
//        private final String authUrl;
//    }
//
//    @Getter
//    @RequiredArgsConstructor
//    public static class Facebook {
//
//        @NotNull(message = "해당값은 필수 값입니다")
//        private final List<String> audiences;
//        @NotBlank(message = "해당값은 필수 값입니다")
//        private final String authUrl;
//    }
}
```

## Issue
```java
@RequiredArgsConstructor  // This was the main issue
public class OIDCProperties {
    private final Google google;  // 'final' was another issue
```

So Spring tries binding the properties in 2 ways
- Create object via no-args constructor, then use setters
- Use a constructor with matching parameter names

So I didnt have a no args constructor so method 1 didnt work but I thought method 2 would work.
But actually when JVM compiles our java code to bytecode, the **parameter name isnt preserved**!!! 

So the compiler will see it as: 
```java
public OIDCProperties(Google arg0) { ... }  // what the compiler sees
```

Moreover, the fields are final which means that setter methods are not allowed

## Solution 1
To fix that and preserve the original code, you need to 
```gradle
tasks.withType(JavaCompile) {
    options.parameters = true
}
```

## Solution 2 by Cluade
Remove requiredargsconstructor and final 

```java
@Setter
@Getter
@Validated
@ConfigurationProperties(prefix = "auth.oidc")
public class OIDCProperties {

    private Google google;
    private Apple apple;
    private Facebook facebook;

    // Default constructor needed for properties binding
    public OIDCProperties() {}

    @Setter
    @Getter
    public static class Google {
        // Setters needed for properties binding
        @NotNull(message = "해당값은 필수 값입니다")
        private List<String> audiences;

        @NotBlank(message = "해당값은 필수 값입니다")
        private String authUrl;

    }

    @Setter
    @Getter
    public static class Apple {
        @NotNull(message = "해당값은 필수 값입니다")
        private List<String> audiences;

        @NotBlank(message = "해당값은 필수 값입니다")
        private String authUrl;

    }

    @Setter
    @Getter
    public static class Facebook {
        @NotNull(message = "해당값은 필수 값입니다")
        private List<String> audiences;

        @NotBlank(message = "해당값은 필수 값입니다")
        private String authUrl;

    }
}
```

## Another example
In my redis config, I was trying to get value from yml with @Value but the variable itself was declared as **final**.
```java
@Value("${spring.redis.port}")
private final int redisPort;
```

This is a problem cuz
1) final means they are initialised at declaration time/in the constructor
2) @Value injects values **AFTER** object construction using reflection
3) When there's final field, it makes the field immutable after initialisation
4) So Spring cannot inject value  
