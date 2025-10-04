## IP address
We need to know [bit and byte](https://github.com/brian6484/CSKnowledge/blob/main/Network/Bit%20%26%20Byte.md) first. 

IP address is a unique number assigned to every device that is connected to the network that uses Internet Protocol for communication.

Firstly theres IPv4, that has 32 bits (4 bytes), that is written as 4 numbers separated by dots. 

## The Structure: 32 Bits, 4 Bytes, 4 Octets

| Component | Size | Format | Meaning |
| :--- | :--- | :--- | :--- |
| **Total Size** | **32 bits** | N/A | Total number of binary digits. |
| **Total Size** | **4 bytes** | N/A | Since 8 bits = 1 byte. |
| **Segments** | **4 Octets** | N.N.N.N | The 32 bits are divided into four 8-bit segments. An 8-bit segment is called an **octet**. |

### 1. The 32 Bits (Binary)

The "32 bits" means the address is fundamentally a string of 32 ones and zeros (binary digits).
* Example: `11000000 10101000 00000001 00000001` (32 bits long)

### 2. The Four Numbers (Decimal/Dotted-Decimal Notation)

To make it easier for humans to read and remember, the 32 bits are divided into four 8-bit (1 byte) sections, and each 8-bit section is converted into its decimal (base 10) number. These four numbers are then separated by dots.
* Example: The binary string above converts to **`192.168.1.1`**.

Since an 8-bit number can range from $00000000_2$ to $11111111_2$, the decimal value of each of the four numbers can range from **0 to 255** ($2^8 - 1$).

***

## How the Numbers are Used

An IPv4 address is divided into two main parts:

1.  **Network Part:** Identifies the specific network the device is on (like a city or neighborhood).
2.  **Host Part:** Identifies the specific device *within* that network (like a specific house address).

The boundary between the Network and Host parts is determined by a **Subnet Mask**, which is a whole other topic! But the basic idea is that this structure allows routers to efficiently direct data packets to the correct network and then to the correct device.



**In short: An IPv4 address is a four-part number, where each part is between 0 and 255, that uniquely identifies a device on a network.**

Let me know if you want to dive deeper into any of these concepts, like the subnet mask, or learn about the differences with IPv6! 🚀
That's a great starting point for learning about **IPv4**! You're right:
> **An IPv4 address consists of 32 bits (4 bytes), written as four numbers separated by dots.**

The confusion often comes from understanding how those 32 bits are actually organized and why they are written as four decimal numbers (the "dotted-decimal notation").

Here is a simplified breakdown of the core concepts:

***

## 1. Binary vs. Decimal Format

An IPv4 address is fundamentally a **32-bit binary number**. However, a 32-digit string of 1s and 0s is extremely hard for a human to read.

* **Binary:** `11000000101010000000000100000001`
* **Decimal (Dotted-Decimal Notation):** `192.168.1.1`

To make it readable, the 32 bits are divided into four groups of **8 bits**. A group of 8 bits is called an **octet**. Each octet is then converted from binary to its decimal equivalent, and the four resulting numbers are separated by dots.

| Group | Octet 1 | Octet 2 | Octet 3 | Octet 4 |
| :---: | :---: | :---: | :---: | :---: |
| **Bits** | 8 bits | 8 bits | 8 bits | 8 bits |
| **Total** | \multicolumn{4}{c|}{32 bits} |
| **Decimal Range**| **0** to **255** | **0** to **255** | **0** to **255** | **0** to **255** |

Since a single octet (8 bits) can represent a decimal number from 0 to 255 ($2^8$ combinations), no number in an IPv4 address will ever be greater than 255.

***

## 2. The Two Primary Parts

Every IPv4 address has two logical parts that network routers use to determine where to send data:

### A. Network Portion (or Network ID/Prefix)
This part identifies the **specific network** on which the device resides (like a street or neighborhood). All devices on the same local network will share the *same* Network Portion of the address.

### B. Host Portion (or Host ID/Identifier)
This part uniquely identifies the **specific device** *within* that network (like a house number on a street). Every device on a network must have a unique Host Portion.

### The Role of the Subnet Mask

The crucial piece of the puzzle is the **Subnet Mask**. It is another 32-bit number (like `255.255.255.0` or `/24` in CIDR notation) that tells the router **where the Network Portion ends and the Host Portion begins**.

| IP Address | `192` . `168` . `1` . `10` | (Example Device IP) |
| :---: | :---: | :---: |
| **Subnet Mask** | `255` . `255` . `255` . `0` | (Standard home network mask) |
| **Network Portion** | **192.168.1** | (Determined by the 255s in the mask) |
| **Host Portion** | **.10** | (Determined by the 0 in the mask) |

This hierarchical structure (Network first, then Host) is what allows for efficient data routing across the internet.

***
Understanding IPv4 Address Structure is a video that goes over the basics of how the 32-bit IPv4 address is organized into its network and host parts.
