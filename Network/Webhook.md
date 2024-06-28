## Webhook
Normally, the traditional API way is for client to call the server. But webhook is the opposite. When there is an event in 
the server side, the server calls the client by the **callback url**. Callback url is the client's address for the server to
send data to.

## Diff between webhook and polling
![114152964-cf0ff280-9959-11eb-99ae-5a190b67f4bb](https://github.com/brian6484/CSKnowledge/assets/56388433/6d4defc5-0db1-4b54-8a56-5f4be540f869)

For polling, the client has to keep checking for a change in the state of server constantly. But webhook reduces the number
of API requests that client needs to send to server cuz it is the server that sends the data to client when there is an event.
