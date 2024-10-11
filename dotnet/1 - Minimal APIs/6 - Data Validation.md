Data can come from many source in a web app. And so you need a way to validate them before anything touches your infrastructure or anything that could leak information to an attacker.

- data should be formatted correctly
- numbers should be in a particular range
- some values are required, while others are optional

#### DataAnnotation attributes
You can use `DataAnnotations` to specify the rules your parameters should conform to. They provide metadata that describes the sort of data your parameters will have apart from the data type they contain

```csharp
using System.ComponentModel.DataAnnotations;

class UserModel {
	[Required]
	[StringLength(100)]
	public string Name { get; set; }
}
```

If the attributes don't provide what you need right out of the box, you can write custom attributes and derive from the base `ValidationAttribute`. Also, if you want to implement behavior of an attribute that depends on another attribute, you can implement the `IValidatableObject`  interface.

To actually enforce the rules of the data annotations, you need a validation API for Minimal APIs. This is different from MVC controllers and Razor Pages who have the validation API already built-in. On the bright side, you're free to use whatever validation API you want. You can download an already existing package that exposes a way to tack on a filter to the endpoints to enforce validation.

```sh
dotnet add package MinimalApis.Extensions
```

You can then add validation to the endpoints using the `WithParameterValidation` method. If the parameters are valid, the endpoint handler gets executed. But if not, a `400 Bad Request` is returned together with a problem details object.

```csharp
app.MapGet("/users", (UserModel user) => user).WithParameterValidation();
```

This works for complex types, but if the validation filter sees that the parameter doesn't have a data annotations filter, it won't validate it. For this, you can encapsulate the simple type in a class and use `[AsParameters]`.

```csharp
app.MapGet("/user/{id}", ([AsParameters] UserModel user) => user).WithParameterValidation();

class UserModel {
	[Range(1, 10)]
	public int Id { get; set; }
}
```

