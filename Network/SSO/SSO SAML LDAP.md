## flow
so for sso flow is 

u login to identity provider's sso portal (microsoft azure portal) and if successful the identiy provider sends the service provider a saml token. and the identity provider checks me in its active directory via ldap and makes this saml token. service provider consumes this token and if ok i can use multiple resources without needing to login each time?

One small clarification on the flow:
The typical browser-based SSO flow is actually slightly different in terms of who sends what to whom:

You try to access a Service Provider
- SP redirects you to the Identity Provider for login
- You authenticate with the IdP
- The IdP creates a SAML assertion
- Your browser sends this SAML assertion to the SP (usually via an HTTP POST or redirect)
- SP validates the assertion and creates a session for you

So technically, the IdP doesn't directly send the token to the SP - it sends it back through your browser, which then forwards it to the SP. This is important for the security model.
The "multiple resources without login" part: Once authenticated with the IdP, you can access other SPs that trust the same IdP without logging in again during your SSO session. Each SP still needs to receive and validate a SAML assertion, but you don't have to re-enter credentials.

## in terms of cookie
```
Step 1: User clicks "Login with SSO" on Confluence

Confluence generates a SAML request
Confluence might set some cookies here (like a temporary state/session cookie)
Redirects to Okta

Step 2: User logs into Okta

Okta authenticates the user
Okta generates a SAML response (proof of authentication)
Redirects back to Confluence with SAML response

Step 3: Confluence receives SAML response ← THIS is where JSESSIONID matters

Confluence validates the SAML response
Confluence tries to SET the JSESSIONID cookie (this is the session cookie)
But SameSite=Strict BLOCKS Confluence from setting this cookie (cross-site context from Okta redirect)
Without JSESSIONID, Confluence can't create a session
User appears logged out → redirects to login page
```

## top level navigation
if samesite=lax or none, it allows top level naviagtion, which is like clicking a link or a redirect.

- A protocol to query/access directory services (like Active Directory)
- Used for authentication (verify username/password) and user lookup
- Direct connection: Application → LDAP server
- Use case: Sync users, validate credentials in real-time
- Active Directory = Microsoft's implementation of a directory service (think of it as a database of users, groups, permissions)

## saml
SAML (Security Assertion Markup Language):

- ✅ An authentication standard for Single Sign-On (SSO)
- Uses assertions (XML tokens), not tokens stored in browser
- Flow: User → IdP (Identity Provider) → SAML response → SP (Service Provider/your app)
- Use case: SSO across multiple applications, federated authentication

When to use:

LDAP: Internal authentication, user directory sync, direct credential validation
SAML: SSO experience, third-party IdP (Okta, Azure AD), web-based authentication
