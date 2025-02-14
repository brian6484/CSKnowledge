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
Contains metadata about the token, typically the type (JWT) and the hashing algorithm used (e.g., HS256) and kid, which specifies which key is used to sign the JWT.
This kid is used by the server to fetch the appropriate public key from like a OIDC provider's public key endpoint.
```json
// Header
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "abc123"  // Key ID
}
```

### Payload
Contains the claims, which are statements about the user and additional data.
There are 3 types of claims:
Registered Claims: Predefined keys like iss (issuer), exp (expiration time), sub (subject), aud (audience).
iss claim specifies the issuer that made this JWT token and is usually a url like Google/Fb.
aud claim specifies the **intended audience** of the token. For example, it might be a client ID or url of a service
that is meant to consume this token.

Public Claims: Custom claims that must be agreed upon to avoid collisions.
Private Claims: Custom claims shared between parties.
```json
{
  "iss": "https://auth.example.com/",  // Issuer
  "aud": "my-client-id",              // Audience
  "sub": "user123",
  "exp": 1681197497
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

## Common questions
### **1) Why Do We Need Refresh Tokens?**  
We need **refresh tokens** because **access tokens have a short lifespan** (e.g., 15 minutes to 1 hour). Without refresh tokens, users would have to **log in again** every time an access token expires.  

**Benefits of Refresh Tokens:**  
✅ **Better Security:** Short-lived access tokens limit damage if stolen  
✅ **Better User Experience:** Users stay logged in without re-entering credentials  
✅ **Reduced Load on Auth Server:** No need to verify user credentials repeatedly  
---

### **2) How to Handle Token Expiration in a Real-World App**  
When an **access token expires**, a system should automatically refresh it without logging the user out.  

#### **Common Approach: Silent Token Refresh**
1. **API Call Fails with `401 Unauthorized`**  
   - The server detects an expired access token.  
2. **Frontend Sends the Refresh Token**  
   - A request is made to `/refresh` endpoint with the refresh token.  
3. **Server Issues a New Access Token**  
   - The refresh token is verified, and a new JWT access token is issued.  
4. **Frontend Retries the API Call with the New Token**  
---

### **3) Is It Safe to Store JWTs in `localStorage`? Why or Why Not?**  
🚫 **No, it is not safe** to store JWTs in `localStorage`.  

#### **Why?**
❌ **Vulnerable to XSS Attacks**  
   - If an attacker injects JavaScript (`<script>` tag) into your website, they can read `localStorage` and steal the JWT.  

#### **Safer Alternatives**  
✅ **Use HTTP-Only Cookies** (Cannot be accessed by JavaScript)  
✅ **Store in Memory (State Management like Redux, React Context)** → Less persistence, but safer  

---

### **4) How to Invalidate a JWT Before It Expires?**  
Since JWTs are **stateless**, they **cannot be revoked** directly unless you implement **one of these strategies:**  

#### **Option 1: Maintain a Token Blacklist (Database Approach)**
1. Store invalidated JWTs (or their `jti` claim) in a database.  
2. On each request, check if the JWT is blacklisted.  
3. If it is, reject the request.  

🔹 **Example: Adding `jti` Claim in JWT**
```json
{
  "sub": "user123",
  "exp": 1710003600,
  "jti": "unique-token-id"
}
```
When a user logs out, store `jti` in a **revoked tokens list**.

To optimise space in DB, just store the jwt tokens until their **expiration time**. Also can use redis in-memory to delete those tokens once ttl expires.

#### **Option 2: Use Short-Lived Access Tokens + Refresh Tokens**  
- Since access tokens expire quickly, an attacker can only use them for a short time.  
- Refresh tokens can be **revoked in the database** upon logout.  

#### **Option 3: Change Secret Key (Force Logout All Users)**
- Changing the JWT signing secret (`HS256` or `RS256` key) will **invalidate all issued tokens**.  



