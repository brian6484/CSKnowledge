# OSI
## About
APS T NDP (abs T NDP national day parade) acronym
![Screenshot 2023-11-16 133204](https://github.com/brian6484/CSKnowledge/assets/56388433/804681f2-936c-40e7-8fcf-e88798b53292)

Open Systems intercommunication (OSI) is a standard for different computer systems to communicate with one another. It splits up
communication system to 7 layers

## Layer 7 - application layer
![Screenshot 2023-11-16 133705](https://github.com/brian6484/CSKnowledge/assets/56388433/891a9333-caf2-4fe9-a8a5-c9bfff320021)

This is the only layer that interacts with the user. Software apps like web browsers and email client use this layer to communicate with users.
Protocols include **HTTP and SMTP** (Simple Mail Transfer Protocl)

## Layer 6 - presentation layer 
![Screenshot 2023-11-16 133717](https://github.com/brian6484/CSKnowledge/assets/56388433/4a96e679-8c8f-4952-837f-d6c5f98213f7)

It prepares data to be used by layer 7 through means like encryption, translation and compression of data. This is impt cuz
2 devices may use different encoding methods, so this layer translates incoming data into a specific format that layer 7 can understand.
It also compresses data received from layer 7 to send to layer 5 to improve speed and efficiency.

## Layer 5 - session layer
![Screenshot 2023-11-16 133733](https://github.com/brian6484/CSKnowledge/assets/56388433/35bbec9c-a73c-45a4-9c12-069cad056f28)

It opens up and closes communication between 2 devices and the duration which the communication is opened for data transfer is called **session**.
It can also set up checkpoints like if 100mb file, it can set checkpoint for like 25mb so in case of a disconnect, you can continue from there.

## Layer 4 - transport layer (TCP, UDP)
![Screenshot 2023-11-16 133743](https://github.com/brian6484/CSKnowledge/assets/56388433/e7247c2d-0a33-4031-baf9-c2cf32e9afcb)

It is an end to end communication between 2 devices. This layer takes data from session layer and breaks it up into **segments** to be 
sent to layer 3. Layer 4 on receiver side is responsible for reassembling segments into meaningful data for session layer to be consumed.

## Layer 3 - network layer
![Screenshot 2023-11-16 133754](https://github.com/brian6484/CSKnowledge/assets/56388433/8e474fec-bb9b-4bf3-9f2b-661b2762615f)

This aids data transfer between **2 diff networks** so it is responsible for flow control and error handing in **inter-network communication**.
It breaks up segments from layer 4 to even smaller units called **packets** and the receiver side reassembles these packets. It also finds the
best physical path to reach destination - *routing*.

This layer includes IP, ICMP, etc.

## Layer 2 - data link layer 
![Screenshot 2023-11-16 133805](https://github.com/brian6484/CSKnowledge/assets/56388433/18218c3d-af5c-4c7f-9984-03c7e4bf33ff)

This aids data transfer within **same network** unlike network layer.
It takes packets from layer 3 and breaks it up to smaller units called **frames**.


## Layer 1 - physical layer
![Screenshot 2023-11-16 133815](https://github.com/brian6484/CSKnowledge/assets/56388433/20042aae-5004-49ff-8e90-6ebd86c78841)

It is the physical equipment like cables and switches where data will be converted into a bit stream - of 0s and 1s.

## How it works example
For info to be transferred over a network, it has to travel down the 7 layers of OSI model on sending side and travel up the 7 layers on the receiver side.
For example, if Brian sends an email to Jae, email message at application layer picks up SMTP protocol and passs data to presentation layer. Presentation layer
compresses this data and session layer initialises communication session.

Transportaion layer segments this and network layer breaks segments to packets and data link layer from packets to frames. Finally the frames to physical layer
are converted to bit stream of 1s and 0s and sent to a physical medium - **cable**.

Jae's computer receives this bit stream through a physical medium - **wifi**. Then the process is reversed. Bit stream is converted back to frames, packets and
segments and segments to one piece of data. This data then flows to session layer, which passes data to presentation layer *before ending the session*. 
Presentation layer removes the compression and passes raw data to application layer 7. Layer 7 feeds human-readable data to Jae's email software.
