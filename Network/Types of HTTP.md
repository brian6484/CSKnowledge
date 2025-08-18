## HTTP/1.0 and HTTP/1.1 → always run on top of TCP
TCP provides reliable and ordered connection between client and server.

## HTTP/2 → also runs on top of TCP
Supports [multiplexing](https://github.com/brian6484/CSKnowledge/tree/main/Network)

## Analogy
HTTP/1.1 = one-lane road → cars (requests) must wait in line.

Multiplexing = multi-lane highway → cars move independently and overtake each other.

## HTTP/3
ueses QUIC over UDP.
QUIC ensures reliability, ordering and congestion control at application layer. 
Supports multiplexing but avoids TCP's head of line blocking prob

## HTTP/2 VS HTTP/3 Multiplexing Difference
HTTP/2 
If a TCP packet is lost, all streams wait because TCP guarantees order.
```
Browser                            Server
   |----Request 1------------------>|
   |----Request 2------------------>|
   |----Request 3------------------>|
   <--Response 1--------------------|
   <--Response 2 (delayed if packet lost)|
   <--Response 3--------------------|
```

HTTP/3
Each stream is independent. Packet loss in one stream does not block others.
```
Browser                            Server
   |----Request 1------------------>|
   |----Request 2------------------>|
   |----Request 3------------------>|
   <--Response 1--------------------|
   <--Response 2 (only this stream waits if packet lost)|
   <--Response 3--------------------|
```


