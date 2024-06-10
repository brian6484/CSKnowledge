## Message Queue
![image (5)](https://github.com/brian6484/CSKnowledge/assets/56388433/2319394e-aaf1-4198-a55f-f7a1f1b5cbcb)

As MSA is on the trend, message queue plays a part in this. But first, we have to understand MOM.

[very useful](https://velog.io/@choidongkuen/%EC%84%9C%EB%B2%84-%EB%A9%94%EC%84%B8%EC%A7%80-%ED%81%90Message-Queue-%EC%9D%84-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)

## Message-Oriented Middleware (MOM)
MOM is a software for asynchronous data communication between application software. It can store, route and transform these
messages when delivering them.

It provides persistence by ensuring that even when network or system crashes, messages have already been persisted to storage
so they will be eventually sent to the consumers. Because of this backup and persistence capability, senders and receivers
dont need to maintain a simultaneous network connection, which **decouples the communication process** and allows greater
flexibility. 

It provides routing via point-to-point(1 sender to 1 receiver) and pub-sub(1 publisher to multiple consumers) models. It can
also route based on the header, content and other criteria.

Lastly, it can convert the types of messages to adapt to the systems that may use different protocols or different data types.
Like a message can be convereted to message, EMAIL, database update, etc 
