## Ref
[VERY USEFUL LINK](https://www.callicoder.com/spring-boot-security-oauth2-social-login-part-2/)
explained very well, including the custom classes but we have the relevant basic theory [here](https://github.com/brian6484/CSKnowledge/blob/main/Network/OAuth2.md)
so lets try to implement this with code.

## Flow
![image (2)](https://github.com/brian6484/CSKnowledge/assets/56388433/a6201109-59b3-49a2-9169-9a35f4a65570)
There are additional filters and resolvers that come into play, as well explained in [here](https://velog.io/@nefertiri/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0-OAuth2-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC)

### OAuth2AuthorizationRequestRedirectFilter
So when client clicks the social login button (google/github/etc), the frontend requests backend with this endpoint url (remember?)
```
GET http://localhost:8080/oauth2/authorization/google?redirect_uri=http://localhost:3000&mode=login
```
provider is google and redirect uri when oauth2 is successful is that localhost with port 3000. It is this OAuth2AuthorizationRequestRedirectFilter
that handles this request with **doFilterInternal** method. Wihtin this doFilterInternal method, **resolve()** method is invoked, which
if the request url and parameter is correct, it creates OAuth2AuthorizationRequest object and returns that

<img width="935" alt="image (3)" src="https://github.com/brian6484/CSKnowledge/assets/56388433/817eaf28-ca78-4055-96e5-35631035c5e5">

Then, AuthorizationRequestRepository's saveAuthorizationRequest() method saves this  OAuth2AuthorizationRequest object. So in your project,
you can have a custom-implemented class (maybe called HttpCookieOAuth2AuthorizationRequestRepository) that implements this repo and serialise
this obejct to save in a cookie.

Then, this OAuth2AuthorizationRequestRedirectFilter redirects user to authorizationRequestUri in that OAuth2AuthorizationRequest object.
That url will contain the OAuth2 provider's login page url.

### DefaultOAuth2AuthorizationRequestResolver


