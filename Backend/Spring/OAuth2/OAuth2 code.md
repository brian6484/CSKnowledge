## 2 main Steps in Oauth2
1) The very first initial step is before even adding the access token in the **Authorisation header** of requests, the user needs
to login using OAuth 2 and get **id and access token** from google/apple.
- The frontend sends these tokens to the backend **inside the request BODY** (not in headers yet)

```http
POST /v1/auth/oidc/login
Content-Type: application/json

{
  "oauthType": "GOOGLE",
  "idToken": "google-id-token",
  "accessToken": "google-access-token"
}
```

But why is the token in the BODY? The backend needs to verify the idToken before issuing its own access token (which will be included
in the Authorisation header for every request later)

Verifying the id token:
```java
@Slf4j
public abstract class OIDCVerifier {
    protected final OIDCInfo verifyIdToken(String idToken) {
        String[] tokenParts = idToken.split("\\.");
        String kid =
                JsonPath.read(new String(Base64.getUrlDecoder().decode(tokenParts[0])), "$.kid");

        String bodyString = new String(Base64.getUrlDecoder().decode(tokenParts[1]));
        String iss = JsonPath.read(bodyString, "$.iss");
        String aud = JsonPath.read(bodyString, "$.aud");

        OIDCPublicKeys oidcPublicKeys = getOIDCPublicKeys(iss);
        OIDCPublicKeys.KeyInfo oidcPublicKey = getOIDCPublicKey(oidcPublicKeys, kid);
        if (!audienceValid(aud))
            throw new BusinessException(ExceptionStatus.TOKEN_INVALID_EXCEPTION);

        try {
            Claims body =
                    Jwts.parserBuilder()
                            .setSigningKey(
                                    getRSAPublicKey(oidcPublicKey.getN(), oidcPublicKey.getE()))
                            .build()
                            .parseClaimsJws(idToken)
                            .getBody();

            return OIDCInfo.builder().oAuthType(getOAuthType()).oAuthId(body.getSubject()).build();
        } catch (ExpiredJwtException e) {
            throw new BusinessException(ExceptionStatus.TOKEN_EXPIRED_EXCEPTION);
        } catch (NoSuchAlgorithmException | InvalidKeySpecException | JwtException e) {
            throw new BusinessException(ExceptionStatus.CUSTOM_AUTHENTICATION_ENTRYPOINT);
        }
    }
```

2) Making authenticated API calls -> token in authorisation header
Once backend verifies the idToken from oauth provider, backend issues its own access token, which the frontend should include in the
authorisation header for *every* request.

For backend to check this, backend should have filter/interceptor that validates this jwt token in the header. Normally it is 
done by JwtAuthenticationFilter. Note that this filter should also be registered in the spring security config.

```java
@Slf4j
@RequiredArgsConstructor
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtTokenProvider jwtTokenProvider;
    public static final String JWT_EXCEPTION_HEADER = "auth-error-msg";

    @Override
    protected void doFilterInternal(
            HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        String token = HttpHeaderUtils.getTokenFromAuthHeader(request);

        if (!StringUtils.hasText(token)) {
            setAuthenticationToContext(createGuestAuth());
        } else {
            try {
                Authentication authentication = jwtTokenProvider.getAuthentication(token);
                setAuthenticationToContext(authentication);
            } catch (JwtException e) {
                response.addHeader(JWT_EXCEPTION_HEADER, e.getMessage());
            }
        }
        filterChain.doFilter(request, response);
    }

    private AnonymousAuthenticationToken createGuestAuth() {
        return new AnonymousAuthenticationToken(
                "GUEST", "GUEST", List.of(new SimpleGrantedAuthority(RoleType.GUEST.getCode())));
    }

    private void setAuthenticationToContext(Authentication authentication) {
        SecurityContextHolder.getContext().setAuthentication(authentication);
    }
}
```

spring security
```java
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        log.info("Configuring security filter chain"); // Add debug log
        return http.cors(cors -> cors.configurationSource(corsConfigurationSource()))
                .httpBasic(HttpBasicConfigurer::disable)
                .csrf(CsrfConfigurer::disable)
                .formLogin(FormLoginConfigurer::disable)
                .headers(
                        headers ->
                                headers.frameOptions(HeadersConfigurer.FrameOptionsConfig::disable))
                .sessionManagement(
                        configure ->
                                configure.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
                .addFilterBefore(
////////////////////////////////here/////////////////////////////////////////////////////////////////
                        new JwtAuthenticationFilter(jwtTokenProvider),
                        SecurityContextHolderFilter.class)
                .build();
    }
```
