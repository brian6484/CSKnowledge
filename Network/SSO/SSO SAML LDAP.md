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
