## AOP
![image (3)](https://github.com/brian6484/CSKnowledge/assets/56388433/2119ef41-cab8-463e-ab74-d2159797ad28)

AOP is known to handle cross-cutting concerns that are present in almost every module like logging or security, and this helps increase the modularity of each module by removing unnecessary code and only containing the core business logic,
following the **SRP principle**. Not only that, it modularises scattered concerns into a single module.

Without AOP
1) repetitive code everywhere
2) modifications require changes everywhere
3) mixing core business logic with additional functionalities makes code hard to read

## Concepts
1) Aspect - has *advice* and *pointcut*.
This is the one that encapsulates a cross-cutting concern like logging, transaction, security, etc by making it a separate module

2) Advice - additional functionality to *Target* at a specific point in program.
It has code to be executed during these **specific points** like before, after or during the method invocation.

3) Target - main core business logic (literally target for AOP to act upon). *Advice* is applied to this *Target* without directly modifying the Target's code

4) Jointpoint - states the **specific area** where this *Advice* is to be applied. For example it could be like
method entry points, constructor invocations, etc

5) Pointcut - set of rules that define where *Advice* should be applied. It selects JointPoints where *Advice* is executed

## Example
1) aspect example. This entire thing is aspect and it contains 2,3,4,5
```java
@Aspect
@Component
public class MyAspect {

    @Pointcut("execution(* com.example.MyService.*(..))")
    public void myPointcut() {}

    @Before("myPointcut()")
    public void beforeAdvice() {
        System.out.println("Before advice: Logging before business logic...");
    }

    @AfterReturning("myPointcut()")
    public void afterReturningAdvice() {
        System.out.println("After returning advice: Logging after successful execution...");
    }

    @AfterThrowing(pointcut = "myPointcut()", throwing = "exception")
    public void afterThrowingAdvice(Exception exception) {
        System.out.println("After throwing advice: Handling exception - " + exception.getMessage());
    }
}
```

2) Advice example
```
beforeAdvice(): Executed before the business logic.
afterReturningAdvice(): Executed after the successful execution of the business logic.
afterThrowingAdvice(): Executed after an exception is thrown during the business logic.
```
3) target example could be a controller or service.

4) the myPointCount() method in this Aspect serves as Pointcut, where it specifies where this Advice should
be applied at. In this case it is  com.example.MyService.*(..)) so all the methods in MyService

