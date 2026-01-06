### The Problem

You want to send a secret message to someone over the internet. How do you encrypt it so only they can read it?

**Old way (symmetric encryption):**
```
You and friend share ONE secret password
├─ You encrypt with password
└─ Friend decrypts with SAME password

Problem: How do you safely share the password in the first place? ❌
```

---

### The Solution: Public/Private Key Pairs

**New way (asymmetric encryption):**

Everyone has TWO keys:
1. **Private key** = Secret key (NEVER share)
2. **Public key** = Public key (share with everyone)

**Magic property:**
```
Data encrypted with public key → can ONLY be decrypted with private key
Data encrypted with private key → can ONLY be decrypted with public key
```

---

### Real World Analogy: Mailbox

**Public key** = Your mailbox (anyone can drop letters in)
**Private key** = Your mailbox key (only you can open and read letters)

```
Friend wants to send you secret message:
1. Friend puts message in YOUR mailbox (uses your public key to encrypt)
2. Only YOU can open mailbox (use your private key to decrypt)
3. Even friend who sent it can't read it once it's in the mailbox!
```

---

### Concrete Example

**Alice wants to send secret to Bob:**

```
Bob generates key pair:
├─ Private key: bob_private.key (keeps secret)
└─ Public key: bob_public.key (gives to everyone)

Alice encrypts message:
Message: "My password is 12345"
    ↓ (encrypt with Bob's public key)
Encrypted: "k8$mK2#pL9@qR3..."
    ↓ (send to Bob)
Bob decrypts:
    ↓ (decrypt with Bob's private key)
Message: "My password is 12345"

Important: Even Alice can't decrypt it once encrypted! Only Bob can.
```

---

### How This Relates to SSL/TLS

**In HTTPS:**

```
Server (Jira) has:
├─ Private key: jira_private.key (SECRET - on server only)
└─ Public key: inside jira.crt certificate (PUBLIC - sent to everyone)

Browser connects:
1. Server sends certificate (contains public key)
2. Browser encrypts session key with server's public key
3. Only server can decrypt (using its private key)
4. Now both have shared session key for fast symmetric encryption
```

---

## NOW: Keystore vs Truststore

### Keystore = Your Private Key + Your Certificate

**What's inside:**
```
┌─────────────────────────────────┐
│         KEYSTORE.JKS            │
├─────────────────────────────────┤
│ YOUR private key (SECRET!)      │
│ YOUR certificate (with public   │
│ key inside)                     │
└─────────────────────────────────┘
```

**Purpose:** Prove WHO YOU ARE when someone connects to you

**Example - Jira as server:**
```
Browser → connects to Jira
          ↓
Jira: "Here's my certificate (with my public key)"
      (Pulls from KEYSTORE)
          ↓
Browser: "Great! Let me encrypt data with your public key"
          ↓
Jira: "I'll decrypt with my private key"
      (Uses private key from KEYSTORE)
```

---

### Truststore = Other People's Certificates (Public Keys)

**What's inside:**
```
┌─────────────────────────────────┐
│      TRUSTSTORE (cacerts)       │
├─────────────────────────────────┤
│ DigiCert Root CA certificate    │
│ Let's Encrypt certificate       │
│ GoDaddy certificate            │
│ Your LDAP server certificate    │
│ (Only public keys, NO private)  │
└─────────────────────────────────┘
```

**Purpose:** Decide WHO TO TRUST when you connect to them

**Example - Jira connecting to LDAP:**
```
Jira → connects to ldaps://ad.company.com:636
       ↓
LDAP: "Here's my certificate (with my public key)"
       ↓
Jira: "Let me check my TRUSTSTORE... is this certificate signed by someone I trust?"
      (Checks TRUSTSTORE for matching CA)
       ↓
If found: "Yes, I trust you!" ✅
If not found: "I don't trust you!" ❌ 
              → Error: "unable to find valid certification path"
```

---

## Visual Comparison

### When You're the SERVER (incoming connections)

```
Browser/User
    ↓ connects to
Jira Server
    ↓ looks in
KEYSTORE
    ├─ jira_private.key (to decrypt)
    └─ jira.crt (to send to browser)
```

**Keystore says:** "This is who I am"

---

### When You're the CLIENT (outgoing connections)

```
Jira (as client)
    ↓ connects to
External API / LDAP
    ↓ receives certificate
Jira checks
    ↓ looks in
TRUSTSTORE
    └─ "Do I have this CA's certificate?"
       ├─ Yes → Trust ✅
       └─ No → Don't trust ❌
```

**Truststore says:** "These are the entities I trust"

---

## Real Scenario: Complete Flow

**Setup:**
- Jira runs on port 8443 (HTTPS with Tomcat)
- Jira connects to LDAP server for authentication

### Incoming: User connects to Jira

```
1. User types: https://jira.company.com:8443
2. Jira pulls from KEYSTORE:
   ├─ jira_private.key
   └─ jira.crt (contains public key)
3. Jira sends jira.crt to user's browser
4. Browser verifies certificate is trusted
5. Browser encrypts session data with Jira's public key
6. Jira decrypts with its private key from KEYSTORE
```

### Outgoing: Jira connects to LDAP

```
1. User tries to login
2. Jira connects to: ldaps://ad.company.com:636
3. LDAP server sends its certificate (with public key)
4. Jira checks TRUSTSTORE:
   "Is LDAP server's certificate (or its CA) in my truststore?"
5. If YES → Jira trusts LDAP, continues ✅
   If NO → Error: "unable to find valid certification path" ❌
```

---

## Why Two Separate Stores?

**Security separation:**

```
KEYSTORE (sensitive):
└─ Contains YOUR private key (extremely sensitive!)
   Must be protected with strong password
   If stolen, attacker can impersonate you

TRUSTSTORE (less sensitive):
└─ Contains only PUBLIC certificates
   No private keys
   If stolen, attacker learns who you trust (not as bad)
```

---

## Common Atlassian Issues

### Issue 1: Missing certificate in TRUSTSTORE

```
Error: "unable to find valid certification path to requested target"

What happened:
├─ Jira trying to connect to external service
├─ External service has self-signed certificate
└─ Certificate not in Jira's TRUSTSTORE

Solution:
keytool -import -alias external-api -file api.crt \
  -keystore $JAVA_HOME/lib/security/cacerts -storepass changeit
```

### Issue 2: Missing certificate in KEYSTORE

```
Error: "Cannot find key entry"

What happened:
├─ Browser trying to connect to Jira
├─ Jira's KEYSTORE is empty or corrupted
└─ Jira can't prove its identity

Solution:
keytool -importkeystore -srckeystore cert.p12 \
  -destkeystore /opt/jira/keystore.jks
```

---

## Memory Tricks

**KEYSTORE:**
- **KEY**store = Your KEYS (private key)
- "K" for "Keep secret"
- YOU prove who YOU are
- Incoming connections

**TRUSTSTORE:**
- **TRUST**store = Who you TRUST
- "T" for "Them" (other people's certs)
- THEY prove who THEY are
- Outgoing connections

---

## Quick Commands Reference

```bash
# View KEYSTORE (your identity)
keytool -list -keystore /opt/jira/keystore.jks -storepass password

# View TRUSTSTORE (who you trust)
keytool -list -keystore $JAVA_HOME/lib/security/cacerts -storepass changeit

# Add to KEYSTORE (your new certificate)
keytool -importkeystore -srckeystore mycert.p12 -destkeystore keystore.jks

# Add to TRUSTSTORE (trust someone new)
keytool -import -alias ldap-server -file ldap.crt \
  -keystore $JAVA_HOME/lib/security/cacerts -storepass changeit
```

---

## Simple Test

**Question:** Jira needs to connect to `https://api.external.com` but gets "unable to find valid certification path" error. Which store do you modify?

<details>
<summary>Answer</summary>

**TRUSTSTORE**

Because Jira is the CLIENT connecting OUT to external API. Jira needs to trust the external API's certificate, so you import it into the TRUSTSTORE.

```bash
# Get certificate
openssl s_client -connect api.external.com:443 </dev/null 2>/dev/null | \
  openssl x509 > external-api.crt

# Import to truststore
keytool -import -alias external-api -file external-api.crt \
  -keystore $JAVA_HOME/lib/security/cacerts -storepass changeit
```
</details>

---

**Does this make sense now?** 

**Core concept:** 
- Private key = secret, only you have
- Public key = everyone can have
- Keystore = your private key (for proving YOU)
- Truststore = others' public keys (for trusting THEM)
