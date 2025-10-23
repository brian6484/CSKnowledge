## What part of the IP and TCP header does traceroute modify?
Actually the primary frield that `traceroute` modifies to determine the route to a destination is in the IP header and TCP header. It modifies the
TTL field of IP header. Destination port number of TCP header is modified. (explained later)

## how traceroute works 
It measures the path that packets take to a destination. It sends probes (data packets), which can be tcp, udp, etc
and while they dont carry app data, its purpose is to **see each hop on network path and measure round-trip time**.
The TTL field in the IP header is manipulated along the way. 

## example
so ttl is like "how many hops (routers) am i allowed to travel before dying" and it is in packet ip header. 
1) traceroute first sends packet with ttl=1. First router receives and decrements this ttl. Since ttl =0, router sends back
ICMP "time exceeded" message. Now traceroute knows the first hop and how long it took to reach it
2) traceroute sends another with ttl=2. First router sends to second router with ttl=1.
3) traceroute thus increases ttl by 1 until packet receives final destination (no time exceeded error message)

## destination port number
So we need to set the destination port to be a high, unlikely to be used value to tell us that signal tracing is complete. Cuz when destination host receives this tcp probe packet, it chceks its list of running apps or srevices and no application should listent to that specific, high port number like 33434. Since the host received a packet for a port that is not open, it sends a ICMP DESTINATION UNREACHABLE mesage with **PORT UNREACHABLE** code back to my computer. This message shows the `traceroute` is successful and we foudn the path.
