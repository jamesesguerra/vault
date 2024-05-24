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