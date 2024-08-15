**Middleware** are classes or functions that are run sequentially in a pipeline, that define how your app responds to requests. They map to cross-cutting concerns i.e. they're responsible for a lot of things in different layers in your app e.g. logging, security.

They can:
- handle HTTP requests by generating a response
- process an HTTP request, alter it in some way, and pass it on to another piece of middleware
- process an outgoing HTTP alter it in some way, and pass it on to another piece of middleware, or to Kestrel

Some examples of cross-cutting concerns are:
- logging requests
- adding standard security headers to the response
- 

### Endpoint middleware
The endpoint middleware is responsible for handling an HTTP request and generating a response. This doesn't include requests for static files as a static-file middleware is called before the endpoint middleware. This static-file middleware is responsible for generating static content without passing the request on any further.