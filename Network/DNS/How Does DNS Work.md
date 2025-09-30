## DNS (Domain Name System)
DNS translates human-readable domain names(like facebook.com) into IP addresses

1) Browser doesnt directly query DNS server directly but asks OS/stub resolver(software component built into ur OS that
handles DNS lookups).
2) OS resolver first checks local cache or hosts file for this
3) If not found, resolver forwards request to **recursive DNS server** like ISP's DNS or public DNS like Google DNS 8.8.8.8
4) If still not found, recursive DNS server queries root DNS server, which points to the Top-Level Domain (TLD) server like *.com*
5) The TLD server manages info for their domains like .com. While it doesnt know the IP address of facebook.com, it knows
**which authoritative DNS servers** are responsible for facebook.com
6) Authoritative server returns this IP address to recursive DNS server, which stores the result temporarily with TTL and returns
the IP address to OS resolver, which gives to browser


## nameserver
Its primary job is to translate human-friendly domain names (like google.com) into the numeric Internet Protocol (IP) addresses (like 142.250.68.14) that computers need to find each other on the network. There are 2 types - recurisve and authoritative.

Recursive DNS server = goes around the internet asking others to resolve the query for you.

Authoritative DNS server = has the official, correct answer because it stores the domain’s DNS records.

## What happens when u enter a domain into browser?
1) Browser checks cache (all that above explanation)
2) browser establishes tcp conenction
3) tls handshake for https
4) sends https request
5) facebook servers respond with html content
6) browser parses html and makes addition requests for CSS, Javascript, images and other resources
7) rendering engine builds Document Object Model (DOM) & CSS object model (CSSOM), combines them to Render tree and
paints. Painting is where browser draws actual pixels on screen
8)   Javascript executed, page displayed

## recursive vs iterative query
In a typical DNS resolution process, your computer sends a recursive query to your local DNS resolver, and then that local resolver performs a series of iterative queries to the rest of the DNS system to get the final answer for you.

## Technical Explanation

### **Recursive Query** = "Do all the work for me"

```
Your Computer → DNS Resolver
   "What's the IP for www.example.com? 
    Don't come back until you have the answer!"

[Resolver does all the work...]

Your Computer ← DNS Resolver
   "Here's the IP: 93.184.216.34"
```

**Key characteristics:**
- Client asks ONE question
- Client waits for ONE final answer
- The DNS resolver is **responsible** for finding the complete answer
- Called "recursive" because the resolver might recursively ask itself "what's the IP for the nameserver I need to contact next?"

### **Iterative Query** = "Point me in the right direction"

```
DNS Resolver → Root Server
   "What's the IP for www.example.com?"

DNS Resolver ← Root Server
   "I don't know, but ask the .com nameserver at 192.5.6.30"

DNS Resolver → .com TLD Server
   "What's the IP for www.example.com?"

DNS Resolver ← .com TLD Server
   "I don't know, but ask example.com's nameserver at 199.43.135.53"

DNS Resolver → example.com Nameserver
   "What's the IP for www.example.com?"

DNS Resolver ← example.com Nameserver
   "Here's the answer: 93.184.216.34"
```

**Key characteristics:**
- Resolver asks multiple questions
- Each response is a **referral** (pointer to next server) or the final answer
- Resolver must do the iteration itself
- Called "iterative" because it's literally iterating through multiple servers

## Why the Names?

### **Recursive** 
The term comes from the resolver's perspective. When you make a recursive query, you're asking the resolver to recursively solve the problem - even if that means the resolver needs to recursively figure out intermediate steps (like finding the nameserver's IP address before querying it).

It's "recursive" because the burden of resolution is **passed down** to the next party, who must fully resolve it.

### **Iterative**
The resolver iterates through multiple nameservers, making repeated queries until it gets the final answer. It's a loop/iteration process.

## Visual Flow

```
┌─────────────┐
│ Your PC     │ 
└──────┬──────┘
       │ "What's example.com?" 
       │ (RECURSIVE - give me final answer)
       ↓
┌─────────────────────┐
│ DNS Resolver        │ ← This does all the iteration
│ (8.8.8.8)          │
└──────┬──────────────┘
       │ "What's example.com?"
       │ (ITERATIVE - just give me a hint)
       ↓
┌─────────────────────┐
│ Root Server         │
└──────┬──────────────┘
       │ "Try .com server at 192.5.6.30"
       ↓
┌─────────────────────┐
│ .com TLD Server     │
└──────┬──────────────┘
       │ "Try example.com NS at 199.43.135.53"
       ↓
┌─────────────────────┐
│ example.com NS      │
└──────┬──────────────┘
       │ "Here's the IP: 93.184.216.34"
       ↓
┌─────────────────────┐
│ DNS Resolver        │
└──────┬──────────────┘
       │ "Here's the IP: 93.184.216.34"
       ↓
┌─────────────┐
│ Your PC     │
└─────────────┘
```

## Testing This Yourself

**See recursive behavior (you → resolver):**
```bash
dig example.com @8.8.8.8
# You get the final answer immediately
```

**See iterative behavior (simulate what resolver does):**
```bash
# Step 1: Ask root server
dig example.com @a.root-servers.net

# Response tells you to ask .com server
# Step 2: Ask .com server
dig example.com @a.gtld-servers.net

# Response tells you to ask example.com's nameserver
# Step 3: Ask authoritative nameserver
dig example.com @a.iana-servers.net

# Now you get the actual IP
```

**Or use +trace to see the whole iterative process:**
```bash
dig +trace example.com
# Shows each step of iteration
```
