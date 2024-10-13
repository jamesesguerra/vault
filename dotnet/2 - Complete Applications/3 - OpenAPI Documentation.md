OpenAPI is a language-agnostic specification for describing RESTful APIs. It can discover endpoints on your app and create an OpenAPI document that can be exposed as an HTML document to which you can test your endpoints easily.

The two best-know NuGet packages to use are NSwag and Swashbuckle.
```sh
dotnet add package Swashbuckle.AspNetCore
```

You can add OpenAPI support to a minimal API app using Swashbuckle by:

```csharp
var builder = new WebApplication.CreateBuilder(args);

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

app.UseSwagger();
app.UseSwaggerUI();
```

You can then view the OpenAPI document description of your API at `/swagger/v1/swagger.json`. You can optionally edit this metadata.

```csharp
builder.Services.AddSwaggerGen(x =>
	x.SwaggerDoc("v1", new OpenApiInfo()
	{
		Title = "Fruitify",
		Description = "An API for interacting with fruit stock",
		Version = "1.0"
	})
);
```

Initially, the HTTP status code responses on Swagger UI won't be accurate as they will be missing 404 or other error codes. To fix this, you can add metadata to your endpoints. You can also add tag metadata to tell the specification how to group your endpoints.

```csharp
app.MapGet("/fruit/{id}", (string id) =>
	_fruit.TryGetValue(id, out var fruit)
		? TypedResults.Ok(fruit)
		: Results.Problem(statusCode: 404))
	.WithTags("fruit")
	.Produces<Fruit>()
	.ProducesProblem(404);

app.MapPost("/fruit/{id}", (string id, Fruit fruit) =>
	_fruit.TryAdd(id, fruit)
		? TypedResults.Created($"/fruit/{id}", fruit)
		: Results.ValidationProblem(new Dictionary<string, string[]> {
			{ "id", new[] { "A fruit with this id already exists" }}
		}))
	.WithTags("fruit")
	.Produces<Fruit>(201)
	.ProducesValidationProblem();
```

