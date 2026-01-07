**CIDR (Classless Inter-Domain Routing)** is a compact way to specify IP address ranges.

## **Format:**
```
192.168.1.0/24
```
- `192.168.1.0` = network address
- `/24` = number of bits in the network prefix (subnet mask)

## **How it works:**

The `/24` means the first 24 bits are the network, remaining 8 bits are for hosts.

**Examples:**

- `192.168.1.0/24` 
  - Subnet mask: `255.255.255.0`
  - Range: `192.168.1.0` - `192.168.1.255` (256 addresses)
  
- `10.0.0.0/8`
  - Subnet mask: `255.0.0.0`
  - Range: `10.0.0.0` - `10.255.255.255` (16 million addresses)

- `192.168.1.0/32`
  - Single specific IP address (all 32 bits fixed)

## **Quick reference:**
- `/32` = 1 address (single host)
- `/24` = 256 addresses (common for small networks)
- `/16` = 65,536 addresses
- `/8` = 16 million addresses

**Why it's useful:** Instead of saying "192.168.1.0 through 192.168.1.255", you just say `192.168.1.0/24`. Much more efficient for routing tables and firewall rules.

## subnet mask
IP:   192.168.1.10
Mask: 255.255.255.0
AND operation → Network: 192.168.1.0
