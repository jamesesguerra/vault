### Services
Services are small, independent pieces of code (classes) written either by you, or someone else, that provides some functionality to your app. Each service should be responsible for a small piece, but you can combine them to create the complete application.

### The need for DI
If you have some services that rely on each other, for example, if `OrderTotalCalculator` relies on the functionality of the `ShippingCostService` and the `TaxCalculator` to work out the total price of an order, it needs to reference them somehow. The naive approach to this is creating an instance of these classes inside the `OrderTotalCalculator` and using their methods directly. However, this violates the single-responsibility principle because when some functionality of those classes change, it's very likely you'll have to change parts of `OrderTotalCalculator` as well.

One solution to this is to invert the control and make it someone else's problem. So inside `OrderTotalCalculator`, instead of creating these classes yourself, you can simply list your dependencies in the constructor, and let another class fill in those dependencies for you. Then the `OrderTotalCalculator` can focus on its functionality without worrying about anything else. 


**Problem #1**
- i have a `MovieFinder` class to find movies directed by a certain person
- this class uses another `MovieFinder` class to find all the movies from a certain source
- i create an implementation of a `Finder` interface that takes in a colon-delimited list of movies in a text file

```csharp
class MovieLister {
	private MovieFinder _finder;

	public MovieLister() {
		_finder = new ColonDelimitedMovieFinder("movies1.txt");
	}
}
```

- this is fine, until my friends wanna use the same implementation class, but have movies stored in other places e.g. a SQL DB
- now, this isn't possible since the `MovieLister` class is dependent on both the `MovieFinder` interface *and* the `ColonDelimitedMovieFinder` class implementation
- you want to be able to give your `MovieLister` to your friends, and make it possible for them to inject / plug in their own implementation for the `MovieFinder`

![[no-di.png]]

**Solution #1**
- **plugins** - we want our lister to work with any implementation, and for that implementation to be plugged in at some later point, out of our hands
- we also want our lister class to be ignorant of the implementation class, while also being able to interact with an instance of it to do its work
- **core problem** - how to assemble these plugins in an application? Inversion of Control


### Inversion of Control
The inversion is how containers lookup a plugin implementation. We want an assembler to create an appropriate implementation for the `MovieFinder` interface, and serve it to `MovieLister`

![[with-di.png]]

#### Dependency Graphs
If your code abides by the SRP, you'll soon realize that you have quite a handful of classes that have references to several other classes. This then creates the dilemma that the "root" object that is going to do the work, has to create all its dependencies + the dependencies of its dependencies. This creates a dependency graph which are the set of classes that must be created to create a certain "root" object.

DI solves the problem of dependency graphs by inverting the chain of dependencies. Instead of the root object creating dependencies, its dependencies will be passed into it by some piece of code. The service responsible for providing instances of the dependencies to the root object's constructor is called a **DI container** or **IoC container**.

After configuring a DI container, you can ask it for an instance of a service, and it will provide it while taking care of the dependency graph. In this case, a service just refers to any class or interface that a container creates when asked for it.

#### Implicit & explicit dependencies
Implicit dependencies is creating an instance of a class inside another class that needs it. Explicit dependencies are when you specify in the constructor parameters the dependencies a class needs. Generally, you want the dependencies in your classes to be explicit as they're easier to test and reason about.

#### Service Registration
The act of telling your program to use a certain implementation of an interface when providing services is called **registration**.  It's like saying *"When a service requires <class / interface>, use an instance of this class"*.

To register a service, you use the `Services` property of the `WebApplicationBuilder` instance in `Program.cs`. The `Services` property is of type `IServiceCollection` and this is where you register services that the DI container knows about.

**Existing  / built-in services**
Many features of .NET Core require services that need to be injected, but for those services, there is an extension method to wire everything up already which you can just add. These are usually prefixed by `Add` and can also take in a lambda to fine tune the services.

For example, if you wanted to add all the framework services for a Razor Pages application, you need only add this line
```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Service.AddRazorPages();
```

**Your own services**
Like built-in extension methods, you make statements about the instances you want to supply services to configure the DI container. These statements are made using the `Add*` methods on the `IServiceCollection`. Each method tells the DI container 3 things:
- **service type** - the class / interface that will be requested as a dependency
- **implementation type** - the class that must be created by the container to fulfill the dependency
- **lifetime** - defines how long an instance of a service should be used by the DI container

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IEmailSender, EmailSender>();
builder.Service.AddSingleton<NetworkClient>(); 
```

It seems a bit like magic, but your implementation type has to adhere to certain things as well so that the DI container can create it:
- it has to be a concrete type (class, struct)
- it has to have a relevant constructor the DI container can use:
	  - a constructor with arguments that are already registered to the container OR
	  - a constructor with arguments with default values

And so for types that have constructor arguments WITHOUT default values, you can supply the container with an instance yourself if you want a certain instance to be used each time one is requested. These instances have to be used with `AddSingleton` since they are singletons.
```csharp
builder.Services.AddSingleton(new EmailServerSettings(Host: "smtp.server.com"));
```

If you want to create a new one for every request, you can pass a function to create the instance. Now in this case, you wouldn't be restricted to using `AddSingleton` anymore. The `provider` variable that is passed to the lambda is an instance of `IServiceProvider`, which exposes the `GetService` / `GetRequiredService` methods to directly ask for an implementation.
```csharp
builder.Services.AddScoped(provider => new EmailServerSettings(Host: "smtp.server.com"));
```

#### Tidying Up
Creating all these statements to register services can make your code messy. To clean things up, you can place them inside an extension method, and call that one method in your `Program.cs` file instead.

```csharp
// extensions file
public static class ServiceExtensions
{
	public static IServiceCollection AddEmailServices(this IServiceCollection services)
	{
		services.AddScoped<IEmailSender, EmailSender>();
		services.AddScoped<NetworkClient, NetworkClient>();
		services.AddSingleton(new EmailServerSettings("smtp.gmail.com", 25));
		return services;
	}
}
```

```csharp
// Program.cs
builder.Services.AddEmailServices();
```

#### Injecting / Requesting Services
You can get a hold of an implementation of a service in one of 2 ways:
1. Have it injected for you in a class constructor / endpoint handler
2. Explicitly request for it from the `WebApplication` instance. It exposes a `Services` property of type `IServiceProvider` which acts as a service locator. You can use the following methods
	- `GetService<T>()` - returns the requested service `T` if available, otherwise `null`
	- `GetRequiredService<T>()` - returns the requested service `T` if available, otherwise throws an exception


Generally, you want to avoid the second way of doing it as this follows the service locator pattern. However, this is the only way to access services in the main loop of the application, so there is no problem using it there. But when it comes to other types, you want to inject it in the constructor / endpoint handler.

##### Injecting multiple instances of an interface
If you have multiple implementations of an interface you want to register and inject, you can start by registering the services:

```csharp
builder.Services.AddScoped<IMessageSender, EmailSender>();
builder.Services.AddScoped<IMessageSender, SmsSender>();
```

You can then inject all the services by injecting an array of the interface, which gives you access to all the implementations in the order you registered them:

```csharp
string RegisterUser(
	string username,
	IEnumerable<IMessageSender> senders)
{
	foreach (var sender in senders) { }
}
```

#### Service Lifetimes
How long do instances of a service last in the DI container? This is dictated by the lifetime you specify.

1. **transient** - every time an instance of a service is requested, a new instance is created
2. **scoped** - creates and shares an instance within a scope e.g. one HTTP request
3. **singleton** - you get the same instance of the service regardless of scope / how many times you call it

---
## Service Locator
An alternative to DI, but achieves the same result wherein the `MovieLister` isn't dependent on the `MovieFinder` implementation. A service locator is an object that knows how to get a hold of all the services an application might need. There is no inversion of control in this case since there is an explicit request to get a hold of an implementation. In dependency injection, that control is inverted to something else.


next: [[2 - Configuration]]