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

<entity-body>
```

The request line has 3 fields: the HTTP method, the path to the resource on the server, and the HTTP version.

The header fields can contain any number of known HTTP header fields. In this example, the `Host` field specifies the domain of the host where the object resides. `Connection` tells the server close the TCP connection after sending the requested object. `User-Agent` tells the server what kind of browser made the request since servers can choose to send multiple versions of the same object, depending on the kind of browser. The same is true for the `Accept-language` field which tells the server to send the French version of the object, if available. These types of fields that tell the server which variant to send is called content negotiation.

#### HTTP response
Once the server gets the request, it generates an HTTP response message together with the requested object in the entity body.

Like the HTTP request, an HTTP response consists of a status line, several header lines, and an entity body. The status line consists of 3 fields: the supported HTTP version of the server, the HTTP status code of the response, and a phrase that describes the status. 

The header lines can be multiple things as well. In the example, `Date` refers to the date and time the server responded. `Server` is the type of web server that generated the response and is similar to the `User-Agent` field of an HTTP request. `Content-Length`  specifies the size of the entity body in bytes, and the `Content-Type` specifies the kind of object being sent. **NOTE**: the `Content-Type` header field dictates what the object is going to be interpreted as, and not the object's file name.

```
HTTP/1.1 200 OK
Connection: close
Date: Tue, 18 Aug 2015 15:44:44 GMT
Server: Apache/2.2.3 (CentOS)
Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT
Content-Length: 6821
Content-Type: text/html

<entity-body>
```

For both HTTP requests and responses, any number / combination of header fields can be used. This is dependent on the browser type and version, the user configuration of the browser, and whether the browser has a cached version of an object.

### Cookies
Even though HTTP is a stateless protocol, ie the server doesn't store anything about the client / previous HTTP transactions, it can still do something to identify a user from their HTTP request. Web servers can do this by using cookies -- which are passive data structures. 

Cookies have 4 parts:
- the cookie header field in the HTTP request
- the cookie header field in the HTTP response
- the cookie file on the client / browser
- the identity of the user on the backend database

When a user makes a request, the Web server can choose to create an identity using either information from the server / information volunteered by a client e.g. an email address. This identity is placed in the database and sent back in a `Set-Cookie` header fields. Once the user agent receives this header field, it will store the cookie in its cookie file. And for its next requests, it will then send this cookie in the same header field. Once the server receives this cookie in the request, it can then perform cookie-specific actions ie actions as a function of the user identity.

### Web cache
A **Web cache** or **proxy server** is a network entity that satisfies HTTP requests in behalf of an origin server. It has its own disk storage and stores copies of recently requested objects in its storage. 

Browsers can be configured to point to a Web cache without the user knowing. In those cases, when a browser makes an HTTP request, it first opens a TCP connection to the Web cache. If the Web cache has the requested object, it serves it to the browser. If not, the Web cache opens its own TCP connection to the origin server, who then serves the request to the Web cache, and the Web cache serves it to the client. In this manner, a Web cache is both a client and a server. 

A Web cache is typically installed by an ISP. Once configured, the cache can lower the traffic intensity on the access link, depending on the hit percentage.  If there is a cache hit, there is no need to go the Internet router and all the way to the origin server. This reduces the delay as there would be no more access delay (delay between the institutional router and the Internet router) and Internet delay (delay between the Internet router and the origin server).  There would only be LAN delay as the Web cache is installed in the same network.

#### The conditional GET
When a Web cache requests for an object from an origin server, it stores the object along with its `Last-modified` header field. If for example, a user requests the same object from the Web cache after a week, the object is possible stale / outdated. And so what a Web cache does is sends a new GET request to the Web server, but this time, adds a `If-modified-since` header field with the value equal to the `Last-modified` field it has stored. If the object being requested has been modified after the specified date, the origin server sends the object once again to the cache. Otherwise, the origin server sends a `304 Not Modified`, which tells the cache it's free to send its cached version to the client once again.