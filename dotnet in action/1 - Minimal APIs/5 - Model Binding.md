**Model binding** is the process of turning a user's raw HTTP request and making it available to your code, mapping it to data types like POCOs.

A model binder looks through a HTTP request URL, its headers, etc. for values it can bind to the endpoint handler. There are 6 binding sources a binder can use:
- **route values** from URL segments (route parameters)
- **query string values** passed at the end of a URL
- **header values** in the HTTP request
- **JSON body**
- **DI services**
- **Custom binding** ie the `HttpRequest` object (manual binding)

#### Simple types
When an endpoint handler contains parameters that are simple types, the model binder automatically tries to bind to a route parameter with the same name first. Otherwise, it tries to bind to a query string with the same name. Simple types are types that have a `TryParse` method, even custom data types can be simple types even they implement a `TryParse`.

```csharp
app.MapGet("/products/{id}", (int id) => id); // matches /products/4 , id = 4
app.MapGet("/products", (int id) => id); // matches /products?id=4 , id = 4
```

You can also mark parameters as optional and not throw an exception if a parameter in the endpoint handler can't be bound from any source by making them nullable...
```csharp
app.MapGet("/products", (int id?) => $"Received {id}")
```

and also add default values
```csharp
app.MapGet("/products", (int id = 0) => $"Received {id}")
```

**Forced binding**
In some cases, you might want to force the model binder to bind a parameter from a specific source:
- `[FromRoute]` - binds to route parameters
- `[FromQuery]` - binds to query strings
- `[FromHeader]` - binds to header values

```csharp
app.MapGet("/products/{id}", (
	[FromRoute] int id,
	[FromQuery] string? page,
	[FromHeader(Name = "maxResult")] string? maxResult) => $"Received {id} {page} {maxResult}" );
```

It's important to note that if the binder is unable to bind to a required parameter, it throws a `400 Bad Request` error.

#### Complex types
The model binder can bind a JSON body value to a complex type parameter in the endpoint handler. Note that you can only bind one complex type parameter to the JSON body, you can only bind a JSON body, and this binding only works for POST endpoints unless you force it with `[FromBody]`.

```csharp
app.MapPost("/product", (Product product) => {
	return $"Received {product.Id} {product.Name}";
});

record Product(int Id, string Name);
```

#### Array types
To bind array values from a query string, you pass multiple values of a query string with the same name, and use an array as the parameter in the endpoint handler. You can control the name that the model binder binds to from the query string with an attribute

```csharp
// matches /products?id=1&id=2&id=3
app.MapGet("/products", ([FromQuery(Name = "id")]int[] ids) => { });
```

#### Well-known types
You can also inject in well-known types, such as `HttpContext` and more granular versions of it that contain information about the HTTP request and response, as well as services from the DI container, by simply adding them to the parameter list of the endpoint handler.

- `HttpContext` - contains information about the HTTP request and response
- `HttpRequest` - contains information about the request only; equivalent to `HttpContext.Request`
- `HttpResponse` - contains information about the response only; equivalent to `HttpContext.Response`

#### AsParameters
Once you start building routes, the parameter list of models you want to bind to could start to get messy. If you want to encapsulate all of them into 1 class / struct, you can use the `[AsParameters]` attribute.  

**NOTE:** This is only required in Minimal APIs. This type of model encapsulation works out of the box in MVC controllers.

prev: [[4 - Routing]]