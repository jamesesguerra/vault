.NET Web API controllers sit somewhere in between minimal APIs and Razor pages that use MVC controllers. Web APIs use the MVC Framework as it follows the MVC design pattern as well. In this case, even though the View that displays the data isn't necessarily a UI that is user-friendly, the View is a response that has to be *machine-friendly*.

One of the biggest features of web API controllers is the `ApiController` attribute which applies several common conventions used in web APIs.

#### Starting Project

To create a Web API project, you can do
```sh
dotnet new webapi -o WebAPI -controllers
```

Upon checking `Program.cs`, you'll notice that the API controllers are added using `MapControllers()` instead of `MapControllerRoute()`, as web API controllers typically use explicit routing instead of conventional routing.

The starter code in `Program.cs` instructs your application to find all the web API controllers in your app and configure them in the `EndpointMiddleware`. Each action method then becomes an endpoint and can receive requests when the `RoutingMiddleware` maps an incoming URL to the action method.

#### A simple controller
Controllers are typically placed in a `Controllers` folder, but this has been changing with stuff like the vertical slice architecture.

When creating a new controller, you typically want to use the `[ApiController]` attribute and derive from the `ControllerBase` class. This base class provides several helper methods for generating results such as `Ok()` and `NotFound()`, and the attribute applies common conventions.

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
public class FruitController : ControllerBase
{
	List<string> _fruit = new List<string> { "Pear", "Lemon", "Peach" };

	[HttpGet("fruit")]
	public IEnumerable<string> Index()
	{
		return _fruit;
	}
}
```

##### IActionResult
When you return data from an action method like this, it's already formatted into an appropriate response, and sent to the browser with an appropriate 200 status code. You can return data directly like this, but you're free tor return an `IActionResult` instead, which is often required.

`IActionResult` is equivalent to minimal APIs' `IResult`. It allows you to flexibly return different responses from your endpoint handler / action method.

```csharp
[HttpGet("fruit/{index}")]
public ActionResult<string> GetAtIndex(int index)
{
	if (index >= 0 && index < _fruit.Count)
	{
		return _fruit[index];
	}
	return NotFound();
}
```

Using the generic `Action<T>` class like in this example allows you to conveniently return either an HTTP status such as `Ok(string)`, `NotFound()`, or simply, a string.

#### MVC aspects
Web API controllers are similar to Razor page controllers in that they both have an application model that returns data necessary to service a request. They differ, however, in the fact that Razor page controllers add values to the `PageModel` to be used in the Razor template, whereas action methods inside controllers return an API model.

Both responses have metadata attached to it. `PageModel`s have the UI that describes how to render the response in HTML, while API models have output formatters that describes how to serialize the response.