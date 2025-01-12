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
