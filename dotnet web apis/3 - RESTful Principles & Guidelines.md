> There's a different between a REST-based web application and a truly RESTFUL web API.

### REST Guiding Constraints

#### 1. Client-server approach
Separate the concerns of the server from those of the clients. Our web API should only:
- respond to requests made by the client, thus making it unable to make requests on its own
- has no constraints or dependencies on anything about the client (location, environment, technology, underlying implementation etc.)

Most of this is already in place in a .NET Core app with how Controllers and Minimal APIs return standard HTTP responses. They don't deviate from the RFC standard and don't return responses that have information about the client e.g. session IDs.

#### 2. Cross-Origin Resource Sharing (CORS)
There exists a **same-origin policy** implemented by browsers to block external requests initiated by scripts when it tries to access a resource from an origin different from the page hosting the script. The purpose of CORS is to allow browsers to "relax" this policy, saying that it can make requests even when the resources are from a different origin.

CORS settings sent by the server have a function of telling the client what and what not to block. There are 2 main techniques that browsers apply to check for CORS settings:

1. **simple mode**
	- browser checks the request to see if it meets certain requirements (e.g. `GET`, CORS-safe headers only)
	- if requirements are met, request is processed as normal
	- CORS is handled by checking the `Access-Control-Allow-Origin` header to ensure that it complies with the origin of the script
2. **preflight mode**
	- if request doesn't meet criteria, browser puts it on hold and automatically issues a preemptive `OPTIONS` request
	- adds an additional layer of security since the request could be more sensitive or risky
	- checks CORS settings before making the request happen, as opposed to making it happen then checking the CORS settings
	- `Access-Control-Allow-Origin` - the origin allowed to make the request, or `*`  if any origin is allowed
	- `Access-Control-Allow-Headers` - comma-separated list of allowed HTTP headers
	- `Access-Control-Allow-Methods` - comma-separated list of allowed HTTP methods

**Implementing CORS**
We can define a relaxed policy that allows requests from any origin for edge-case scenarios where we want any origin to access our resources, and a more restrictive policy.

```csharp
builder.Services.AddCors(options =>  
{  
    options.AddDefaultPolicy(cfg =>  
    {  
        cfg.WithOrigins(builder.Configuration["AllowedOrigins"]!);  
        cfg.AllowAnyHeader();  
        cfg.AllowAnyMethod();  
    });
    
    options.AddPolicy(name: "AnyOrigin", cfg =>  
    {  
        cfg.AllowAnyOrigin();  
        cfg.AllowAnyHeader();  
        cfg.AllowAnyMethod();  
    });
});
```

Apply the settings before the authorization middleware so that everything after takes into account the CORS policy. If you wanna use the default policy, leave the parameters blank. If you want to use a specific policy, pass the name to the `UseCors` method.
```csharp
app.UseCors();
// or
app.UseCors("AnyOrigin");

app.UseAuthorization();
```

You can enable CORS on a per-endpoint basis for a more fine-grained level of granularity with the `[EnableCors]` attribute. For example, you can use enable the default policy globally then only enable some policies on a per-action method or per-Controller basis.
```csharp
app.MapGet("/error", [EnableCors("AnyOrigin")] () => Results.Problem());
```

#### 3. Statelessness
Enforcing statelessness means avoiding using convenient features of .NET Core that involve storing session data on the server and retrieving it on subsequent requests. All the session-related information must be stored and handled by the client. And so whenever we want to benefit from convenient techniques that involve sessions e.g. authenticating specific calls, we have to provide the client with a **token** to create and manage the session state on its own side.

#### 4. Cacheability
A **cache** is a system or component meant to store data and make it available for further requests with less effort. When dealing with HTTP requests, it's typically used to store frequently requested resources.

**Groups of caching techniques**
1. **server-side caching**
	- application caching
	- caching system that saves data to a key-value store such as Memcached or Redis
	- stores data retrieved from a DBMS or other backend services
2. **client-side caching**
	- browser caching
	- defined in the HTTP specification that relies on several HTTP response headers (`Expires`, `Last-Modified`)
	- stores responses from servers such as HTML, JSON output
	- a major advantage is that it requires no server-side activity, therefore leading to bigger advantages in terms of performance, latency, and bandwidth -- client-side caching is therefore the most important
	- use the `ResponseCache` annotation to control response caching (use `[ResponseCache(NoStore = true)]` for responses you don't want to cache such as error endpoints)
3. **intermediate caching**
	- proxy caching
	- stores data by using dedicated services that are optimized for geographically-distributed content (CDNs)
	- similar to client-side caching except it stores the cached data on a server located between the client and the server instead of on the client's local drive
	- more beneficial since it can serve multiple clients at once, therefore being a shared cache

#### 5. Layered system
This principle takes the client-server approach of separating concerns a step further by applying it to various server components. A RESTful architecture can benefit greatly from a service-distributed approach where components are scalable and modular.

You can tack on two additional components: a **reverse proxy** for implementing an intermediate cache and for horizontal scalability, and a **CDN service** for another intermediate cache leading to less server bandwidth use and cost savings.

![[layered-architecture.png]]

#### 6. Uniform Interface
REST's uniform interface is really what separates it from other network-based architectural styles. In a nutshell, a uniform interface makes it so that the API responds not only with the requested data, but also makes the client aware of how it works and how it can be used to request further data.

**Guiding principles**
1. **identification of resources** - each individual resource must be identified by a URI
2. **manipulation through representations** - the representation sent to the client must contain enough info to modify or delete the resource
3. **self-descriptive resources** - the representation sent to the client must contain all the required info to process the data e.g headers
4. **HATEOS** - clients should be able to interact with the API without prior knowledge 

---
### API Documentation

```csharp
builder.Services.AddSwaggerGen(); // generates a swagger.json file

app.UseSwagger(); // exposes the JSON file in a configurable endpoint
app.UseSwaggerUI(); // enables the UI for browsing the documentation
```

### API Versioning
**Versioning** a product means assigning a unique version number to each release.

**Why use API versioning?**
APIs evolve over time. Bug fixes and new features can cause system integrations to break, making clients that consume the API break as well. API versioning reduces the risk of breaking consumers of the API by publishing a new version on a new location without removing the old versions. This way, clients won't be forced to migrate right away and the new version can be tested by those who are ready to embrace the change.

prev: [[3 - RESTful Principles & Guidelines]]
next: [[4 - Working with Data]]