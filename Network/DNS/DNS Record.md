## DNS record
piece of info in DNS that maps domain name to something useful like IP address or service. Its purpose is to tell
internet how to route traffic for a domain

### A record
so when u run `dig`, it sends a query over the network to DNS server, **asking for the A record for example.com**.
domain -> ipv4 address

```
example.com.   IN   A     192.0.2.1       # IPv4 address
example.com.   IN   AAAA  2001:db8::1    # IPv6 address
```

### AAAA record
domain -> ipv6 address

so if question askes in ipv6, what is the A record equivalent? Answer is AAAA record. While A record maps domain to ipv4, AAAA record does the same. the AAAA comes from fact that **ipv6 is 4 times larger than ipv4 address (128 bits vs 32 bits)**! V useful to remember. 

### CNAME
domain -> another domain (alias)

How it works:
blog.example.com  →  CNAME  →  hosting-provider.com

When someone visits blog.example.com, DNS says "actually, go look up hosting-provider.com instead."

Example:
```
blog.example.com    CNAME    myblog.github.io
www.example.com     CNAME    example.com
```
### NS
specifies the *authoriatative name servers* for the domain

