## Issue 
In my service logic, I had some business logics that I was invoking. But in one of the methods, a prototype with @Scope("prototype")
annotation was injected. That was the old code that I just copied and pasted to use in my refactored code. But the problem
is that whenever I invoke this service logic, it sends the old data again.

```java
@Scope("prototype")
public class ObjectInjectedInMyServiceMethod {

}

@Service
@RequiredArgsConstructor
public class Service {
  private final ObjectInjectedInMyServiceMethod object;
}
```

## What is prototype?
Spring's bean registration method is basically singleton scope, and the Spring container manages all beans as singletons. But Spring also provides the ability to create and return a new object each time it is requested (prototype bean, @Scope("prototype")). So if it returns
a new object each time with this prototype, why is Spring reusing this object over and over again and sending stale data?

## Issue is DI
if you inject it into another bean using constructor or field injection (as in your Service class), Spring creates the prototype bean **only once** when the Service bean is created. As a result, the Service bean will hold a reference to a single instance of ObjectInjectedInMyServiceMethod, leading to the problem of stale data if that instance is reused across method calls.

## Solution
Instead, ApplicationContext bean should be injected in that object. This is so that new bean is instantiated every time
this service logic is invoked.

```java
in ur method:
object = applicationContext.getBean(ObjectInjectedInMyServiceMethod.class)
```
