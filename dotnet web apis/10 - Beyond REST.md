#### REST
REST is an architectural style specifically designed for network-based applications that use HTTP request methods. Its paradigm includes:
- client-server approach
- statelessness
- cacheability
- layered system
- uniform interface, HATEOS

**Cons**
- overfetching -- forcing clients to retrieve more data than they need

---
#### SOAP
SOAP is a messaging protocol specification used for exchanging structured information across networks using XML.

**Why choose REST over SOAP?**
- XML used to make requests and receive responses become complex and difficult to read, understand, and maintain
- supports fewer data formats compared to REST that supports JSON and CSV
- worse compatibility since modern browsers are optimized to consume REST web services
- SOAP messages are usually sent with POST requests, making it hard to cache at the HTTP level since it's non-idempotent

---
#### GraphQL
A query language for APIs created by Facebook.

**Pros**
- allows clients to receive data by using a single declarative query to a single endpoint
- each client can ask exactly for what they need within a single request / response cycle, avoiding the risk of over / under fetching
- standardized query syntax makes it so that there's no need to implement paging, sorting, filter manually

**Cons**
- no native caching support
- slower than REST since there's additional overhead
- often comes with no support, and has a steeper learning curve

--- 
### gRPC
gRPC is a language-agnostic, cross-platform, high-performance Remote Procedure Call architecture designed by Google to ensure **high-speed communication among microservices**.

**Pros**
- lightweight -- the framework can run on almost any hardware
- low latency granted by HTTP/2 compared to HTTP/1.1 approaches by REST and GraphQL
- protobuf binary serialization reduces network use
- takes advantage of bidirectional, streaming, multiplexing features of HTTP/2 compared to the HTTP/1.1 request-response communication model

**Cons**
- complex and steep learning curve
- most clients are unable to support gRPC out of the box because they don't expose built-in APIs to control HTTP/2 requests with JavaScript
- protobuf isn't human readable and you need some tool to read it
- harder to cache 


