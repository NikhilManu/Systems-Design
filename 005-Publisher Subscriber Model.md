What is Publisher Subscriber Model?

![Initial Architecture](https://github.com/NikhilManu/Systems-Design/blob/main/Images/Publisher%20Subscriber%20Model/Initial%20Architecture.jpg)

We have 5 Services (S0 ... S4) and a client connected.

Client sends a request to S1, it needs to send 2 messages (to S0 and S2). Order of the messages doesnt matter.
Similarly S3 and S4 needs to get messages from S2 after its done processing and order of the message also doesnt matter.

If we are using a Request/Response architecture, we will be sending the messages asynchronousely and wait for the responses ( for both S1 and S2)

Problems Faced here 
    -S2 will be waiting for S3's and S4's response to be completed (Strongly coupled)
    -If S4 Fails, then S2 will send a timeout to S1. Ultimately S1 will send a timeout to the client. It took a long time for request to fail
     This is called failure Latency
    -S1 processed the data correctly and so the values in the DB for S1 will be useless if S2 gave a timeout error. So there is Inconsistent data in the DB right now.


Better way to handle this is Publisher Subscriber Model
---------------------------------------
![Initial Architecture](https://github.com/NikhilManu/Systems-Design/blob/main/Images/Publisher%20Subscriber%20Model/Subscriber%20Architecture.jpg)

In this Model we can remove the Request/Response dependency between the Services and make it pass a message. Messages are Fire and Forget.

Now S1 can send a success to the Client. The message broker is going to be waiting till S2 is back up and then send the message. Same thing will happen for S3 and S4.
Message will be persisted until the down services are back up

Advantages
---------
    -Decoupling
    -Simplifies Interaction (Single Point of failure)
    -If you send request and get a response SUCCESS, this means that the process will terminate in some point in future. (Transaction Capability - Loose Transaction Gurantee of Atleast Once)
    -Easily Scalable because if new service comes , then we only need to register it with the message broker

Disadvanteges
-----------
    -Poor Inconsitency (So cannot use for mission critical services)
    -Idempotency still required

    (See the examples for both the points in the video
        1. how Multpile API request for transfer of money can cause data inconsistency)


Twitter Uses this Model. 