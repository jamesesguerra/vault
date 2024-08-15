### How .NET Core works
1. The client (browser or mobile) makes a request to your web server, although it's more common to place a reverse proxy between the client and Kestrel
2. The default web server of .NET Core, Kestrel, takes the request and create an `HttpContext` object you can use to cater the request
3. The `HttpContext` object goes through a middleware pipeline that changes the object in several ways; ending up at the endpoint middleware which calls the code that will eventually generate the final response
4. The controllers, either MVC, Minimal API, or Web API, take the request and send it to your C# code to carry out the domain / business logic
5. The C# code can access the DB using a data access layer, either with EF Core or Dapper, and returns the data as the response
6. The result gets propagated back and the server generates the appropriate response, either HTML or JSON

![[dotnet-process.png]]

### Paradigms
There are different ways you can build applications with .NET Core
1. **Minimal APIs** - simple HTTP APIs that can be consumed by browsers and mobile devices
2. **Web APIs** - more complicated than minimal APIs but have more features and structure
3. **gRPC APIs** - efficient server-to-server APIs, used for inter-microservice communication
4. **Razor pages** - page-based server-rendered applications
5. **MVC controllers** - similar to Razor pages, without the page-based paradigm
6. **Blazor WebAssembly** - SPA framework similar to Angular and React
7. **Blazor Server** - server-rendered application with SPA feel (SSR)
