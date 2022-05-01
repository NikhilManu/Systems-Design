Features going to be implemented:
------------------------
    1. Group Messaging
    2. Send + Delivered + Read receipts
    3. Online / Last Seen
    4. Temporary Chats (Since if both users delete the chat, it is gone forever)
    5. Image Sharing (Already covered in Tinder Architecture)
    6. One to One Chat


One to One Chat With Receipts
-------------------------
    Each Users are connected to some Gateway service. Since there may be multiple users connected to multiple Gateways
    Some data must exist which keeps information of which user is connected to which gateway. Also this data will be duplicated 
    one all server. This shows lot of coupling in the system

    The correct place we should ask for who is connected to which server(Box) is a sessions microservice (The multiple servers in diagram to avoid single failure point)

    When User A send message with userId of B. When this is received by sessions, it identifies which Box user B is connected to and
    the routes this message to Gateway of which B is connected. Sending cant be done since it is HTTP Protocol, so here we cant send server to client message

        Http is not something we can use
        We will use WebSockets. Since they allow peer to peer communication. There is no client server semantics over here

    
    When Message gets to session, we will give response back to A that the message is sent. Since the message is stored in database, 
    it is persistent and will keep retrying till the message is sent to B.
    Once the Message is received at B, it should again give a response back a acknowldgement and will reach the session service.
    Since the message has TO and FROM, it can take data of FROM and gives delivery Receipt to Box which A is connected
    Read Reciept is same as before, the only difference being when the user opens the chat, acknoweldgement is given back

![Chats and Receipts](https://github.com/NikhilManu/Systems-Design/blob/main/Whatsapp%20Design/Images/Chat%20and%20Receipts.png)

Last Seen and Online 
--------------------
    To DO....



