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