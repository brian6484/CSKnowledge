# TCP and UDP
## About
TCP and UPD are in layer 4 of OSI (Open Systems Interconnection), which is the Transport Layer.
[more on OSI here][https://github.com/brian6484/CSKnowledge/blob/main/Network/OSI%20.md]

## IP 
Internet Protocol IP is the address of the Internet. Data in the form of packets is delivered from sender to receiver but IP
does not guarantee the order of packets received or error checking. This is cuz IP is connectionless protocol, where target does not send
an acknowledgement back to the source.

This is TCP's role.

## TCP
TCP/IP is like sending a message on a puzzle through mail. The message is broken down to puzzle pieces and travel through different postal route.
Some may arrive earlier or later then the rest so they are out of order. So whilst IP makes sure that the puzzle pieces arrive at the designated
destination, TCP is the puzzle assembler that puts pieces together in the right order. Not only odes it assemble, but if some pieces are missing,
it asks for missing pieces to be resent and lets the sender know that puzzle is received.

![Screenshot 2023-11-16 144101](https://github.com/brian6484/CSKnowledge/assets/56388433/1b6a2077-a89b-4045-bafc-02322be29493)

In more technical terms, lets say when an email is sent over TCP, a conncetion is made and 3 way handshake is made. The handshake consists of
1) source first sends syncrhonisation SYN packet to target
2) target sends back SYN-ACK synchronisation-acknowledgement packet to the target to agree to the process
3) source sends ACK packet to confirm the process, after which email contents are sent

As we learnt in OSI, email is broken down to packets before it is sent through the Internet. They traverse several gateways before being reaseembled
by TCP at the target device. So TCP is bi-directional whereas UDP is uni-directional.

## UDP
It is usally used for video streaming and DNS lookups where target does not need to send an acknowledgement back. 
This is cuz UDP sends packets (called datagrams) **without establishing a connection first** and with **no order guarantee** unlike TCP/IP.

![Screenshot 2023-11-16 145115](https://github.com/brian6484/CSKnowledge/assets/56388433/1a8780e8-d037-48b0-9933-31105b39e8e2)


So since there is no handshake, UDP is unidirectional and is thus significantly faster but less reliable. 
IF UDP datagrams are lost in transit, they are not resent like TCP/IP which asks lost packets to be resent.

## So is UDP inferior than TCP/IP?
But most network routers actually dont perform packet ordering and arrival confirmation like UDP cuz more memory is needed. So it is not much of a flaw.
TCP/IP is thus just an additional functionality when the app requires it.
