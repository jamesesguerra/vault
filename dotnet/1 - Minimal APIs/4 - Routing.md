Routing is the process of mapping an incoming request to an endpoint that will handle it. Routing in .NET Core is used in minimal APIs, Razor Pages, and .NET MVC.

In minimal APIs, the middleware responsible for routing the is `UseRouting` middleware which selects a handler based on a URL. It attaches which endpoint will handle the request and records it in the `HttpContext` object. This object can then be accessed by the endpoint middleware which executes the selected endpoint and returns the response.

### Endpoint Routing
Endpoint routing is a way for all .NET Core apps to use the same routing system. Each endpoint has a route template which defines which URLs will match it.

- **EndpointRoutingMiddleware / RoutingMiddleware** - chooses which endpoint to execute for a given request at runtime
- **EndpointMiddleware** - runs last in the middleware pipeline; executes the endpoint that was chosen

This means that all the middleware after the Routing middleware can access the `HttpContext` object to see which endpoint will be invoked, before it actually is. This also implies that it's a good place to put something like an Authorization middleware to check if the user is authorized to execute the selected endpoint by accessing the endpoint's metadata.

#### Segments
Routes are divided into segments, with each segment being either:
- a literal value, such as `product`
- or a route parameter, such as `{id}`

```
/parameter/{id}
```

Segments are usually separated by a `/`, but in reality, any character can be used.  Segments are case-insensitive, and literals have to match the URL exactly. On the other hand, route parameters can take on any value, but they are required.

**Optional parameters**
You can mark parameters as optional with the `?`. Alternatively, you can give a route parameter a default value by using a `=`. For optional parameters without a default, their values have to be specified at the end of the route template.

```
literal   required   default   optional

/product/{category}/{name=all}/{id?}
```

**Route parameter constraints**
It's possible to constrain the values that a route parameter can take since a simple route parameter such as `{id}` will match both `/1` and `/one`. You can add constraints based on the data type and/or the length / value of the parameter.

examples:
- `{id:int}` - restrict values to integers
- `{age:min(18)}` - matches integer values of 18 or greater
- `{name:length(6)}` - matches string values with a length of 6

**Catch-all parameters**
This is similar to rest in JS in that it allows you to capture all the values after a specified point. For example, if you wanted to capture all the currencies you want to convert to in a route template like: `USD/convert/GBP/EUR/CAD`, you can do this: and the conversions will be stored as a string.

```csharp
app.MapGet("/{currency}/convert/{**options}", (string currency, string conversions) => { })
```

#### Route Generation
There are also instances where you want to *generate* links. In a sense, this is the opposite of routing. Routing takes in a URL, matches it with a template, and splits the route values. In link generation, you specify route values and combine them with a template to make a URL. For this to work, you need to use the `LinkGenerator` class

The `GetPathByName` generates a link path (e.g. `products/big-widget`). In the example, you name an endpoint with `.WithName` so you can reference it from other endpoint handlers.
```csharp
app.MapGet("/product/{name}", (string name) => {
	return "The product is " + name;
})
   .WithName("product");

app.MapGet("/links", (LinkGenerator links) => {
	var link = links.GetPathByName("product", new { name = "big-widget" });

	return $"View the product at {link}";
});
```

If you want to generate a full URL, use the `GetUriByName` method which takes in the scheme and host as well.
```csharp
var link = links.GetUriByName("product",
	new { Name = "fancy-big-widget" }, "https", new HostString("localhost")
);
```

In some cases, you also want to redirect one endpoint to the URL you just generated, instead of simply returning it. For this use case you can use the `Results` class and its `RedirectToRoute` method which also references a named endpoint. You can also redirect to another URL explicitly with `Redirect`.

```csharp
app.MapGet("/test", () => {
	return "hello world";
})
	.WithName("hello");

app.MapGet("/redirect-me", () => {
	return Results.RedirectToRoute("hello");
	return Resutlts.Redirect("https://google.com");
})
```

prev: [[3 - Minimal APIs]]
next: [[5 - Model Binding]]