## @GroupSequence
It is in Java's bean validation API that specifies the **order** of validation groups when validating a bean.

## Example
```java
@GroupSequence({ValidationGroups.NotEmptyGroup.class, ValidationGroups.SizeCheckGroup.class,
    ValidationGroups.PatternCheckGroup.class})
public interface ValidationSequence {
}
```
So it will check for notemptygroup -> sizecheckgroup, etc. 

```java
public interface ValidationGroups {
    interface NotEmptyGroup {}
    interface SizeCheckGroup {}
    interface PatternCheckGroup {}
}

public class LoginRequest {

    @Schema(description = "OAuth Type", requiredMode = Schema.RequiredMode.REQUIRED)
    // TODO : EnumCheck
    private OAuthType oauthType;
    @Schema(description = "Id Token", requiredMode = Schema.RequiredMode.REQUIRED)
    @NotBlank(message = "Id Token은 필수 값입니다.", groups = ValidationGroups.NotEmptyGroup.class)
    private String idToken;
    @Schema(description = "Google/Kakao Access Token", requiredMode = Schema.RequiredMode.NOT_REQUIRED)
    private String accessToken;
}

	@Operation(summary = "OIDC 로그인")
	@PostMapping("/oidc/login")
	@ResponseStatus(HttpStatus.OK)
	public SuccessResponse<JwtToken> oidcLogin(
		@RequestBody @Validated(ValidationSequence.class) LoginRequest request,
		UserDevice device
	) {
		return new SuccessResponse<>(authService.oidcLogin(request, device.getDeviceType()));
	}
```

But what if some parameters like this oauthType are not assigned any groups? In this case, they will be validated **AFTER** the parameters with assigned groups.

