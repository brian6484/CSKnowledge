## traceroute 
It measures the path that packets take to a destination. It sends probe packets, which can be tcp, udp, etc
and while they dont carry app data, its purpose is to **see each hop on network path and measure round-trip time**.
The TTL field in the IP header is manipulated along the way

## example
so ttl is like "how many hops (routers) am i allowed to travel before dying" and it is in packet ip header. 
1) traceroute first sends packet with ttl=1. First router receives and decrements this ttl. Since ttl =0, router sends back
ICMP "time exceeded" message. Now traceroute knows the first hop and how long it took to reach it
2) traceroute sends another with ttl=2. First router sends to second router with ttl=1.
3) traceroute thus increases ttl by 1 until packet receives final destination (no time exceeded error message)
