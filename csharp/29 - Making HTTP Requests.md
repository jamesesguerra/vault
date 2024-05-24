You can make HTTP requests to an API by using the `HttpClient` class
```cs
using var client = new HttpClient();
```

You then specify its base address, and then use the `GetAsync` method together with the request URI to make the request
```cs
client.BaseAddress = new Uri("https://datausa.i/api/");

var response = await client.GetAsync("data?drilldowns=Nation&measures=Population");
response.EnsureSuccessStatusCode();
```

You then read the message as a string
```cs
string json = await response.Content.ReadAsStringAsync();
```

### Converting to a Type
The types you read from the content will be strings. You can then deserialize them into their objects either using the built-in `System.Text.Json.Serialization` namespace, or the 3rd party `Newtonsoft.Json` namespace.

1. (Optional) Paste the type in Json2Csharp converter to generate a class / record you can place in your code.
2. Deserialize the object 

using the built-in class:
```cs
var listOfTodos = JsonSerializer.Deserialize<IEnumerable<Root>>(content);
```

using `Newtonsoft`:
```cs
var listOfTodos = JsonConvert.DeserializeObject<IEnumerable<Root>>(content);
```