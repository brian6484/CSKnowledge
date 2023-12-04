# Webscoket
Unlike HTTP which is a stateless, connectionless unidirectional request-response protocol, Websocket is a **persistent connection**.

## How Websocket connection flows
The process starts with a WebSocket handshake, initiated by the client. The client sends an HTTP request with an "Upgrade" header to the server.
If the server supports WebSockets, it responds with an HTTP 101 status code ("Switching Protocols") and includes the "Upgrade" header in the response.
Once the handshake is complete, the connection is upgraded to a WebSocket connection.

Either the client or the server can close the connection via a specific control frame called the "Close frame". A status code can also be included to state
the reason of closing connection.
