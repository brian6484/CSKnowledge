## 502 Bad gateway
502 Bad Gateway means the reverse proxy itself is working, but it can't reach the backend application. The proxy received my request but when it tried to forward it to the backend server, it got no response—maybe the app crashed, the port isn't listening, or there's a network/firewall issue between the proxy and backend. If the proxy itself was down, I'd get a **connection refused or timeout error** 
instead of 502.

## 503 service unavailable
this means the server itself is down or is currently incapable of serving req. Unlike 500 which occurs due to application logic issues, 503 is the server's incapacity.
