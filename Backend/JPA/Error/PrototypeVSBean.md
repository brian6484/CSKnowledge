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

The issue was that since prototype is a single object, it is instantiated ONLY ONCE. So invoking the service logic reuses this
old object that contains stale data. 

## Solution
Instead, ApplicationContext bean should be injected in that object. This is so that new bean is instantiated every time
this service logic is invoked.

```java
in ur method:
object = applicationContext.getBean(ObjectInjectedInMyServiceMethod.class)
```
