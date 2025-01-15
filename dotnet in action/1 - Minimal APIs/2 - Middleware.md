 **Middleware** are classes or functions that are run sequentially in a bidirectional pipeline, that define how your app responds to requests. They map to cross-cutting concerns i.e. they're responsible for a lot of things in different layers in your app e.g. logging, security.

They can:
- handle HTTP requests by generating a response
- process an HTTP request, alter it in some way, and pass it on to another piece of middleware
- process an outgoing HTTP alter it in some way, and pass it on to another piece of middleware, or to Kestrel

Some examples of cross-cutting concerns are:
- logging requests
- adding standard security headers to the response
- associating a request with the relevant user

### Endpoint middleware
The endpoint middleware is responsible for handling an HTTP request and generating a response. This doesn't include requests for static files as a static-file middleware is called before the endpoint middleware. This static-file middleware is responsible for generating static content without passing the request on any further.

### Authentication & Authorization
Middleware can access the details of a previously run piece of middleware to carry out its functionality. For example, an authentication middleware can associate the request of the current user. It will then pass the request on to the authorization middleware, and in turn, that piece can check if that user is authorized to make the request. If they aren't, the authorization middleware short-circuits the pipeline.

### HttpContext
The `HttpContext` object is an object created by the .NET Core web server that encapsulates all the data that can be associated with the HTTP request and its corresponding response.This object goes through the middleware pipeline, and each piece of middleware can access it in order to figure out how to respond to the request. After generating a response, the object goes all the way back to the web server, and the web server converts the object representation into raw HTML / JSON.

![[middleware-process.png]]


### Using middleware
You can use middleware after the `WebApplication` instance has been created by calling `Use-` methods on it. 

```csharp
var app = builder.Build();

app.UseHttpLogging();
```

You can use the `UseWelcomePageMiddleware` to test if your application can process requests without errors.
```csharp
app.UseWelcomePage();
```

You can use the `UseStaticFiles` middleware to serve static content from the `wwwroot` folder.
```csharp
app.UseStaticFiles();
```

Typical middleware in a minimal endpoint
```csharp
app.UseDeveloperExceptionPage();
app.UseStaticFiles();
app.UseRouting();

app.mapGet("/", () => "");
```

Even though .NET already adds some middleware by default, you can choose to use them explicitly to change the order in which they're called. 

### Error-handling middleware
Error-handling middleware handles errors once the response propagates back to the middleware pipeline. If the exception isn't caught earlier in the pipeline, the server will the return a 500. These pieces of middleware can also change the response to return more user-friendly responses.

There are 2 types that come packaged with .NET core by default:
- `UseDeveloperExceptionPage`
	- added by default on dev environment
	- only for dev environment as it exposes a lot of information about the code
- `UseExceptionHandler`
	- generates a user-friendly response without exposing too much information to the user
	- you can create a custom error page to maintain the UI of the app
	- this piece of middleware catches exceptions and "re-executes" the pipeline by calling the error-handling endpoint; if the error-handling path itself returns an error, the browser will receive a generic 500 page

```csharp
var builder = WebApplicationBuilder(args);
var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
	app.UseExceptionHandler("/error"); // point to an endpoint that returns the error page
}

app.MapGet("/error", () => "Sorry, an error occurred.");
app.Run();
```

prev: [[1 - Creating a new App]]
next: [[3 - Minimal APIs]]