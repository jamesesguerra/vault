### Custom Validation Attribute
1. Add a new class file to represent the new validation attribute
2. Extend the `ValidationAttribute` base class
3. Override the `IsValid` method with your own implementation
4. Configure and return a `ValidationResult` object containing the outcome

```csharp
public class SortOrderValidatorAttribute : ValidationAttribute  
{  
    public string[] AllowedValues { get; set; } = { "ASC", "DESC" };  
    public SortOrderValidatorAttribute() : base("Value must be one of the following {0}") { }  
  
    protected override ValidationResult? IsValid(  
        object? value,  
        ValidationContext validationContext)  
    {        var strValue = value as string;  
  
        if (!string.IsNullOrEmpty(strValue)  
            && AllowedValues.Contains(strValue)) return ValidationResult.Success;
              
        return new ValidationResult(  
            FormatErrorMessage(string.Join(",", AllowedValues)));  
    }
}
```

You can also create validation attributes that rely on the properties of a certain type. This uses reflection to check whether the sort column in the request is a valid column for that entity.

```csharp
public class SortColumnValidatorAttribute : ValidationAttribute  
{  
    public Type EntityType { get; set; }  
  
    public SortColumnValidatorAttribute(Type entityType)  
        : base("Please enter a valid Sort Column")  
    {
	    EntityType = entityType;  
    }  
    
    protected override ValidationResult? IsValid(  
        object? value,  
        ValidationContext validationContext)  
    {
	    var strValue = value as string;  
        if (!string.IsNullOrEmpty(strValue)  
            && EntityType.GetProperties()  
                .Any(p => p.Name == strValue)) return ValidationResult.Success;  
        
        return new ValidationResult(  
            FormatErrorMessage(ErrorMessage));  
    }
}
```

```csharp
public class RequestDTO {
	[SortColumnValidatorAttribute(typeof(BoardGameDTO))]
	public string SortColumn = "NAME";
}
```

The downside to using this approach of extending `ValidationAttribute` is that you can't use it with generics. This is because attributes that decorate a class are evaluated at compile time, but a generic `<T>`  class will only receive its final type at runtime. As a result, attributes can't use generic type parameters. This means that you can't reuse the `RequestDTO` for other types without the help of something else.

### `IValidatableObject`
This is where the `IValidatableObject` comes to the rescue. It works like a class-level attribute in that in can validate its properties. It has 2 major advantages:
- it isn't required to be defined at compile time, so it can use generic types
- it's designed to validate the class in its entirety, so we can use it to check multiple properties at the same time (cross-validation checks)

```csharp
public class RequestDTO<T> : IValidatableObject  
{  
	// ...
    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)  
    {        var validator = new SortColumnValidatorAttribute(typeof(T));  
  
        var result = validator.GetValidationResult(SortColumn, validationContext);  
  
        return (result != null)  
            ? new[] { result }  
            : [];  
    }}
```

### Custom Error Handling
If you wanna override the default error messages given in response to model binding / validation errors:
```csharp
builder.Services.AddControllers(options =>  
{  
    options.ModelBindingMessageProvider.SetValueIsInvalidAccessor(  
        x => $"The value {x} is invalid");  
  
    options.ModelBindingMessageProvider.SetValueMustBeANumberAccessor(  
        x => $"The field {x} must be a number");  
  
    options.ModelBindingMessageProvider.SetAttemptedValueIsInvalidAccessor(  
        (x, y) => $"The value {x} is not valid for {y}");  
  
    options.ModelBindingMessageProvider.SetMissingKeyOrValueAccessor(  
        () => "A value is required");  
});
```

#### Customizing the exception handling middleware
By default, you would have something like this to send back a RFC-compliant response whenever errors occur.

```csharp
app.MapGet("/error", [ResponseCache(NoStore = true)] () => Results.Problem());
```

However, this might not work for all cases. There might be cases when you'd want to customize the HTTP status code, message, and so on. You can inject `HttpContext` in action methods, and use an `IExceptionHandlerPathFeature` to access the originating exception. You then create a new `ProblemDetails` instance yourself, configuring it depending on your needs / the type of exception that occurred.

```csharp
app.Map("/error", [ResponseCache(NoStore = true)] (HttpContext context) =>  
{  
    var exceptionHandler = context.Features.Get<IExceptionHandlerPathFeature>();  
    var details = new ProblemDetails();  
    
    details.Detail = exceptionHandler?.Error.Message;  
    details.Extensions["traceId"] =  
	    System.Diagnostics.Activity.Current?.Id  
	        ?? context.TraceIdentifier;  
    details.Type = "https://tools.ietf.org/html/rfc7231#section-6.6.1";  
    details.Status = StatusCodes.Status500InternalServerError;  
    return Results.Problem(details);  
});
```

You can also customize the properties based on the exception. For example, you can customize the status:
```csharp
details.Status = exceptionHandler?.Error switch  
{  
    NotImplementedException => StatusCodes.Status501NotImplemented,  
    TimeoutException => StatusCodes.Status504GatewayTimeout,  
    _ => StatusCodes.Status500InternalServerError  
};
```

#### Exception handling action
If you don't want to create a separate endpoint for errors, you can configure the `ExceptionHandlerMiddleware` to run an action instead.
It's almost the same as the endpoint, except you'd have to write out the details manually to the buffer.

```csharp
app.UseExceptionHandler(action => {
	action.Run(async context => {
		var exceptionHandler = context.Features.Get<IExceptionHandlerPathFeature>();

		var details = new ProblemDetails();
		details.Detail = exceptionHandler?.Error.Message;
		details.Exception["traceId"] = 
			System.Diagnostics.Activity.Current?.Id  
	            ?? context.TraceIdentifier;  
	    details.Type = "https://tools.ietf.org/html/rfc7231#section-6.6.1";  
	    details.Status = StatusCodes.Status500InternalServerError;  
	    await context.Response.WriteAsync(
		    System.Text.Json.JsonSerializer.Serialize(details));
	});
});
```

prev: [[5 - CRUD with LINQ]]
next: [[6 - Data Validation & Error Handling]]