# Webscoket
Unlike HTTP which is a stateless, connectionless unidirectional request-response protocol, Websocket is a bi-directional **persistent connection**. This thus allows
real-time update without need of *polling*.

## How Websocket connection flows
The process starts with a WebSocket handshake, initiated by the client. The client sends an HTTP request with an "Upgrade" header to the server.
If the server supports WebSockets, it responds with an HTTP 101 status code ("Switching Protocols") and includes the "Upgrade" header in the response.
Once the handshake is complete, the connection is upgraded to a WebSocket connection.

Either the client or the server can close the connection via a specific control frame called the "Close frame". A status code can also be included to state
the reason of closing connection.

## Data transfer
string data is transferred through text frames while binary data is through binary frames

## Limitations
As you will see later, reason why we incorporate Redis is because if we have multiple servers, since it is stateful, it needs to connect to that 1 specific server so it is not that flexible.
