#### Route Attributes
Routing in web API controllers are very similar to minimal APIs. In web APIs, you decorate each action method with an attribute and provide the associated route template for the action method.

```csharp
public class HomeController : ControllerBase
{
	[Route("")]
	public IActionResult Index() { }
}
```

You can also use multiple route attributes to have the action method response to multiple URLs
```csharp
[Route("")]
[Route("blank")]
public IActionResult Index() { }
```

The name of the controller and action method have no bearing on the route templates. And as with minimal APIs, your route parameters can:
- be optional
- have default values
- use route constraints

#### Route Groups
Similar to minimal APIs, web API controllers can also use route groups to reduce repetition. You can use this to reduce the amount of repetition when it comes to the prefixes of route templates.

```csharp
[ApiController]
[Route("api/fruit")]
public class FruitController : ControllerBase
{
	[HttpGet("")] // matches api/fruit
	public IEnumerable<string> Index()
	{
		return _fruit;
	}
	
	[HttpGet("{index}")] // matches api/{index}
	public ActionResult<string> GetAtIndex(int index)
	{
		return Ok(_fruit[index]);
	}
}
```