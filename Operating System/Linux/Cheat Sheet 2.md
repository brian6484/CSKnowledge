## Scenario
So this cheat sheet assumes you have access to SSH and can type commands to diagonose Server issue. Server reources can be divided mainly
into 4 parts - CPU, memory, IO and network. 

### Starting point
```
dmesg | tail -10
vmstat 1 3
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpin swpout   bi   bo   in   cs us sy id wa st
 2  8      0      0   45  892  1234 5678 12  8 40 38  2
 3 12      0      0  234 1892  2345 9876 15 10 35 38  2
 4 15      0      0  678 2145  2891 11234 18 12 30 38  2
```
b = blocked processes (normally to be below 5)

si,so = swapping memory to disk, shows system is running out of memory

bi,bo = disk i/o operations per second, shows system is v busy with i/o

in = interrupts/s. Remember interrupt is when a device (disk, network, timer) needs CPU's attention or resource

cs = context switches/s. Every time kernel switches from 1 process to another. **May suggest thrashing** cuz many porcesses are competing
for cpu and i/o so they are constantly getting swapped in and out.

### CPU
notice iostat shows **BOTH CPU AND DISK I/O"" metrics! So its useful for cpu and disk i/o checking 
```
iostat -xz 5
```

### IO
-x = extended statistics and -z means skip devices with zero activity! Thats y we dont disks that are doing i/o, not every disk.
```
# check disk io
# Show extended disk I/O statistics AND cpu usage every 5 seconds until manually stopped
iostat -xz 5
Linux 5.15.0-97-generic (webserver01)  10/16/2025  _x86_64_  (4 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
          12.34    0.12    8.45   38.67    2.10   38.32

Device            r/s     w/s     rMB/s   wMB/s r_await w_await aqu-sz  %util
sda             145.2   892.3    12.34  128.56   2.34   45.67   8.92   98.45
sda1            145.2   892.3    12.34  128.56   2.34   45.67   8.92   98.45

# identify io heavy processes (top 5)
iotop
Total DISK READ:        12.34 M/s | Total DISK WRITE:      128.56 M/s
  PID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN     IO>    COMMAND
18492  be/4  postgres    0.00 B/s  124.34 M/s  0.00 %  45.23 % postgres: checkpoint
22341  be/4  root        0.00 B/s    2.12 M/s  0.00 %   8.34 % java -jar app.jar
31245  be/4  syslog      1.23 M/s    0.34 M/s  0.00 %   5.67 % rsyslog
```

so notice for iostat -xz 5, the disk i/o, specifically %util is maxed out at 98%. It cant handle more i/o requests.

iotop's `IO>` column shows **percentage of time** process is spending doing I/O operations.

`DISK WRITE` column means that postgres is writing to disk at 124M/s. Because the disk is saturated and cannot meet the demand immediately, new requests must wait in a queue, causing the average write latency to spike to $45.67\ \text{ms}$. This is the effect.

### Network

### netstat
Firstly theres -an and -tulpn. Which do we use for which case?

-a = shows all sockets (LISTEN and ESTABLISHED)

-n = shows numerical addreeses (and not hostnames)

-t = TCP connections only

-u = UDP connections only

-l = Show only **listening** sockets (NOT ESTABLISHED CONNECTIONS)

we use -an to see connections only and not interested in the programs that are using them.

we use -tulpn to see **which program is using which port**. It requires **sudo** to see the process name and only shows listening
ports by servers

if we wan check network for port 80 ONLY, not 8080. Firstly we just use -an and We need a space after 80, which means only port 80
```
netstat -an | grep ':80 '
```

we can add more greps after like this to count established connections on port 80
```
netstat -an | grep ':80 ' | grep ESTABLISHED | wc -l
```

and we can see who is connecting. Before the awk comand, the 5th column shows the IP address and the port number. We are only interested
in the IP so while we extract the 5th column, we set the delimiter (-d) as the colon (:) and -f1 means the specififer for the first field.
before
```
# 5th column
192.168.1.5:45678
```

```
netstat -an | grep ':80 ' | grep ESTABLISHED | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr | head -10
```

### dig
Its translates dns between domain names and ip addresses. Its bi directional so if we do dig google.com it gives ip address but
```
dig -x 10.0.5.142

The `-x` flag means "reverse lookup" - go from IP to hostname/domain.
```

if it gives an internal Prometheus metric monitoring system and its doing Ddos, and my web server is behind a nginx, we should check
the nginx log via
```
sudo tail -100 /var/log/nginx/access.log | grep 10.0.5.142
```
this is cuz if nginx is proxying requests to my backend app, every prometheus req hits nginx first. Prom -> nginx:80 -> backend:8080/metrics.

to see how fast the metrics are coming in one second at 3.47pm 20 to 29 seconds so 10 seconds
```
sudo tail -1000 /var/log/nginx/access.log | grep 10.0.5.142 | grep "15:47:2[0-9]" | wc -l
```

