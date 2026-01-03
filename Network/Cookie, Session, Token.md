## Cookie
cookie, which saves state on the client side via `set-cookie` header in HTTP. But since it stored on client side it is more prone to attacks.

## Session
Session saves state on server side. But even sessions are prone to attacks and can pose in-memory issues since we need to store each session on the server. 

How they work together:

1) User logs in
2) Server creates a session (stores user data server-side)
3) Server sends a cookie with the session ID to browser
4) Browser sends this cookie with every request
5) Server uses session ID to retrieve session data

Example:

Cookie: sessionid=abc123 (just the ID)
Session: {userId: 42, username: "john", isAdmin: true} (actual data on server)

For example, in session hijacking/sniffing (this is example of BOTH cookie and session unsafety), when user authenticates(logins), session allocates a *session id*, which is a unique identifier for each user's session. It is either stored as cookie in client-side or passed in url to server, which is sent to server with each request. But if an unsafe network is used like HTTP instead of HTTPS, hacker can intercept this session ID and falsely impersonate to request resources from server with this intercepted session ID.

## Token
Finally there is token like OAuth and JWT.
