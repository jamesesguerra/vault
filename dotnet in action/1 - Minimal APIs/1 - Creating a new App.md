
Creating a new .NET Core app entails 4 steps
1. **Generate** a template to get started
2. **Restore** all the packages and dependencies to the local folder using NuGet
3. **Build** - compile the application to generate the necessary assemblies
4. **Run** the compiled assemblies

### Projects and Solutions
A **project** is an individual folder of files that get built into one assembly. A **solution** groups projects together that can be built and developed together.

#### Using an empty template
1. You can create an empty template with VS 2022 by:
	- Clicking `Create a New Project > File > New > Project`
	- Select `ASP.NET Core Empty` using C# from the list of templates
	- Enter details like the project and solution name
	- Select the .NET runtime, ensure that `Configure for HTTPS` is checked and that `Do not use top-level statements` is not
	- `Create`

	You can also use the dotnet CLI to do the same
```sh
mkdir WebApplication1 && cd WebApplication1
dotnet new sln -n WebApplication1
dotnet new web -o WebApplication1
dotnet sln add WebApplication1
```


2. You can then restore, build and run the project. By default, VS2022 and the dotnet CLI already restore the packages for you so it's not necessary. But you can carry these commands out with
```sh
# from within the project folder
dotnet restore
dotnet build
dotnet run
```

![[dotnet-run-process.png]]

### Files in a typical template
1. `Connected Services` and `Dependencies` folders - are the project's dependencies that aren't stored on disk
2. `Program.cs` - contains the startup and configuration code; typically uses top-level statements so that you don't have to write the main method yourself
3. `.csproj` - file that has the information how to build the project; this includes the runtime, the type of app (web, console, shared library), as well as the NuGet packages used by the project
4. `Properties/launchSettings.json` - tells VS2022 how to build and debug the app
5. `appsettings` - contains configuration that alters the behavior of the app at runtime
6. `.sln` - the solution file that groups projects together; installing NuGet packages can also be done at the solution level so that all projects under it can reference that package


### `WebApplication` instance
What the `Program.cs` file does is create a builder called `WebApplicationBuilder` to create an instance of `WebApplication`. The `WebApplication` class is the core of the application as it has the application configuration as well as Kestrel server which receives requests and sends responses.

This builder which builds a complex object is a design pattern from the GOF book. So typically, you create the builder, configure it to your liking, and then only after that will you build the `WebApplication` instance. But the builder does some configuration already by default. 
```csharp
var builder = WebApplicationBuilder.CreateBuilder(args); // defines the app configuration
var app = builder.Build(); // creates the WebApplication instance with the configuration in mind
```

The builder defines **how your app runs**, while the app defines **how you respond to requests**. And your app can respond to request with 2 main building blocks:
- **middleware** - small components that execute in sequence that can perform a whole range of tasks such as logging, error handling, etc.
- **endpoints** - defines how the response should be generated

```csharp
app.MapGet("/", ()) => "Hello world!");
```

#### Builder and services
In this example, the `Services` property of the `builder` is used to register services to be used in the app. `IServiceCollection`, the interface of `Services`, is basically a list of services that your app needs to know about and use. By adding services to this collection, the IoC container will know how to provide it.

Also, after you call `builder.Build()`, you cannot register any more services and configuration. 

```csharp
using Microsoft.AspNetCore.HttpLogging;

var builder = WebApplication.CreateBuilder(args); // 1. create the webapp builder

builder.Services.AddHttpLogging(opts =>      // 2. register the services and configuration for the app om the container
    opts.LoggingFields = HttpLoggingFields.RequestProperties);

builder.Logging.AddFilter(
    "Microsoft.AspNetCore.HttpLogging", LogLevel.Information);

var app = builder.Build();       // 3. build the app

if (app.Environment.IsDevelopment())
{
    app.UseHttpLogging();        // 4. add middleware (conditionally) to create a pipeline
}

// 5. define endpoint to map to
app.MapGet("/", () => "Hello World!");
app.MapGet("/person", () => new Person("James", "Esguerra"));

// 6. run the app`
app.Run();

public record Person(string FirstName, string LastName);
```

### Middleware and Endpoints
Once you've built the `WebApplication`, you can do 3 main things.
- add middleware
- map endpoints that generate a response for a request
- run the app

**Middleware** are small components that execute sequentially when the app receives an HTTP request and define how your app responds to requests. Because they're executed in sequence, it's also important to consider the order in which middleware is added to the app as this order is the order that will be used when the app is running.

You can conditionally add middleware depending on the current environment by checking the apps `Environment` property. The `Environment` property exposes several properties like
- `ContentRootPath` - the location of the current working directory of the app, typically the folder in which the app is running
- `WebRootPath` - location of the `wwwroot` folder that contains static files
- `EnvironmentName` - the name of the environment

The first piece of middleware that is added by default is the routing middleware. On the other hand, the last piece is the endpoint middleware. The routing middleware checks the request URL to determine which endpoint to invoke, and the endpoint middleware calls the endpoint to generate the final response.


![[http-request-process.png]]

prev: [[dotnet in action/1 - Minimal APIs/0 - Introduction|0 - Introduction]]
next: [[2 - Middleware]]
