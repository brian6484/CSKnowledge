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


## Diff between servers
Recursive DNS server = goes around the internet asking others to resolve the query for you.

Authoritative DNS server = has the official, correct answer because it stores the domain’s DNS records.
