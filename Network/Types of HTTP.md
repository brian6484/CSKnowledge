## HTTP/1.0 and HTTP/1.1 → always run on top of TCP
TCP provides reliable and ordered connection between client and server.

## HTTP/2 → also runs on top of TCP
Supports multiplexing, which is **sending multiple requests and reponses over the same connection simultaneously
WITHOUT WAITING FOR PREVIOUS ONE TO FINISH**.

### Without multiplexing
Each request has to wait until previous request completesm which causes delays (called **head-of-line blocking** in HTTP/1.1)

### With multiplexing
Server can send responses *as soon as they are ready** even when earlier requests havent finished

## Benefits of multiplexing 
1) reduces latency
2) makes loading faster cuz multiple resources can be fetched **in parallel over ONE connection**, not multiple connections
3) Avoids overhead of opening multiple TCP connections

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


