 Tinder Architecture
    Store Profiles (Images) - 5 Images per user
    Recommend matches - No of active users
    Note matches - 0.1% matches per person
    Direct Messaging

Storing Images
---------------

    File vs Blob (Binary Large object)
    --------------

        only few extra gurantees db gives are 
            1. Mutability --> Unnecessary feature for image 
            2. Transaction (ACID) --> no atomic operation will be done in image
            3. Indexes (search) --> No use in image
            4. Access Control --> Required, but can achieve same access control mechanism using file systems

        File Systems:
            Cheaper
            Faster
            Content Delivery Network(CDN) allows fast access



![Inital Design](https://github.com/NikhilManu/Systems-Design/blob/main/Tinder%20Architecture/images/Inital%20Image%20Storing.png)

    Now the profile service is authenticating and sending back the response
    If there is new service which will come later on, that service will have to talk to profile service everytime, that logic
    will also be duplicated third time when another service comes

    So every time user sends request there will be lot of code duplication, to avoid this we can use a
        -Gateway:  
                clients always talk to the gateway and No one else talks to gateway other than clients

                Gateway takes a request (Username and token) and ask the profile service whether it is a valid request or not.
                If the response is Yes then it is directed to correct service otherwise it will fail.


![Final Design](https://github.com/NikhilManu/Systems-Design/blob/main/Tinder%20Architecture/images/Final%20Image%20Storing.png)

    Now we have decoupled the entire system

    Do we really need to store image in profile Services?
        There is no right answer. but logically we may need to store the image in different services, as the images may be needed by 
        different service


Direct Messaging For Chat
--------------------------

    HTTP - Client Server Communication Protocol: 
        You cannot have a chat with this type of protocol because the User2 will have to pull from server or ask every time is there any message for me,
        which is extremly inefficent

    So here we dont want to pull from server, the message has to be pushed to us. So we have to use some Peer to Peer Protocol
    XMPP - Peer to Peer Protocol
    The XMPP is going to be taking a Web Socket connection

    So to decouple the system further, the gateway will not be handling the userId with connectionID. we will use a sessions service
    Sessions is going to be storing connection information, ie.. userId and connectionId

![DM Design](https://github.com/NikhilManu/Systems-Design/blob/main/Tinder%20Architecture/images/DM%20Design.png)


Matching Algorithm
------------------------------

    Here the problem is, we cant send messages to other person unless we are matched, so we will use some Matcher Service
    which stores information of user --> user who are matched and sessions will take some information from matcher so as
    to check whether user1 can send message to user2

![Matcher Design](https://github.com/NikhilManu/Systems-Design/blob/main/Tinder%20Architecture/images/Matching%20Algorithm.png)


Recommendation
----------------------

    We can create some algorithm based on age gender and location. We cannot sort data with multiple indexes as it will pick only any one index during query

    So it is better to use 
        NoSQL - Casandra
        Sharding on RDBMS --> Horizontal Partitioning (Sharding db based on location is best I guess)
            So what if any shard fails? We can use a master slave architecture to avoid single point of failure

    So we will have Some Recommendation Service and some table which stores userId and current location (Since sharding on locations)

![Matcher Design](https://github.com/NikhilManu/Systems-Design/blob/main/Tinder%20Architecture/images/Final%20Design.png)
