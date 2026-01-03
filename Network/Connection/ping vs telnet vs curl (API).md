## ping
they both test basic connectivity but just send ICMP pockets to server and not make http/https connection. If this fails, it means their server is down or there is network routing problem between
us or theres firewall blocking ICMP pockets on their side for security or DNS issue where their hostname cant be resolved.
**it doesnt mean their server is 100% down cuz they might have blocked ping for security issues**
```
ping google.com          # Test connectivity
```

## telnet
its diff from ping and it uses TCP protocol with specific port. While ping checks for host reachability, telnet checks for port reachability. For example, if I'm troubleshooting a database connection, ping might succeed because the server is online, but telnet to port 3306 might fail if the database service isn't running or a firewall is blocking that port. So ping tests 'is the server there?' while telnet tests 'is this specific service accessible?
```
telnet hostname 8080     # Test port connectivity
```

## curl
### basic connection (HEAD request)
HEAD request = GET request but returns ONLY headers, NO body content
```
## -I = headers only and exclude response body
curl -I https://google.com

HTTP/2 200
content-type: text/html; charset=ISO-8859-1
date: Sat, 03 Jan 2025 10:30:00 GMT
server: gws
```

### get
```
## -u = authentiucation where u give username and password in xx:yy format after
curl -u user:pass https://jira.com/rest/api/2/issue/PROJ-123
```

### post
```
## -X METHOD = Specify HTTP method
## -H = add header
## -d = send data (body)
curl -X POST -u user:pass \
-H "Content-Type: application/json" \
-d '{"fields":{"project":{"key":"PROJ"},"summary":"Test"}}' \
https://jira.com/rest/api/2/issue
```
