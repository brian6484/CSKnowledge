## A typical base test case to be inherited
```java
//@ActiveProfiles("test")
@ExtendWith({RestDocumentationExtension.class, SpringExtension.class, DataCleaner.class})
@SpringBootTest
@AutoConfigureRestDocs
@AutoConfigureMockMvc
//@Import({AmazonS3MockConfig.class, EmbeddedRedisConfig.class, DataCleaner.class})
public abstract class IntegrationTest {
    @Autowired
    protected MockMvc mockmvc;

    @Autowired
    protected ObjectMapper objectMapper;

    @Autowired
    protected JwtTokenProvider jwtTokenProvider;

    @MockBean
    protected AuthService authService;

    @Autowired
    protected UserRepository userRepository;

}
```
## Let's look at it in detail
1) ActiveProfiles("test") activates the **test** profile in the application, using config & beans declared in application-test.yml file. But if you are at starting stage and don't have profile, no need.
2) ExtendWith is part of Junit 5 that allows extensions of
- RestDocumentationExtension: for Spring REST Docs, helping generate API doc from tests
- SpringExtension: integrates the Spring TestContext Framework with JUnit 5, enabling support for Spring’s testing feature
- DataCleaner: you can add your own custom extension
3) SpringBootTest tells Spring to look for main config class like the one with @SpringBootApplication and start the application context. It tests integration of tests with **full application context**.
4) AutoConfigureRestDocs enables SpringRest Docs, helping generate API doc from tests
5) AutoConfigureMockMvc for mockmvc
6) @Import this is for **Spring context** while @ExtendWith is config for JUnit. 
