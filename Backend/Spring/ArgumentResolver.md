## Argument Resolver
This is so useful for code reusability and a custom automatic binding of method arguments for our Controllers.

## Example
Let's suppose that we wanna take the **User-Agent** header from the request, convert it to my desired object of *DeviceType* and create a new UserDevice instance from that DeviceType object.
Without this resolver, we would have to write this extraction of header, conversion and creating UserDevice instance logic within the controller level liek
```java
    @PostMapping("/oidc/login")
    @ResponseStatus(HttpStatus.OK)
    public SuccessResponse<JwtToken> oidcLogin(
        @RequestBody @Validated(ValidationSequence.class) LoginRequest loginRequest,
        @RequestHeader("User-Agent") String userAgent  // Get User-Agent directly
    ) {
        // Create UserDevice manually in controller
        DeviceType deviceType = DeviceType.getDeviceType(userAgent);
        UserDevice userDevice = new UserDevice(deviceType);
        
        return new SuccessResponse<>(authService.oidcLogin(loginRequest, userDevice.getDeviceType()));
    }
```

## With resolver
We wanna inject this UserDevice class **directly into controller method argument**.
### Step 1
So the first step is implement this HandlerMethodArgumentResolver class
```java
public class UserDeviceArgumentResolver implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.getParameterType().equals(UserDevice.class);
    }

    @Override
    public Object resolveArgument(
        MethodParameter parameter,
        ModelAndViewContainer mavContainer,
        NativeWebRequest webRequest,
        WebDataBinderFactory binderFactory
    ) throws Exception {
        String header = webRequest.getHeader(HttpHeaders.USER_AGENT);
        String userAgent = header != null ? header.toLowerCase() : "web";
        return new UserDevice(
            DeviceType.getDeviceType(userAgent)
        );
    }
}
```

## Step 2
Register this resolver in WebMvc config
```java
@Configuration
@RequiredArgsConstructor
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new UserDeviceArgumentResolver());
    }
}
```

