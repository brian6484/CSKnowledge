# HTTP VS HTTPS
## Hypertext Transfer Protocol
Hypertext - document containing links to pages,texts, images, videos, etc
Transfer - communication
Protocol - rules

In short, HTTP is an application layer (OSI layer 7 still remember OSI?) protocol to facilitate hypertext between clients and servers.

### characteristics of http
1) connectionless
HTTP uses TCP/IP (not HTTP3, which uses UDP) to first establish a connection between client and server. Once the server is done responding back the
response to client's request, this connection is closed. It closes cuz if a server connects with multiple clients, a lot of resources like memory are used up.

Problem with this is that since server doesnt remember the client, every time a client sends a request to the server, the connection/disconnection needs to be 
made, causing overhead.

This overhead can be mitigated by using persistent connection (**keep-alive**), where multiple requests can be send over the same connection without 
needing to connect over and over again.



