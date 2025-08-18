## tcpdump usages
When I cannot access example.com

Firstly, use **ipconfig** to know whats ur network interface

1) check if DNS queries are leaving the host
```
sudo tcpdump -i eth0 udp port 53
```
sudo - run with root privelges(needed to capture network packets)
tcpdump - packet capture tool
-i eth0 - listen on network interface eth0 (point of connection between ur PC and network)
udp port 53 - captures only UDP traffic on port 53, which is **used for DNS queries**

When u run that, you get all DNS requests and responses leaving or entering ur computer thru that network interface (etho0 in
this case)

for example
```
14:23:01.123456 IP your_pc.54321 > 8.8.8.8.53: 12345+ A? example.com
14:23:01.234567 IP 8.8.8.8.53 > your_pc.54321: 12345* A 93.184.216.34
```
14:23:01.123456 → timestamp

IP → protocol

your_pc.54321 > 8.8.8.8.53 → source IP/port → destination IP/port

12345+ A? example.com → DNS query type (here, asking for the A record of example.com)

12345* A 93.184.216.34 → DNS server response with the IP address

### extra tip to debug in real-time with nslookup
so technically tcpdump shows u the traffic that passes thru interface **when its running**. So if no DNS requests are happening,
tcpdump wont show anything. Like the above records wont be shown unless a real-time DNS query has passed.

So open a new terminal and do
```
nslookup example.com
```
which generates a **real DNS query from ur machine**. tcpdump then captures the query in real time and shows u the request and
response.

Prob: if dns queries fail, issue with DNS server or firewall

2) Check if TCP handshake is completing
```
sudo tcpdump -i eth0 tcp port 80
```
tcp port 80 captures only TCP traffic on port 80 (http). This commands lets us see SYN,SYN-ACK,ACK, which is the TCP handshake.

But again, to see it in real time, you need to open new terminal and run
```
curl http://example.com
```
or open site in browser to generate real http requests. You will see

```
your_pc.54321 > example.com.80: Flags [S], seq 0
example.com.80 > your_pc.54321: Flags [S.], seq 100, ack 1
your_pc.54321 > example.com.80: Flags [.] , ack 101
```
[S] = SYN
[S.] = SYN-ACK
[.] = ACK

Prob: if tcp handshake fails, issue could be blocked by firewall, server down or routing issue

3) Check for delays or retransmissions
```
sudo tcpdump -i eth0 -tt tcp port 80
```
-tt shows timestamps with microsecond precision and *helps measures dealys between packets*.

So if theres multiple SYN packets that are repeated in the logs -> packet loss or retries
Or if theres large delays between packets (like 2 seconds is considered big) -> network latency

Prob: unstable network connection
