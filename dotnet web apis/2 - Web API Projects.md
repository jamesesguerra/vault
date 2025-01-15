#### launchSettings.json
Contains some configuration settings related to how the project should be launched **in development mode**. That also means this file won't be deployed with our app in production. You can tweak configuration on TCP ports used and environment variables.

- `iisSettings` - contains launch configuration when using the IIS web server
- `profiles` - contains other settings specific to IIS and Kestrel web servers

#### appsettings files
- `appsettings.json` - is meant to store configuration settings that will be used by all the runtime environments, unless they're overridden or complemented by environment-specific files
- `appsettings.Development.json` - Development-specific configuration file that will override / complement the base configuration file

```json
{
	...,
	"UseDeveloperExceptionPage": false
}
```

#### Program.cs
Creates a web application builder and adds and configures services to control *how* the app runs. Creates a web application instance, adds middleware, registers controller endpoints, and runs the app to control *how the app response to HTTP requests*.

One helpful middleware to add in development is the `DeveloperExceptionPageMiddleware` which captures exceptions from the HTTP request pipeline and generates a helpful HTML error page. We only want to display this in development since it could expose sensitive information. The `ExceptionHandlingMiddleware` also handles HTTP-level exceptions but is better suited to nondevelopment scenarios where it delegates the error path to a customizable handler.

```csharp
if (app.Environment.IsDevelopment())  
{  
    app.UseSwagger();  
    app.UseSwaggerUI();  
    app.UseDeveloperExceptionPage();  
}  
else  
{  
    app.UseExceptionHandler("/error");  
}

// or this if you wanna use a configuration setting instead of having to change the environment
if (app.Configuration.GetValue<bool>("UseDeveloperExceptionPage"))  
{  
    app.UseDeveloperExceptionPage();  
}  
else  
{  
    app.UseExceptionHandler("/error");  
}
```

You can create such a customizable error handler by creating and action method that maps to `/error`. In this case, we're returning the result of `Problem()` which generates problem details using the default values thrown by the exception.

```csharp
[ApiController]  
public class ErrorController : ControllerBase  
{  
    [Route("/error")]  
    [HttpGet]  
    public IActionResult Error()  
    {
	    return Problem();  
    }
}

// or with minimal APIs
app.MapGet("/error", () => Results.Problem());
```

prev: [[1  - Web APIs at a Glance]]
next: [[2 - Web API Projects]]