**Configuration** is the set of external parameters provided to an app that controls its behavior in some way. These are essentially settings, with one special kind of setting, a secret, which is a setting that contains sensitive data.  You want to use configuration so your app can run differently in different *hosting environments*. This is important as you want your app to behave differently without having to recompile it with different settings.

#### .NET Framework vs .NET Core
.NET framework used a `web.config` file with an `<appsettings>` element to store the configuration. It then used a `ConfigurationManager` to load the value for a given key in the file. .NET Core gives you the freedom to specify whether to load the configuration in either JSON, XML, YAML, environment variables, DB, etc. It then uses *configuration providers* to load the key-value pairs. It also gives you the option to bind the configuration objects to POCOs

#### ConfigurationManager
`ConfigurationManager` implements the 2 main configuration-related interfaces:
- `IConfigurationBuilder` - describes how to construct the final configuration for the app
- `IConfigurationRoot` - holds the configuration values themselves

Configuration providers are loaded to the `ConfigurationManager` using extension methods. The manager queries the values which are added to the `IConfigurationRoot`

![[dotnet-config-process.png]]

### Adding settings
The default settings file for a .NET Core app is `appsettings.json`. 

When adding settings, it's a good idea to group related settings for namespacing purposes and also so it would be easy to bind them to a POCO.

```json
{
	"MapSettings": {
		"DefaultZoomLevel": 9,
		"DefaultLocation": {
			"latitude": 50.500,
			"longitude": -4.000
		}
	}
}
```

#### Adding a configuration provider
To add a configuration provider you can consult with either the `Configuration` property of `WebApplicationBuilder` or `WebApplication`

There are some providers that are already added by default:
- JSON file provider
- User secrets
- Environment variables
- Command-line arguments

...but you can clear all the default providers and start from scratch. But you typically don't wanna do this since the defaults are already good
```csharp
builder.Configuration.Sources.Clear();
```

... and then proceed to add your own providers
```csharp
builder.Configuration.AddCommandLine(args);
builder.Configuration.AddJsonFile("appsettings.json", optional: true)
```

#### Accessing the ConfigurationManager
You can access the `ConfigurationManager` either in `Program.cs`
```csharp
builder.Configuration
```

or request it as a service, since it's registered in the container as the implementation for `IConfiguration`
```csharp
app.MapGet("/", (IConfiguration config) => config.AsEnumerable());
```

#### Accessing configuration values
Because `IConfiguration` stores the settings as key-value pairs, you can access the values from it using dictionary-syntax. You use a `:` to designate a separate section

```csharp
var zoomLevel = build.Configuration["MapSettings:DefaultZoomLevel"];
```

To grab a whole section, you can use the `GetSection` method. Doing this also resets the namespace.
```csharp
var mapSettingsSection = config.GetSection("MapSettings");
var longitude = mapSettingsSection["DefaultLocation:Longitude"];
```

#### Adding multiple providers
It's important to think about the order in which you add your providers since providers added later can override the values added by providers added earlier, assuming they both add the same keys. This is why it's useful to think of the ConfigurationManager as a simple dictionary.

#### Setting secrets
Storing secrets in `appsettings.json` is a bad idea since you never want to check in sensitive information to source control as it could easily be made available to the public. It's better to store these values outside your project folder.

The common approach is to use environment variables for production. For more security, you can use something like Azure Key Vault which also encrypts the secrets, but you still have to store the connection details.

To demarcate sections in environment variables, you can use either a `:` or `__` (double underscore)

**User Secrets**
However, you wouldn't want to set environment variables for a connection string in development as all your development apps would use the same variable. For this reason, you can use the User Secrets manager to store secrets outside the project folder on a per-app basis.

First, you want to generate a unique ID for your app. This ID gets added to the `.csproj` file
```sh
dotnet user-secrets init
```

To add a secret:
```sh
dotnet user-secrets set "MapSettings:GoogleMapsApiKey" F5E453NF
```

Next, you want to add the User Secrets provider, only in development
```csharp
if (builder.Environment.IsDevelopment())
{
	builder.Configuration.AddUserSecrets<Program>();
}
```

#### Reloading config values
If configuration values change, you want your app to keep running and use the new configuration values so it doesn't have to start up again. You can do this by adding a `reloadOnChange` configuration to the provider. This typically only works for file-based providers and isn't free as it sets up a file watcher in the background.

```csharp
builder.Configuration
	   .AddJsonFile(
		   "appsettings.json",
		   optional: true,
		   reloadOnChange: true
	   );
```

---
### Strongly-typed settings and the options pattern
The preferred way to store and retrieve configuration is by using the options pattern which makes it so that you can access your configuration as POCOs. The previous approach is prone to errors since you're using strings to access sections and parts of the configuration.

**IOptions**
To bind configuration values to custom POCO classes, you can use the `IOptions<T>` interface with a single property, `Value` that contains the configured POCO class at runtime.

You first create the custom POCO class that mimics a section of your configuration file.

```csharp
class AppDisplaySettings {
	public string Title { get; set; }
	public bool ShowCopyright { get; set; }
}
```

You then configure the instance to which the options instance will bind against.
```csharp
builder.Services.Configure<AppDisplaySettings>(builder.Configuration.GetSection("AppDisplaySettings"));
```

And finally inject it in your endpoint
```csharp
app.MapGet("/options", (IOptions<AppDisplaySettings> options) => {
	var settings = options.Value;
	return new { Title = settings.Title, ShowCopyright = settings.ShowCopyright };
})
```

**IOptionsSnapshot**
The thing that `IOptions` can't do is reload itself when the underlying configuration files changes. For this reason, you can use `IOptionsSnapshot` which isn't a singleton. It's a scoped service, so a new instance is created whenever the configuration changes. If you do want a singleton, you can use `IOptionsMonitor`.

---
### Configuring an app for multiple environments
You'll likely want to have different configuration files for your environments to, for example:
- use different database connection strings
- use different logging levels

By default, the `WebApplicationBuilder` looks for an environment variable called `ASPNETCORE_ENVIRONMENT` to identify the current environment. It then exposes what it knows about the current environment in a property `WebApplicationBuilder.Environment`. This property exposes several useful properties about running your app:
- `ContentRootPath` - tells the application where it can find configuration files like app settings
- `WebRootPath` - specifies where the app can find static files

You can tell the current environment by accessing the `app.Environment.EnvironmentName` property, or by using one of these extension methods:
- `IsDevelopment()`
- `IsStaging()`
- `IsProduction()`
- `IsEnvironmentName(string environmentName)`

You can then add environment-specific JSON files to the configuration manager.
```csharp
IHostEnvironment env = builder.Environment;

builder.Configuration.Sources.Clear();
builder.Configuration
	   .AddJsonFile(
		   "appsettings.json",
		   optional: false)
	   .AddJsonFile(
		   $"appsettings.{env.EnvironmentName}.json",
		   optional: true);
```

With this pattern, you have a global and mandatory `appsettings.json` file with settings that span all environments, and then environment specific `appsettings.json` files.

##### Launch settings
By default, the ASP.NET project will create a `launchSettings.json` file which has profiles that you can configure to launch your project. By default, it uses the first profile, but you can explicitly launch one of them with:

```sh
dotnet run --launch-profile <Profile Name>
```

You can also pass in the environment using CLI arguments.
```sh
dotnet run --no-launch-profile --environment Staging
```




prev: [[1 - Dependency Injection]]