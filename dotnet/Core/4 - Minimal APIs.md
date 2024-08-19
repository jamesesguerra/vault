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


