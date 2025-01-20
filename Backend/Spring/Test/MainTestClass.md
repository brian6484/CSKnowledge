## Main test class
So coming from [base test class](https://github.com/brian6484/CSKnowledge/blob/main/Backend/Spring/Test/BaseTestClass.md), you want to inherit the abstract base test class to get all those properties.
The main test class thus does not have to be annotated with anything, making it simpler. However, there are still some necessary annotations - @BeforeEach, @AfterEach and @Test.

## @BeforeEach
This is setting up some necessary saving entities or something in common that will apply to **ALL TEST CASES** in our main test class.
```java
    @BeforeEach
    public void setUp() {
        User user = User.builder().email("testo@gmail.com").roleType(RoleType.USER).build();
        userRepository.save(user);
        Auth auth = Auth.builder().oAuthType(alreadyRegisteredUserOAuthType).oAuthId(alreadyRegisteredUserOAuthId).build();
        authRepository.save(auth);

        jwtToken = jwtTokenProvider.createUserToken(user, DeviceType.IPHONE);
    }
```

## @AfterEach
This is to undo the changes after each test so that we have a fresh set up done by @BeforeEach.
```java
    @AfterEach
    public void setDown() {
        userRepository.deleteAllInBatch();
        authRepository.deleteAllInBatch();
    }
```

## @Test
This is for ur main test case.

## Full Example
```java
class AuthControllerTest extends IntegrationTest {
    private User user;
    private JwtToken jwtToken;

    private final OAuthType alreadyRegisteredUserOAuthType = OAuthType.FACEBOOK;
    private final String alreadyRegisteredUserOAuthId = "21521521";
    private final String alreadyRegisteredUserIdToken = "idToken";

    private final OAuthType newUserOAuthType = OAuthType.APPLE;
    private final String newUserOAuthId = "1234";
    private final String newUserIdToken = "newIdToken";


    @BeforeEach
    public void setUp() {
        User user = User.builder().email("testo@gmail.com").roleType(RoleType.USER).build();
        userRepository.save(user);
        Auth auth = Auth.builder().oAuthType(alreadyRegisteredUserOAuthType).oAuthId(alreadyRegisteredUserOAuthId).build();
        authRepository.save(auth);

        jwtToken = jwtTokenProvider.createUserToken(user, DeviceType.IPHONE);
    }

    @AfterEach
    public void setDown() {
        userRepository.deleteAllInBatch();
        authRepository.deleteAllInBatch();
    }

    @Test
    void oidcLogin() throws Exception {
        // given
        String object = objectMapper.writeValueAsString(
                LoginRequest.builder().oauthType(alreadyRegisteredUserOAuthType).idToken(alreadyRegisteredUserIdToken).build());
        LoginRequest request = LoginRequest.builder()
                .oauthType(alreadyRegisteredUserOAuthType)
                .idToken(alreadyRegisteredUserIdToken)
                .build();
        given(authService.oidcLogin(any(), any())).willReturn(jwtToken);
        System.out.println(jwtToken);

        // when
        ResultActions result = mockmvc.perform(post("/v1/auth/oidc/login")
                .content(object)
                .contentType(MediaType.APPLICATION_JSON)
                .accept(MediaType.APPLICATION_JSON));

        // then
        result
                .andDo(print())
                .andDo(document("auth/oidc/login",
                        preprocessRequest(prettyPrint()),
                        preprocessResponse(prettyPrint()),
                        requestFields(
                                fieldWithPath("oauthType").type(JsonFieldType.STRING).description("OAuth Type (GOOGLE, FACEBOOK, APPLE)"),
                                fieldWithPath("idToken").type(JsonFieldType.STRING).description("Id Token"),
                                fieldWithPath("accessToken").type(JsonFieldType.STRING).optional().description("Access Token (GOOGLE, FACEBOOK)")
                        ),
                        responseFields(
                                fieldWithPath("data.accessToken").description("엑세스 토큰"),
                                fieldWithPath("data.refreshToken").description("리프레시 토큰"),
                                fieldWithPath("data.accessTokenExpiredDate").description("엑세스 토큰 만료시간")
                        )
                ))
                .andExpect(status().isOk());
    }
```
