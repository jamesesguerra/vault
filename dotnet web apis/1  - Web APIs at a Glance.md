> a **web api** is an HTTP-based services that has the sole purpose of providing data to clients, allowing them to interact with the information they require in a uniform, structured, and standardized way

### Web APIs
An **application programming interface (API)** is a type of software interface that exposes tools and services that computer programs use to interact with each other and exchange information. The connection required to perform such exchanges are supported by communication standards -- **protocols**. These protocols contain the kinds of messages that can be exchanged (specification), and standard data exchange formats like JSON, XML.

The main purpose of an API can be likened to a plug socket -- they allow users to interact with their underlying functionality simply by knowing how to interact with interface. It standardizes how everyone interacts with it and hides all the complexity / inner-workings underneath the hood.

In this analogy, web APIs are interfaces that expose 1 or more sockets (endpoints), which other parties (clients), can use to interact with the power supply (the data) by using common communication protocols (HTTP) and standards (JSON). By using an interface over the electricity (the data), we don't allow any users (clients) to be exposed to the underlying mechanism (the data model).

#### Web APIs within a SOA
A **service oriented architecture (SOA)** is an architectural style build around the separation of responsibility of various independent services.

The role of web APIs in this architecture is key, as it makes available the data from a database to the clients of the app. Clients can take the form of mobile and web apps that allow people to interact with the data, cloud services such as data warehouses and machine learning software.

![[soa-architecture.png]]

#### Types of web APIs
1. **public APIs**
- available to use by anyone, sometimes with limitations
- no authorization / authentication required, although some require an API key for identification
- employ throttling, queueing, security measures so that they won't be crippled with a lot of requests

2. **partner APIs**
- mean to be available only to a subset of people
- usually for B2B scenarios
- implement strong authentication mechanisms

3. **internal APIs**
- meant to be available only to an organization
- often hosted within the same network as other services

4. **composite APIs (API gateways)**
- aggregate multiple APIs to execute a series of operations within a single call
- allows developers to access several endpoints at a time
- often used in a microservice architecture where a single operation may need to carry out operations that require the completion of a chain of subtasks handled by various services
- an *API orchestrator* that can be composed of the 3 other API types

#### Message protocols
##### REST
REST is designed for network-based applications that use standard HTTP request verbs to access and manipulate data

**Guiding rules**:
- **statelessness** - the server shouldn't store anything about the client / client's session
- **cacheability** - the data exchanged by client and server should use caching capabilities provided by HTTP
- **layered system** - putting the web API behind a proxy can greatly improve performance, security, and scalability

**Uniform Interface**
- **identification of resources** - resources should be accessible through its unique URI
- **manipulation of resources through representations** - clients must be able to perform operations on resources through their URI and HTTP methods
- **HATEOS** - the server should provide useful information to clients, but only through hyperlinks and URIs; this allows clients to discover new resources without knowing anything about the server beforehand

### ASP.NET Core
#### Architecture
Developers build or adopt applications composed of discrete and reusable component that perform specific tasks and communicate through a series of interfaces made available by the framework. These components are called **services** and **middleware**, and they're configured in `Program.cs`.

##### Services
Services are components that an application requires to carry out its functionalities or use cases. We can think of them as *our* app's dependencies since we rely on them to work as expected. .NET Core supports Dependency Injection (DI) which allows us to achieve inversion of control between a class and its dependencies.

- **implementation** - each service is implemented by means of a dedicated interface (or base class) to abstract the implementation; both the interface and the implementation can be provided by the framework, created by the developer, or be a third-party class
- **registration & configuration** - all services can be registered to the default DI container in the built-in `IServiceProvider` class; the actual registration happens in `Program.cs` where the developer can select a suitable lifetime
- **dependency injection** - each service can be injected to the constructor of the class or the parameters of an action method; the framework, through the `IServiceProvider` interface, creates a new one or possible reuses an existing instance, and makes sure to dispose of the instance when it's no longer needed (depending on its lifetime)

##### Middleware
Middleware is a set of components that operate on the HTTP level and can be used to handle the whole HTTP request pipeline. Each type of middleware can pass the HTTP request to the next middleware in the pipeline, or provide an HTTP response itself, thus short-circuiting the pipeline and prevent further processing. This kind of middleware is known as **potentially-terminal middleware** as it has the potential to end the pipeline if certain criteria is met. Some examples of these include the `StaticFileMiddleware` as it immediately returns the request static file if it's available, and the `HttpsRedirectionMiddleware` because it responds with an HTTP-to-HTTPS redirect for all non-HTTPS requests.

On the other hand, there's middleware that will always end the pipeline. These are called **terminal middleware** and an example of this is the `app.Run()` call.

##### Controllers
A controller is used to group a set of action methods (also called actions) that handle similar HTTP requests. They aggregate action methods that have something in common: URI prefixes, routing rules, authorization requirements, caching strategies, etc. These requirements can be defined on the controller, thus avoiding having to add it to each method.

Controllers can inherit from two base classes:
- `Controller` - for typical MVC-apps that return HTML views
- `ControllerBase` - for web APIs that return JSON / XML

##### Minimal APIs
Replaces controllers with smaller, minimal `Map` methods to `Program.cs`. They're a good approach for microservices.

##### Task-based Asynchronous Pattern (TAP)
Task-based Asynchronous Pattern (TAP) is a way to get around the fact that web applications can only handle so many requests due to the limited number of threads. When a web app is forced to deal with too many, the service could go down. A general rule of thumb is to **cache aggressively**. But in some cases, this might not be applicable in the case of non-idempotent requests, or GET requests with a lot of parameters.

ASP.NET Core can deal with this by using TAP. The call to get the requests can be "awaited", allowing the task to not block the main thread.

```csharp
[HttpGet]
public async Task<string> Get()
{
	return await Task.Run(() => {
		return "TODO: Get all requests";
	})
}
```

next: [[2 - Web API Projects]]







