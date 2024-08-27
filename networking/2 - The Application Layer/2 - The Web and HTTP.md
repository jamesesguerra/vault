The Web is the first network application that caught the general public's eye and is what gave the Internet the fame that it has today.

What makes the Web appealing to most users is that it operates *on demand*. Unlike radio and TV, you don't have to tune in at a particular time to consume some piece of content. Also, it's very easy for someone to *produce* content as well.

HTTP is at the heart of the Web and is implemented by a client program and a server program. These programs operate on different end systems, and talk to each other by exchanging HTTP messages.

### Web pages
A **Web page** is a document that consists of objects. An **object** is a single file, be it an HTML file, a JPEG image, or a GIF, that is addressable by a single URL. Most Web pages consists of a base HTML file that references several other objects by their URL. Each url contains a hostname, followed by the path to the object.

A browser implements the client program of the Web and is the requester of these objects. The Web server, which implements the server program of HTTP, houses these objects, each addressable by a URL.

One of the jobs of HTTP therefore, is defining how HTTP clients request objects from servers and how HTTP servers respond to clients.

### HTTP & TCP
HTTP uses TCP as its transport protocol. The HTTP client first establishes a TCP connection with the HTTP server, and when the connection is made, it's free to access TCP through its socket interface. When an HTTP client requests for objects, it sends a message through its socket interface and receives the objects through the same interface. The same is true for HTTP servers. And because HTTP uses TCP, once a process sends an HTTP message through its socket, it need not worry whether or not all the data will arrive at its destination.

It's also important to note that HTTP servers don't store anything about the clients. So if a client requests for an object, and requests for it a second time, the HTTP server can't tell it "you just requested for this so I won't send it back". This makes HTTP a **stateless** protocol.

#### Persistent & Non-persistent connections
For most internet applications, the cycle of requesting for objects and receiving them happens quite frequently. And so another option a developer can consider is whether the TCP connection for each request-response pair should be reused for all other objects in a Web page, or whether each object should use a separate TCP connection.

For non-persistent connections, overhead can be caused by establishing the TCP connection each time for each objects. The **round trip time (RTT)** , which is the time it takes for a client to send a small packet to a server, and get the response back, is 1 for establishing the TCP connection. And so for each object, it takes 2 RTT. Although browsers can be configured to make the requests in parallel instead of serially.

On the other hand, persistent connections persist the TCP connection until it isn't used for a configurable amount of time.  A TCP connection is established only once, and the client and server are free to request and respond back-to-back, without waiting for responses to pending requests.

### HTTP Message format
HTTP defines the types of messages that can be sent as well as the format and meaning of those messages. The 2 types of HTTP messages you can send are HTTP requests and HTTP responses.

#### HTTP request
An HTTP request consists of 1 request line, multiple header lines, and an entity body.

```
GET /somedir/page.html HTTP/1.1 
Host: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: fr
```

The request line has 3 fields: the HTTP method, the path to the resource on the server, and the HTTP version.

The header fields can contain any number of known HTTP header fields. In this example, the `Host` field specifies the domain of the host where the object resides. `Connection` tells the server close the TCP connection after sending the requested object. `User-Agent` tells the server what kind of browser made the request since servers can choose to send multiple versions of the same object, depending on the kind of browser. The same is true for the `Accept-language` field which tells the server to send the French version of the object, if available. These types of fields that tell the server which version to send is called content negotiation.