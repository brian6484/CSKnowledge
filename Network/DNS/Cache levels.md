# DNS Caching & Tools Explained

## DNS Caching at Multiple Levels
DNS results are cached at multiple points to make lookups faster and reduce load on DNS servers.

### Even before browser cache - etc/hosts file (OS-level host file)
it bypasses all the dns if u have Hard-coded IP-to-hostname mappings in this file
```
127.0.0.1  example.com
```

### **1. Browser Cache**
- **First place checked** when you type a URL
- Each browser maintains its own DNS cache
- Typically short TTL (Time To Live): 60 seconds to a few minutes
- Separate from OS cache

**View Chrome's DNS cache:**
```
chrome://net-internals/#dns
```

**Clear browser DNS cache:**
- Chrome: Go to chrome://net-internals/#dns and click "Clear host cache"
- Firefox: Restart browser or clear all cache

### **2. Operating System Cache**
- **Second level** - checked if browser cache misses
- Managed by OS DNS resolver
- Usually respects TTL from DNS records

**Linux:**
```bash
# Most Linux systems don't cache by default unless systemd-resolved or nscd is running

# Check if systemd-resolved is caching:
systemd-resolve --statistics

# Clear systemd-resolved cache:
sudo systemd-resolve --flush-caches

# If using nscd (Name Service Cache Daemon):
sudo /etc/init.d/nscd restart
# or
sudo systemctl restart nscd
```

**View OS DNS cache (if systemd-resolved):**
```bash
resolvectl statistics
```

### **3. DNS Resolver Cache (ISP/Google/Cloudflare)**
- **Third level** - your configured DNS server (8.8.8.8, 1.1.1.1, ISP's resolver)
- Largest cache, serves many users
- Caches for the full TTL specified in DNS records
- Can cache for hours or days depending on TTL

**How TTL works:**
```bash
dig example.com

# In response you'll see something like:
example.com.  300  IN  A  93.184.216.34
#             ^^^
#             TTL in seconds (5 minutes)
```

This means the resolver can cache this answer for 300 seconds before querying again.

If this still cant find the result, then we go the entire process from root to tld to authoritative server 

### **Cache Flow Example:**

```
You type "www.example.com"

         /etc/hosts (if there is a mapping)
         ↓
┌─────────────────────┐
│ Browser Cache       │ ← Check here first
└──────┬──────────────┘
       │ MISS
       ↓
┌─────────────────────┐
│ OS Cache            │ ← Check here second
└──────┬──────────────┘
       │ MISS
       ↓
┌─────────────────────┐
│ DNS Resolver Cache  │ ← Check here third
│ (8.8.8.8)          │
└──────┬──────────────┘
       │ MISS
       ↓
┌─────────────────────┐
│ Full DNS Resolution │ ← Only if all caches miss
│ (Root → TLD → Auth) │
└─────────────────────┘
```

**Why multiple levels?**
- **Speed:** Cached responses are milliseconds vs 20-100ms for full resolution
- **Reduced load:** Fewer queries to authoritative nameservers
- **Resilience:** Can still serve cached data if DNS servers are slow/down
