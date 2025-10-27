These are both ways of describing IP addresses and network ranges! Let me break them down:

## `10.0.0.0/26`

This is **CIDR notation** (Classless Inter-Domain Routing). It defines a range of IP addresses:

- **10.0.0.0** is the network address (the starting point)
- **/26** means "the first 26 bits are the network portion"

Since IP addresses are 32 bits total, this leaves 32 - 26 = **6 bits for hosts**. With 6 bits, you can have 2^6 = 64 addresses in this range:
- Range: `10.0.0.0` to `10.0.0.63`
- Usable for devices: `10.0.0.1` to `10.0.0.62` (62 addresses)
- The first address is the network address, and the last is the broadcast address

## `255.255.255.0`

This is a **subnet mask**. It's another way to define which part of an IP address is the network vs. the host.

In binary, `255.255.255.0` looks like:
```
11111111.11111111.11111111.00000000
```

The `1`s indicate the network portion, and the `0`s indicate the host portion. So:
- First 24 bits = network portion
- Last 8 bits = host portion (allowing 2^8 = 256 addresses, with 254 usable)

**This is equivalent to /24 in CIDR notation.**

## Quick Comparison

- `10.0.0.0/24` = subnet mask `255.255.255.0` → 256 addresses
- `10.0.0.0/26` = subnet mask `255.255.255.192` → 64 addresses
- `10.0.0.0/16` = subnet mask `255.255.0.0` → 65,536 addresses

The `/` number (CIDR) is more compact and commonly used in modern networking, while subnet masks are the older notation. Both describe the same thing!
