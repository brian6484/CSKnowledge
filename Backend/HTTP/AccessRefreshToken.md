## First, need to understand auth flow
1) User logs in - server issues jwt access and refresh token
2) access token is included in api requests
3) access token expires -> client sends the **refresh token** to get a new one
4) new jwt access token is issued

### **Access Token vs. Refresh Token**  

Access tokens and refresh tokens are both used for authentication and authorization in APIs, but they serve different purposes:  

| Feature | **Access Token** | **Refresh Token** |
|---------|---------------|---------------|
| **Purpose** | Grants access to a protected resource (e.g., API) | Used to request a new access token when the old one expires |
| **Lifespan** | Short-lived (minutes to hours) | Long-lived (days to months) |
| **Storage** | Stored in memory (frontend) or secure storage | Secure storage (e.g., HTTP-only cookies, database) |
| **Security Risk** | High (if leaked, attackers can access resources) | Lower (can only be used to get a new access token) |
| **Usage** | Sent in each API request for authentication | Sent only when an access token expires |
| **Example Use Case** | API calls, verifying user identity | Keeping a user logged in without requiring re-authentication |

So unlike its name, refresh token is **long-lived**.
