1) dig
dig example.com queries the **default nameserver** that is configured on ur PC. 
```
; <<>> DiG 9.18.1 <<>> example.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; QUESTION SECTION:
;example.com.                   IN      A
# ↑ What we asked for: A record (IPv4 address) for example.com

;; ANSWER SECTION:
example.com.            86400   IN      A       93.184.216.34
# ↑ domain               ↑ TTL  ↑ class ↑ type  ↑ IP address
#                      (24hrs)

;; Query time: 23 msec
# ↑ How long the query took

;; SERVER: 192.168.1.1#53(192.168.1.1)
# ↑ Which DNS server answered (your default resolver)

;; WHEN: Tue Sep 30 10:15:23 KST 2025
;; MSG SIZE  rcvd: 56

```

for troubleshooting: but `dig @8.8.8.8 example.com` queries the **specific nameserver** at its IP address 8.8.8.8 (Google Public DNS). Normally dig @8.8.8.8 example.com is for troubleshooting check. If we recently changed an ip address of a domain, and we wanna see if that change has been propagated to well-known external DNS server. **If dig example.com gives the old IP, but dig @8.8.8.8 example.com gives the new IP, it means the change has propagated, but your local DNS server (or cache) hasn't updated yet.**

solution:
flush my local DSN cache of my PC 
```
sudo resolvectl flush-caches
```

theres also **dig +trace example.com - Show full resolution path**

```bash
dig +trace example.com
```

**What it does:**
- Shows the **entire iterative query process**
- Starts from root servers
- Shows each step: Root → TLD → Authoritative
- Simulates what your DNS resolver does behind the scenes

**Example output:**
```bash
dig +trace example.com

# Step 1: Root servers
.                       518400  IN      NS      a.root-servers.net.
.                       518400  IN      NS      b.root-servers.net.
# ... more root servers

# Step 2: Query root, get .com TLD nameservers
com.                    172800  IN      NS      a.gtld-servers.net.
com.                    172800  IN      NS      b.gtld-servers.net.
# ... more .com servers

# Step 3: Query .com TLD, get example.com nameservers
example.com.            172800  IN      NS      a.iana-servers.net.
example.com.            172800  IN      NS      b.iana-servers.net.

# Step 4: Query example.com nameserver, get final answer
example.com.            86400   IN      A       93.184.216.34
```

**When to use +trace:**
- Debugging DNS propagation issues
- Finding where DNS resolution fails
- Understanding the full DNS hierarchy
- Checking delegation from parent to child zones

**Important notes:**
- `+trace` queries are **NOT cached** - goes to authoritative sources
- Takes longer because it does full resolution
- May fail if firewalls block queries to root/TLD servers

#### **Useful dig options:**

```bash
# Short output (just the answer):
dig +short example.com
# Output: 93.184.216.34

# Query specific record type:
dig example.com A        # IPv4 address
dig example.com AAAA     # IPv6 address
dig example.com MX       # Mail servers
dig example.com NS       # Nameservers
dig example.com TXT      # Text records
dig example.com CNAME    # Canonical name (alias)

# Query all records:
dig example.com ANY

# Reverse DNS lookup (IP to hostname):
dig -x 93.184.216.34

# Show only answer section:
dig +noall +answer example.com

# Verbose output:
dig +trace +additional example.com

# Use TCP instead of UDP:
dig +tcp example.com

# Set custom timeout:
dig +time=2 example.com
```

### **2. nslookup (Name Server Lookup)** tbc!!!!

Older, simpler tool. Less detailed than dig but more portable.

```bash
nslookup example.com
```

**Output:**
```bash
Server:         192.168.1.1        # DNS server used
Address:        192.168.1.1#53     # Server IP and port

Non-authoritative answer:          # Answer came from cache
Name:   example.com
Address: 93.184.216.34
```

**Interactive mode:**
```bash
nslookup
> example.com                      # Query domain
> set type=MX                      # Change query type
> example.com                      # Query again for MX records
> server 8.8.8.8                   # Switch DNS server
> example.com                      # Query using new server
> exit
```

**Query specific server:**
same as dig example.com @8.8.8.8
```bash
nslookup example.com 8.8.8.8
```

**Specific record types:**
```bash
nslookup -query=A example.com      # IPv4
nslookup -query=AAAA example.com   # IPv6
nslookup -query=MX example.com     # Mail
nslookup -query=NS example.com     # Nameservers
```

**Reverse lookup:**
```bash
nslookup 93.184.216.34
```

### **3. host - Quick lookup**

Simplest DNS tool. Good for quick checks.

```bash
host example.com
```

**Output:**
```bash
example.com has address 93.184.216.34
example.com has IPv6 address 2606:2800:220:1:248:1893:25c8:1946
```

**Query specific record:**
```bash
host -t A example.com              # IPv4
host -t AAAA example.com           # IPv6
host -t MX example.com             # Mail servers
host -t NS example.com             # Nameservers
host -t TXT example.com            # Text records
```

**Reverse lookup:**
```bash
host 93.184.216.34
```

**Verbose output:**
```bash
host -v example.com
# Shows query and full response
```

**Query specific server:**
```bash
host example.com 8.8.8.8
```

---

## Tool Comparison

| Feature | dig | nslookup | host |
|---------|-----|----------|------|
| Detail level | High | Medium | Low |
| Output format | Verbose, structured | Medium, readable | Minimal |
| Trace capability | Yes (+trace) | No | No |
| Interactive mode | No | Yes | No |
| Best for | Debugging, detailed analysis | Quick checks, interactive | Simple lookups |
| Cache indication | Shows TTL | Shows "Non-authoritative" | No indication |
| Modern | Yes, actively maintained | Legacy, but still works | Simple, stable |

## Practical Examples

### **Debugging slow website:**

```bash
# 1. Check if DNS is the problem:
time dig example.com
# If "Query time" is >100ms, DNS might be slow

# 2. Try different DNS servers:
time dig @8.8.8.8 example.com      # Google
time dig @1.1.1.1 example.com      # Cloudflare
time dig @208.67.222.222 example.com  # OpenDNS

# 3. Check if it's a caching issue:
dig example.com
# Look at TTL - if very low, cache expires quickly
```

### **Checking DNS propagation:**

```bash
# After changing DNS records, check if propagated:
dig +trace example.com

# Check from different DNS servers:
dig @8.8.8.8 example.com +short
dig @1.1.1.1 example.com +short
# If different, propagation still in progress
```

### **Finding mail servers:**

```bash
dig example.com MX +short
# Output: 10 mail.example.com.
#         ↑ priority (lower = higher priority)
```

### **Checking if domain exists:**

```bash
dig example.com
# status: NOERROR = exists
# status: NXDOMAIN = doesn't exist
```

### **Troubleshooting "website not found":**

```bash
# 1. Can you resolve it?
dig example.com

# 2. Does it resolve from public DNS?
dig @8.8.8.8 example.com

# 3. Is the authoritative server responding?
dig +trace example.com

# 4. Is it a local cache issue?
dig example.com @localhost  # Skip local cache
```

