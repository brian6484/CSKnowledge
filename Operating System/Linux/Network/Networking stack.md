## Networking stack
Its more or less the same as the OSI layer

```
┌─────────────────────────────────────────────────────────┐
│ USER SPACE                                              │
├─────────────────────────────────────────────────────────┤
│ OSI Layer 7: Application Layer                          │
│   Your app: web browser, ssh client, etc.              │
│   Libraries: libcurl, OpenSSL                           │
├─────────────────────────────────────────────────────────┤
│ OSI Layer 6: Presentation Layer                         │
│   Usually handled by userspace libraries               │
│   OpenSSL/TLS, data encoding/decoding                  │
├─────────────────────────────────────────────────────────┤
│ OSI Layer 5: Session Layer                             │
│   Usually handled in userspace                         │
│   Application manages sessions                         │
└─────────────────────────────────────────────────────────┘
                         ↓ syscalls
╔═════════════════════════════════════════════════════════╗
║ KERNEL SPACE                                            ║
╠═════════════════════════════════════════════════════════╣
║ Socket Layer (Interface between user/kernel)           ║
║   - File descriptors, socket buffers (sk_buff)         ║
║   - BSD socket API implementation                      ║
╠═════════════════════════════════════════════════════════╣
║ OSI Layer 4: Transport Layer                           ║
║   TCP:  net/ipv4/tcp*.c                                ║
║   UDP:  net/ipv4/udp.c                                 ║
║   - Port numbers, connections, flow control            ║
╠═════════════════════════════════════════════════════════╣
║ OSI Layer 3: Network Layer                             ║
║   IP:   net/ipv4/ip_*.c, net/ipv6/                     ║
║   ICMP: net/ipv4/icmp.c                                ║
║   - IP addresses, routing, fragmentation               ║
║   - Netfilter/iptables (packet filtering)              ║
╠═════════════════════════════════════════════════════════╣
║ OSI Layer 2: Data Link Layer                           ║
║   Ethernet: net/ethernet/                              ║
║   ARP:      net/ipv4/arp.c                             ║
║   - MAC addresses, frame handling                      ║
╠═════════════════════════════════════════════════════════╣
║ OSI Layer 1: Physical Layer                            ║
║   Network Drivers: drivers/net/                        ║
║   - e1000, ixgbe, r8169, etc.                          ║
║   - Direct hardware (NIC) control                      ║
╚═════════════════════════════════════════════════════════╝
                         ↓
                    Hardware (NIC)
```

