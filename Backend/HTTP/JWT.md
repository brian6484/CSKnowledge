## JWT
It is a URL-safe token that can be passed as a JSON object between parties.

### Hold up, what is url-safe?
What I mean by URL-safe is that JWT uses Base64Url encoding for its Header and Payload parts.
Unlike standard Base64 encoding, Base64Url replaces certain characters that have special meanings in URLs:
+ is replaced with - (dash),
/ is replaced with _ (underscore),
= padding is omitted.

This makes the encoded string safe to include in URLs, query strings, or HTTP headers without needing additional encoding.

V impt: characters like +, /, and = can be misinterpreted by browsers or servers. Using Base64Url encoding prevents this issue.
So we can add our JWT token without a problem in either the **Authorisation Header** like 
```
Authorization: Bearer <JWT>
```
or a query parameter like
```
https://example.com/callback?token=<JWT>
```

## Continuing with JWT token
JWT has 3 parts - separated by a dot - Header, Payload, Signature.
Example
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

```

### Header
Contains metadata about the token, typically the type (JWT) and the hashing algorithm used (e.g., HS256)
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload
Contains the claims, which are statements about the user and additional data.
There are 3 types of claims:
Registered Claims: Predefined keys like iss (issuer), exp (expiration time), sub (subject), aud (audience).
Public Claims: Custom claims that must be agreed upon to avoid collisions.
Private Claims: Custom claims shared between parties.
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

### Signature
It verifies the authenticity of the token. It is created by encoding the header and payload and signing it with
a **secret key**, using the alg that is in the header.
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

## How does JWT work?
1) A user logs in with their credentials (username and password).
The server verifies the credentials and generates a JWT containing user information and claims.

2) The JWT is signed using a secret key or a public/private key pair.
The signed token is sent back to the client.

3) The client stores the JWT, typically in local storage, session storage, or a secure HTTP-only cookie.

4) For every subsequent request, the client includes the JWT in the Authorization header (e.g., Authorization: Bearer <token>).
The server decodes and verifies the JWT using the secret key.

5) The server checks the token’s signature and validates claims (e.g., expiration, issuer).
If the token is valid, the request is processed, and the user is authenticated.

6) JWTs often include an expiration time (exp claim).
After the token expires, the user must re-authenticate to obtain a new token.
