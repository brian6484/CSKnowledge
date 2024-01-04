# HTTP VS HTTPS
## Hypertext Transfer Protocol
Hypertext - document containing links to pages, texts, images, videos, etc
Transfer - communication
Protocol - rules

In short, HTTP is an application layer (OSI layer 7 still remember OSI?) protocol to facilitate hypertext between clients and servers.

### characteristics of http
1) connectionless
HTTP uses TCP/IP (not HTTP3, which uses UDP) to first establish a connection between client and server. Once the server is done responding back the
response to client's request, this connection is closed. It closes cuz if a server connects with multiple clients, a lot of resources like memory are used up.

Problem with this is that since server doesnt remember the client, every time a client sends a request to the server, the connection/disconnection needs to be 
made, causing overhead.

This overhead can be mitigated by using persistent connection (**keep-alive** header), where multiple requests can be send over the same connection without 
needing to connect over and over again. 
```
GET /path/to/resource HTTP/1.1
Host: example.com
Connection: keep-alive

```
This keep-alive header can be included when the client wishes to use a persistent connection. If server does support this, it responds back with the same header until the server closes it with Connection: close header. But from the server, to support persistent connection with many clients brings much in-memory usage of the server.

2) stateless
Server cannot differentiate which client is which because HTTP is stateless - it doesn't know the state of clients. So client needs to authenticate himself everytime.
There are several solutions like cookie, session and token [here](https://github.com/brian6484/CSKnowledge/blob/main/Network/Cookie%2C%20Session%2C%20Token.md)

3) TCP/IP
HTTP uses TCP/IP as mentioned. 

### http message
There obviously is request and response. Let's look at request
<img width="771" alt="httpResponse" src="https://github.com/brian6484/CSKnowledge/assets/56388433/1eae4a28-3b62-4ae5-9408-6716579db556"> 

We have the method, path, version and the headers.

The methods are the usual -GET, POST, DELETE, PUT. More explained [here](https://github.com/brian6484/CSKnowledge/blob/main/Network/HTTP%20method%20and%20PUT%2CPOST%2CPATCH.md)


For headers, there are 4 types
1) general header- used by both requests and responses like Date
2) request header - only for request like User-Agent, which identifies the user agent(i.e. browser) making that requst
```
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.4567.89 Safari/537.36
```
3) response header - only for response like Content-Type, which tells client about the mediat type like text/html
4) entity header - used by both requests and responses that provides info about the resource being sent like Content-Length, which tells the size of the message body in octets

## Hypertext Transfer Protocol Secure (HTTPS)
HTTP doesnt encrypt info sent in requests and responses. HTTP does with the help of SSL or TLS

### encryption
Before we talk about encryption, we need to discuss the type of keys

#### symmetric key
The same key can be used to encrypt and decrypt data. It is like a car key that can lock and unlock the same car. BUT this is old and unsafe because if you lose the
key, hacker can use the key to decrypt the data. So nowadays we use public and private key

#### public & private key
Data encrypted with public key **cannnot** be decrypted with public key. Only private key can decrypt data encrypted with public key.
An example would be a mailbox (public key). Anyone can post mail to you cuz it is public but only you have the private key to open your mailbox and read the mails.
Whe someone wants to send message (encrypt), he can put it in the mailbox and drop it in the hole. But only you with private key can open the box and read it (decrypt).

But there is a prob with keys. How do you know that this public key is reliable? Like how do you know for certain that it is this entity's public key and not a hacker's public key? This can be verified with a digitical cert like SSL cert that is authorises this link between entity and key by a trusted authority (Certified Authority CA)

### Secure Sockets Layer (SSL)
SSL is a tech that encrypts data exchanged between client and server or server to server. There is SSL certificate issued by a certified third party that authenticates the client and encrypts the data exchanged.

### encryption flow
Client first initialiates a SSL/TLS handshake by sending a 'ClientHello' message to server and specifying which cryptographic algorithms
it supports. Server then sends a 'ServerHello' message, selecting an algorithm from the client's list of supported crypto algorithms
and also a digital cert, which includes its public key, that is signed by Trusted Authority (CA). 

Now client generates a pre-master secret, which is a unique and random value that is specific to the current session. This secret
is encrypted with the server's public key (public-key encryption or **asymmetric encryption** and sent to the server, which since only the server has the private key that is *paired* with the public key can encrypt this. No other servers or clinets can decrypt this.

Client and server exchange 'Finished' messages, which means handshake is complete and connection is secure.



