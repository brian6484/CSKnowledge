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

The boundary between the Network and Host parts is determined by [subnet mask](). But the basic idea is that this structure allows routers to efficiently direct data packets to the correct network and then to the correct device.

**In short: An IPv4 address is a four-part number, where each part is between 0 and 255, that uniquely identifies a device on a network.**




