# OAuth2 (Open Authorisation)

## Flow
1) User clicks sign up/login
2) App redirects to an Oauth provider's login page where this request includes
- client id: Google/FB/etc
- redirect url: where users are redirected after they login (should be url in ur app)
- scope
3) User logins in and gives permission for our app to access their info
4) Oauth provider sends you the the **authorisation code** with the redirect url that you stated in step 2
5) We now exchange this authorisation code by sending a server-to-server request to oauth provider for 2 impt tokens
- accessToken (Authorisation): used to access user's data on the provider's platform
- idToken (Authentication): used to verify user's identity. It is often in **JWT token format** and contains user data like name and info

App sends a request like this
```
POST https://oauth2.googleapis.com/token
Content-Type: application/x-www-form-urlencoded

client_id=YOUR_GOOGLE_CLIENT_ID&
client_secret=YOUR_GOOGLE_CLIENT_SECRET&
code=AUTHORIZATION_CODE&
grant_type=authorization_code&
redirect_uri=YOUR_APP_REDIRECT_URL
```

6) Oauth provider sends a response back with these 2 tokens like:
accessToken: with this, we can make requests to oauth provider's API on behalf of the user
idToken: same as before
```
{
  "access_token": "ya29.a0ARrdaM...",
  "id_token": "eyJhbGciOiJIUzI1NiJ...",
  "expires_in": 3600,
  "token_type": "Bearer"
}

```

7) App verifies the idToken to ensure it came from the correct oauth provider and app is now free to use accessToken
8) After verifying these tokens, app can create a user session or a JWT token. User is now logged in.

   

