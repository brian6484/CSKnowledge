## And also why is LB infront of API Gateway?
```
Internet Traffic (10M requests/sec)
        ↓
Load Balancer: "Which data center? Which API Gateway instance?"
        ↓
API Gateway: "Which microservice? Is user authorized? Rate limit check?"
        ↓
Story Service: "Business logic for stories"
```

With LB at first to intercept client req, there can be geographical routing like if user is in Europe, u can route to Europe data center. Not only that, if api gateway instance is under high heavy traffic, it can send req to other instances. For SSL termination which is CPU intensive, LB can also handle this part.

If gateway is in front of LB, it is single point of failure. The gateway might also be in another completely diff region from the user like if japanese sends request and gateway is in US, then theres this extra latency. You can have a cluster of api gateways but Load balancers are infrastructure components (think "dumb traffic distribution")
API Gateways are application components (think "smart request routing")
You always want to handle infrastructure concerns (geographic routing, instance health, SSL) before application concerns (authentication, business routing).

But actually what if u wanna rate limit requests? that can be only done by gateway right?

Actually no. LB like Nginx/Cloudflare can also rate limit like DDOS attacks
```
# Load Balancer (Nginx/CloudFlare)
http {
    limit_req_zone $binary_remote_addr zone=ip_limit:10m rate=100r/s;
    
    location / {
        # Block malicious IPs BEFORE they reach API Gateway
        limit_req zone=ip_limit burst=20 nodelay;
        
        # Block during DDoS attacks
        if ($request_time > 5s) {
            return 429;
        }
        
        proxy_pass http://api_gateway_pool;
    }
}
```

so it stops attacks early before they reach the expensive API gateway and that gateway doesnt waste CPU resources on blocked requests.
```
Load Balancer Rate Limiting:
- Basic IP-based limits (1000 req/sec per IP)
- Geographic rate limiting  
- DDoS protection

API Gateway Rate Limiting:
- User-based limits (different tiers: free/premium)
- API endpoint specific limits (/upload vs /view)
- Business logic rate limiting
```
