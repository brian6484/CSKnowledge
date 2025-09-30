
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
