Routing is the process of mapping an incoming request to an endpoint that will handle it. Routing in .NET Core is used in minimal APIs, Razor Pages, and .NET MVC.

In minimal APIs, the middleware responsible for routing the is `UseRouting` middleware which selects a handler based on a URL. It attaches which endpoint will handle the request and records it in the `HttpContext` object. This object can then be accessed by the endpoint middleware which executes the selected endpoint and returns the response.

### Endpoint Routing
Endpoint routing is a way for all .NET Core apps to use the same routing system. Each endpoint has a route template which defines which URLs will match it.

- **EndpointRoutingMiddleware / RoutingMiddleware** - chooses which endpoint to execute for a given request at runtime
- **EndpointMiddleware** - runs last in the middleware pipeline; executes the endpoint that was chosen