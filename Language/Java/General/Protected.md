## Protected
Example:
```java
public abstract class OIDCVerifier {
    // Abstract method to check support for a specific OAuthType
    protected abstract boolean support(OAuthType oAuthType);

    // Method to extract Apple email from an idToken (concrete method)
    protected final String getAppleEmail(String idToken) {
        // Some implementation to extract email from idToken...
    }

    // Abstract method to verify an idToken (subclasses need to provide specific logic)
    protected final OIDCInfo verifyIdToken(String idToken) {
        // Some implementation to verify idToken...
    }

    // Abstract methods to fetch OIDC public keys and validate audience
    protected abstract OIDCPublicKeys getOIDCPublicKeys(String iss);
    protected abstract boolean audienceValid(String aud);
    protected abstract OAuthType getOAuthType();
}
```

So protected means it can only be accessible within its own package and its subclasses (even if they are outside this package).
Package means like the most top code package com.example.animals;
