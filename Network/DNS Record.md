## DNS record
piece of info in DNS that maps domain name to something useful like IP address or service. Its purpose is to tell
internet how to route traffic for a domain

### A record
domain -> ipv4 address

```
example.com.   IN   A     192.0.2.1       # IPv4 address
example.com.   IN   AAAA  2001:db8::1    # IPv6 address
```

### AAAA record
domain -> ipv6 address

### CNAME
domain -> another domain (alias)

### NS
specifies the *authoriatative name servers* for the domain

