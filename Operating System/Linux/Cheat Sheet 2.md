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

si,so = swapping memory to disk

bi,bo = disk i/o operations per second 

in = interrupts/s. Remember interrupt is when a device (disk, network, timer) needs CPU's attention or resource

cs = context switches/s. Every time kernel switches from 1 process to another. **May suggest thrashing** cuz many porcesses are competing
for cpu and i/o so they are constantly getting swapped in and out.

### CPU
notice iostat shows **BOTH CPU AND DISK I/O"" metrics! So its useful for cpu and disk i/o checking 
```
iostat -xz 5
```

### IO
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





