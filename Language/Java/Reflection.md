## Reflection
It is used to inspect and manipuate classes and members(methods & variables) at **runtime** cuz we dont know the specific
type of classes and objects **at runtime**. 

## Different from generics
Generics defines classes with *type parameters* and checks the constraints **at compile time**. So there is a diff between
compile time and runtime.

## Usages of reflection
### DI 
In Spring boot,
```java
@Component
public class MyService {
    private final MyRepository repository;

    @Autowired
    public MyService(MyRepository repository) {
        this.repository = repository;
    }
}
```

There is reflection used here in the implementation. When Spring application starts, it scans the classpath for classes
annotated with @Component (and service,controller,repo). There is no way for Spring to know these classes **before runtime**
so reflection is used to identify at runtime which classes need to be managed by Spring container.

inner implementation of scanning base packages
```java
public class ClassPathBeanDefinitionScanner {
    public void scan(String... basePackages) {
        // Example code snippet
        for (String basePackage : basePackages) {
            // Use reflection to find classes annotated with @Component, @Service, etc.
            List<Class<?>> classes = findClasses(basePackage);
            for (Class<?> clazz : classes) {
                if (clazz.isAnnotationPresent(Component.class)) {
                    registerBeanDefinition(clazz);
                }
            }
        }
    }

    private void registerBeanDefinition(Class<?> clazz) {
        // Register bean definition in the Spring context
    }
}

```
