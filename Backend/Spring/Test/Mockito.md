## any()
```java
given(authService.oidcLogin(any(), any())).willReturn(jwtToken);
```
Lets see this example. We are not interested in the parameters of this method. Whatever parameters this method takes, we just wanna return this `jwtToken`, that is made in the @BeforeEach step.
This is when we dont wanna run the entire service logic and just wanna return the jwtToken.
