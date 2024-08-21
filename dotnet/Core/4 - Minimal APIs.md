Minimal APIs are the default app paradigm for .NET Core apps i.e. if you create a default /empty web app, it's already configured to use minimal APIs.

You create endpoints by: 
```csharp
app.MapGet("/" => "hello, world");
```

And by default, minimal APIs serialize C# objects to JSON.

### Dynamic Segments
You don't want your endpoints to be static as you want certain parts to be dynamic to change the behavior of the endpoint. You can do this by specifying parameters in the endpoint with `{}`. The lambda expression that handles the endpoint can then receive the dynamic value as an argument.
```csharp
app.MapGet("/person/{name}", (string name) => 
	people.Where(p => p.FirstName.StartsWith(name))
);
```

### Map endpoints
![[minimal-api-mapendpoints.png]]

Endpoint handlers can be functions of different kinds:
- lambdas
- funcs
- local functions
- static methods
- instance methods

And for HTTP verbs that expect data to be passed in through the JSON body, you can specify it also as a parameter to the function, and C# will automatically deserialize it into its corresponding C# object.

```csharp
app.MapPost("/fruits/{id}", (string id, Fruit fruit) => Fruits.All.Add(id, fruit));
```

### Returning status codes with IResult
Showing a user the developer exception page is a poor choice from the user's POV as they won't know much about what's going on. It's better to return an HTTP status code together with the data if need be. If you return an `IResult` / `Task<IResult>` type from the endpoint, it executes the `IResult.ExecuteAsync` method, and you can customize the status code from there.

You can use the static helpers `Results` and `TypedResults` to create a response with common status codes.

```csharp
app.MapGet("/fruits/{id}", (string id) =>
		  _fruit.TryGetValue(id, out var fruit)
			  ? TypedResults.Ok(fruit)
			  : Results.NotFound());

app.MapPost("/fruits/{id}", (string id, Fruit fruit) =>
	_fruit.TryAdd(id, fruit)
		? TypedResults.Created($"/fruit/{id}", fruit)
		: Results.BadRequest(new
		{ id = "A fruit with this is already exists" }));

app.MapPut("/fruits/{id}", (string id, Fruit fruit) => {
	_fruit[id] = fruit;
	return Results.NoContent();
});

app.MapDelete("/fruits/{id}", (string id) => {
	_fruit.TryRemove(id, out _);
	return Results.NoContent();
});
```

You also have the option of setting the status code yourself 

```csharp
app.MapGet("/teapot", (HttpResponse response) => {
	response.StatusCode = 200;
	response.ContentType = MediaTypeNames.Text.Plain;
	return response.WriteAsync("I'm a teapot");
});
```
#### Problem Details
Problem details defines a standard to use for the JSON output of HTTP responses with errors. It defines a set of key-value pairs that will include data such as the status code, title of the error, and an array of validation errors. This ensures that all apps that will consume the API will respond to the same API error format.

```csharp
app.MapGet("/fruits/{id}", (string id) =>
	_fruit.TryGetValue(id, out var fruit)
		? Results.Ok(fruit)
		: Results.Problem(statusCode: 404));

app.MapPost("/fruits/{id}", (string id, Fruit fruit) =>
	_fruit.TryAdd(id, fruit)
		? TypedResults.Created($"/fruit/{id}", fruit)
		: Results.ValidationProblem(new Dictionary<string, string[]>
		{
			{ "id", new[] { "A fruit with this id already exists"}}
		}));
```

You can also make it so that both errors (caused by middleware, other functions, etc) and endpoint error status code responses return problem details. In this case, errors caught by the exception handler middleware will return the developer exception page if the client supports HTML, and the problem details as JSON if it doesn't.

```csharp
builder.Services.AddProblemDetails();
```
However, not all HTTP error status codes will be covered here since if you hit an endpoint that doesn't exist for example, the 404 error won't include problem details. To cover all error status codes with problem details, add the `UseStatusCodePages` middleware
```csharp
app.UseStatusCodePages();
```

### Endpoint filters
If you have common code that you might find yourself adding to multiple endpoints, this could be a sign that it's a function with cross-cutting concerns.
```csharp
app.MapGet("/fruits/{id}", (string id) => {
	// code that clutters the main logic of the endpoint
	if (string.IsNullOrEmpty(id)|| !id.StartsWith("f"))
	{
		return Results.ValidationProblem(new Dictionary<string, string[]>
		{
			{ "id", new[] { "Invalid format. Id must start with 'f'"} }

		});
	}

	return _fruit.TryGetValue(id, out var fruit)
		? Results.Ok(fruit)
		: Results.Problem(statusCode: 404);
});
```

Endpoint filters allow you to define code that is run before the endpoint handler, possibly short-circuiting much like how middleware does. You can define multiple endpoint filters that will eventually run in a pipeline also like middleware. Each filter will have access to the HTTP context (and therefore also the arguments), as well as a function to call the next filter in the pipeline.

![[endpoint-filters.png]]

```csharp
app.MapGet("/fruits/{id}", (string id) => {
	return _fruit.TryGetValue(id, out var fruit)
		? Results.Ok(fruit)
		: Results.Problem(statusCode: 404);
})
	.AddEndpointFilter(ValidationHelper.ValidateId);

public class ValidationHelper
{
	public static async ValueTask<object?> ValidateId(
		EndpointFilterInvocationContext context,
		EndpointFilterDelegate next)
	{
		var id = context.GetArgument<string>(0);

		if (string.IsNullOrEmpty(id) || !id.StartsWith("f"))
		{
			return Results.ValidationProblem(
				new Dictionary<string, string[]>
				{
					{ "id", new[] { "Invalid format. Id must start with 'f'" } }
				}
			);
		}
	return await next(context);
	}
}
```
#### Middleware vs Endpoint filters
They're both for cross-cutting concerns, but middleware is for functionality with a wider reach and operates on low-level concepts like the `HttpContext` object.Endpoint filters are for endpoint-specific requirements.

---
### Route Groups
To reduce the duplication of code in the prefixes of the endpoints, as well as with adding endpoint filters, you can use route groups. Route groups allow you to define a prefix for every endpoint under that route group. Also, if you add an endpoint filter to the route group builder, the filter will be applied to all the endpoints under the route group.

```csharp
var fruitApi = app.MapGroup("/fruits");

fruitApi.MapGet("/", () => Fruit.All);

var fruitApiWithValidation = app.MapGroup("/fruits")
								.AddEndpointFilter(ValidationHelper.ValidateId);

fruitApiWithValidation.MapPost("/fruits/{id}", (string id, Fruit fruit) =>
	_fruit.TryAdd(id, fruit)
		? TypedResults.Created($"/fruit/{id}", fruit)
		: Results.ValidationProblem(new Dictionary<string, string[]>
		{
			{ "id", new[] { "A fruit with this id already exists"}}
		}
));
```