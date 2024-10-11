Symmetric and public key cryptography, message integrity, digital signatures, and end-point authentication can be used to provide security in terms of the CIA to network protocols. When security is provided for a lower-layer protocol, all the upper layers get to enjoy the security services as well. However, you do need security implemented at different layers in the protocol stack since they take care of different things. For example, security implemented in L3 authenticates source IP addresses, but can't provide user-level security. For example, it cannot authenticate whether a user is a valid user in the application's database.

#### Securing TCP Connections with SSL
The enhanced version of TCP is known as **Secure Sockets Layer (SSL)**, and a slightly modified version of SSL version 3 is called **Transport Layer Security (TLS)**. Typically, when someone mentions SSL, they probably already mean TLS.

##### The need for SSL
Why do we need SSL? A scenario could be when Bob wants to buy a perfume from an online store. He enters his name, address, items, and payment information.

- **No confidentiality** - an intruder could sniff his packets and obtain Bob's credit card information
- **No message integrity** - an intruder could modify Bob's order, making him order 10x the number he actually ordered
- **No end-point authentication** - if no server authentication is used, Trudy could use a server to display Alice's website and take Bob's money after he orders from it

SSL addresses this issues by providing confidentiality, message integrity, and authentication to TCP. It's typically used with HTTP, but because it's an enhancement on a transport layer protocol, it can be used with any application layer protocol. SSL provides an API with sockets akin to TCP's sockets. And so if a web application wants to use SSL, the app uses SSL/TLS classes/libraries.

##### How SSL works
Although SSL technically resides in the application layer, from a developer's perspective, it resides in the transport layer.

![[ssl.png]]

###### Almost-SSL
Almost-SSL has 3 phases, with Bob as the client and Alice as the server having a public/private key pair and a certificate that binds her public key to her identity.

**handshake**
Bob needs to (1) establish a TCP connection with Alice, (2) verify that Alice *really* is Alice, and (3) send Alice a master secret key, which will be used by both parties to generate the symmetric keys they need for the SSL session. After Bob establishes a TCP connection with Alice, he sends an SSL hello message to which Alice replies with her certificate containing her public key. Bob then generates a master secret for the SSL session and encrypts it with Alice's public key. He sends the key over and Alice decrypts the encrypted master secret (EMS) with her private key.

![[ssl-handshake.png]]

**key derivation**
Both Bob and Alice use the shared message secret to generate 4 keys. Each party generates a pair of keys: so they would have a key for encrypting data sent, and a key for verifying the integrity of the data.

