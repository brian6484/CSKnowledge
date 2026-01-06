## Issue
if saml auth succeeds yesterday but its failing today

1) Check SAML certificate expiration (most common cause of "worked yesterday, broken today") - theres actually saml certificate. So we know saml token that contains user info is sent back from identity provider to browser. But its the saml certificate i.e. x509 cert that signs this token. IDP uses its private key to sign saml assertion and SP uses the IDP's public cert to verify if this signature is valid.
2) Review SP/IdP logs for specific error codes
3) Verify clock sync between IdP and SP (clock skew causes signature validation failures)
4) Check SAML response - is the assertion being sent? Are attributes correct?
5) verify metadata - has anything changed in IdP or SP metadata URLs?

## cookie issue (v impt)
Step 3: Confluence receives SAML response ← THIS is where JSESSIONID matters

Confluence validates the SAML response
Confluence tries to SET the JSESSIONID cookie (this is the session cookie)
But SameSite=Strict BLOCKS Confluence from setting this cookie (cross-site context from Okta redirect)
Without JSESSIONID, Confluence can't create a session
User appears logged out → redirects to login page

## samesite = strict policy
Controls when cookies can be sent with requests.

**Three settings:**

1. **SameSite=None** (most permissive)
   - Cookie sent with ALL requests, even cross-site
   - Example: You're on siteA.com, it makes request to siteB.com → cookie sent ✅

2. **SameSite=Lax** (default in modern browsers)
   - Cookie sent for top-level navigation (clicking links)
   - NOT sent for embedded requests (iframes, API calls from other sites)
   
3. **SameSite=Strict** (most restrictive) ← **This is what IT set!**
   - Cookie ONLY sent when you're directly on that site
   - NOT sent for ANY cross-site requests
   - **This BREAKS SSO!**

**Why it breaks SSO:**
- User on Confluence → redirects to Okta → Okta redirects back to Confluence
- With SameSite=Strict, Confluence cookies aren't sent during the redirect back
- Confluence doesn't recognize the user → back to login page (loop!)

---

## Third-Party Cookie Blocking

**What it is:** Blocks cookies from domains different than the one you're visiting

**Example:**
- You're on `confluence.company.com` (first-party)
- Page makes request to `okta.com` (third-party)
- Third-party blocking = `okta.com` can't set/read cookies ❌

**Why it breaks SSO:**
- SSO involves multiple domains (Confluence + Okta)
- They need to share authentication state via cookies
- Blocking third-party cookies = can't share state = login fails

---

## Cookie Whitelist

**What it is:** List of sites that ARE allowed to use cookies despite restrictions

**Example:**
```
Blocked by default: *
Whitelist: 
  - confluence.company.com ✅
  - company.okta.com ✅
  - atlassian.net ✅
```

**The problem in your scenario:**
- IT *thinks* they whitelisted everything
- But maybe they missed a domain (like `atlassian-cdn.com` or `*.okta.com`)
- Or whitelisted incorrectly (typo, wrong subdomain)

---

## Quick Summary for Interview

**SameSite=Strict** → Breaks redirects (cookies not sent cross-site)
**Third-party cookie blocking** → Breaks cross-domain communication
**Whitelist misconfiguration** → Some required domain isn't allowed

**All three can break SSO!**

---

**Ready to use DevTools to prove which one is the culprit?** 🎤
