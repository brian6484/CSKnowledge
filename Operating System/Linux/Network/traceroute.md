## What part of the TCP header does traceroute modify?
Actually the primary frield that `traceroute` modifies to determine the route to a destination is in the IP header, not TCP header. It modifies the
TTL field of IP header.

## how traceroute works 
1) ttl manipualtion: 
