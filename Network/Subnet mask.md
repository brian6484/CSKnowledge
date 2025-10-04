### What is a Subnet Mask?

A **subnet mask** divides an IP address into two parts:
1. **Network portion** - identifies which network the device is on
2. **Host portion** - identifies the specific device on that network

Think of it like a street address:
- **Network = Street name** (identifies which neighborhood)
- **Host = House number** (identifies which house on that street)

#### Example: 255.255.255.0

```
IP Address:    192.168.1.100
Subnet Mask:   255.255.255.0

In binary:
IP:            11000000.10101000.00000001.01100100
Mask:          11111111.11111111.11111111.00000000
               └────────Network────────┘ └─Host─┘
```

**What does this mean?**
- `255.255.255.0` means the **first 3 octets (24 bits) identify the network**
- The **last octet (8 bits) identifies the host**
- All devices with IPs `192.168.1.X` are on the same network
- You can have 256 devices (0-255), but 2 are reserved:
  - `192.168.1.0` = Network address
  - `192.168.1.255` = Broadcast address
  - **254 usable host addresses**

#### Common Subnet Masks

| Subnet Mask     | Binary                              | CIDR | Networks | Hosts per Network |
|-----------------|-------------------------------------|------|----------|-------------------|
| 255.0.0.0       | 11111111.00000000.00000000.00000000 | /8   | Few      | 16,777,214        |
| 255.255.0.0     | 11111111.11111111.00000000.00000000 | /16  | More     | 65,534            |
| 255.255.255.0   | 11111111.11111111.11111111.00000000 | /24  | Many     | 254               |
| 255.255.255.128 | 11111111.11111111.11111111.10000000 | /25  | More     | 126               |

### CIDR Notation (Classless Inter-Domain Routing)

CIDR is a **shorthand** for writing subnet masks using a slash and a number.

#### Format: `IP_ADDRESS/NUMBER`

The number represents **how many bits are used for the network portion**.

#### Example 1: `192.168.1.0/24`

```
/24 means 24 bits for network, 8 bits for hosts

Binary subnet mask:
11111111.11111111.11111111.00000000
└──────24 ones (network)──┘ └8 zeros┘

This is the same as: 255.255.255.0

Network: 192.168.1.0
Range: 192.168.1.1 to 192.168.1.254
Broadcast: 192.168.1.255
Total IPs: 2^8 = 256
Usable IPs: 254
```

#### Example 2: `10.0.0.0/26`

```
/26 means 26 bits for network, 6 bits for hosts

Binary subnet mask:
11111111.11111111.11111111.11000000
└──────26 ones (network)──┘ └6 zero┘

This is the same as: 255.255.255.192

Network: 10.0.0.0
Range: 10.0.0.1 to 10.0.0.62
Broadcast: 10.0.0.63
Total IPs: 2^6 = 64
Usable IPs: 62
```

**Why /26?**
- You want a smaller network (only 62 devices)
- More security (smaller broadcast domain)
- Better network organization

#### Example 3: `172.16.0.0/16`

```
/16 means 16 bits for network, 16 bits for hosts

Binary subnet mask:
11111111.11111111.00000000.00000000
└──16 ones──┘ └──16 zeros (hosts)──┘

This is the same as: 255.255.0.0

Network: 172.16.0.0
Range: 172.16.0.1 to 172.16.255.254
Broadcast: 172.16.255.255
Total IPs: 2^16 = 65,536
Usable IPs: 65,534
```

### Quick Formula

For CIDR notation `/N`:
- **Network bits:** N
- **Host bits:** 32 - N
- **Total IP addresses:** 2^(32-N)
- **Usable IP addresses:** 2^(32-N) - 2

### Practical Examples

#### Is 192.168.1.50 on the same network as 192.168.1.100?

With `/24` (255.255.255.0):
```
192.168.1.50  → Network: 192.168.1.0 ✓
192.168.1.100 → Network: 192.168.1.0 ✓
Same network!
```

With `/26` (255.255.255.192):
```
192.168.1.50  → Network: 192.168.1.0  (range 1-62)
192.168.1.100 → Network: 192.168.1.64 (range 65-126)
Different networks!
```

### Linux Commands to Check Network Info

```bash
# Show IP address and subnet mask
ip addr show
ifconfig

# Show routing table
ip route
route -n

# Test if IP is reachable
ping 192.168.1.1

# Check network connectivity
traceroute google.com

# Show network statistics
netstat -i
ss -s
```

Fewer network bits (/8) = Fewer slices, more addresses per slice
```

**Key Takeaway:** 
- **Subnet mask (255.255.255.0)** = The long way to write it
- **CIDR notation (/24)** = The short way to write it
- Both mean the same thing: how much of the IP is the network vs. the host
