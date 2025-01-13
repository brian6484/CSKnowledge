## Diff between authentication and authorisation
Authentication - identifying who you are (like via login or making account)
Authorisation - whether you have the authority to do this particular action

## Terminology of OAuth2
Resource server - server that hosts and manages the protected resources that client wants to access. It is 
responsible for handling requests to these resources, validating tokens, etc

Authorisation server - authenticates resource owner, obtains their consent and issuing access tokens to client. It just
authorises client to access requested resources *on behalf of resource owner*

Resource owner - you

Client - our service that we are making that requests to access the resource owner's resources. It initates the OAUTH2 
flow and obtains access tokens to access the protected resources

Access token - credential which represents the authorisation for client to access protected resources of resource
owner. Client can pass this time-limited access token to resource server to request access for the resources

Refresh token - optional credential where client can get access token without needing to do this whole oauth2 flow since
access token is time-limited

## Flow 
![image (1)](https://github.com/brian6484/CSKnowledge/assets/56388433/eb40c5cb-fa5f-47e6-9040-4ed949ae16a6)

1) First initiated by the frontend client by sending user to this endpoint of
```
http://localhost:8080/oauth2/authorize/{provider}?redirect_uri=<redirect_uri_after_login>
```
where provider will be like google/github/fb and redirect_uri is URI where user will be redirected **once authentication with OAUTH2 provider is successful**. This is diff from OAUTH2 redirectURi parameter

2) Upon receiving this authorisation request, Spring Security's OAuth2 client redirects the user to AuthorisationUrl of provider, which
is set in SecurityConfig. The states(sucess/fail) related to authorisation request is saved in *authorisationRequestRepository* in SecurityConfig.

If user allows permission to your app, provider redirects user with callback url **with authorisation code**.

```
http://localhost:8080/oauth2/callback/{provider}
```

But if he denies, he is redirected to that same callbak url but with error. 

3) If Oauth2 callback is successful and it contains the **authorisation code**, Spring Security will exchange this **authorisation code**
for an **access token** and invoke the **customOAuth2UserService** in SecurityConfig. This Service bean retrieves details of the
authenticated user and creates a *new entry* in db.

4) But if Oauth2 callback results in error, Spring Security will invoke **oAuth2AuthenticationFailureHandler** in SecurityConfig.

5) Finally, oAuth2AuthenticationSuccessHandler creates a **JWT authentication token** for user and sends the user to  OAUTH2 redirectURi
with this jWT token in a *query string* or cookie, as shown in diagram.

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

to be continued
