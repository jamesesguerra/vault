see also [[7 - Logging]]

The .NET Core logging framework comes with several abstractions:
- `ILogger` - has a `Log()` method which you use to interact with your code
- `ILoggerProvider` - used to create a custom `ILogger` depending on the provider e.g console, file
- `ILoggerFactory` - the glue between `ILogger` and `ILoggerProvider` ie you register provider instances to the factory and when you call `CreateLogger()` on the factory, it creates `ILogger` that wraps each of the providers so that when you call `Log()`, it logs to all the providers

![[ilogger.png]]

For example, you can add a console logging provider like this. However, the web application builder already adds this by default
```csharp
builder.Logging.AddConsole();
```

#### Adding log messages
Logging, like most of everything else in .NET Core, is available through DI. To add logging to a service, you merely need to inject an instance of `ILogger<T>` where `T` is the type of your service.

```csharp
public class FruitController : ControllerBase
{
	private readonly ILogger<FruitController> _logger;

	public FruitController(ILogger<FruitController> _logger) {
		_logger = logger;
	}

	[HttpGet("fruits")]
	public async IEnumerable<Fruit> GetAllFruits() {
		var fruits = await _fruitService.GetAllFruits();
		_logger.LogInformation("Loaded {FruitCount} fruits", fruits.Count);
	}
}
```

In this example, we're using `LogInformation`, one of the many extension methods on `ILogger`. These different extension methods let you specify the *log level* of a message, that is, how important it is. Every log message has a log level.

These extension methods are also like `printf` in C with placeholders and parameters matched by position. You can always use string interpolation instead, but using placeholders like this provides more information to the logger that can be useful for structured logging.

#### Parts of a log message
The exact presentation of a log message will vary, but each log record includes up to 6 elements:
- **log level** - the level of how important the log is
- **event category** - typically set to the name of the class creating the log
- **message** - the content of the log message
- **parameters** - if the log message contains placeholders, there would be parameters associated with them
- **exception** - if an exception occurs, you can pass the exception to the log message as well
- **event id** - an optional integer identifier for the message used to categorize what kind of message it signifies in your app; you can use this to quickly find a series of log messages

#### Log levels
![[log-levels.png]]

#### Logging Providers
You control where logs are written from your app by adding `ILoggingProvider`s.

By default, logs in development are written to the console. This is useful for debugging, but it wouldn't be much use when your app is in production since it wouldn't be searchable / saved anywhere. You therefore need to add logging providers to control the destination of your log messages.

##### Default providers
Microsoft has written several first-party logging providers
- **console provider** - writes messages to the console
- **debug provider** - writes messages to the debug window in VS and VSCode
- **event log provider** - writes messages to Windows event viewer
- **event source provider** - writes messages using event tracing

There are also many third-party logging providers such as Azure App Service, ElasticSearch, and integrations with logging frameworks like NLog and Serilog.

#### Configuring Logging Providesr
You configure logging providers in `Program.cs`.

1. Install the logging provider
```sh
dotnet add package NetEscapades.Extensions.Logging.RollingFile
```

2. Add the logging provider to `WebApplicationBuilder.Logging`. `AddFile` is an extension method provided by the package to make it easy to setup.
```csharp
builder.Logging.AddFile();
```

3. By default, console and debug logging providers will always be added automatically. You have to explicitly clear the logging providers if you wanna remove them.

---

#### Structured Logging
If you find yourself looking through a log file and doing a file search, your limiting factor is that the text is *unstructured*, and so your only option is text processing. A better approach is to store the logs in a *structured format* such as JSON so that you can easily query it and create analytics.

Adding structured logging is simply done by adding a logging provider via a package. ElasticSearch is one option but has a steep learning curve. An easier package to get started with is Seq.

**Using Seq**
1. Run the Docker container
```sh
docker run --name seq -d --restart unless-stopped -e ACCEPT_EULA=Y -p 5341:80 datalust/seq:latest
```

2. Add the package to the project
```sh
dotnet add package Seq.Extensions.Logging
```

3. Add the logging provider to `Program.cs`
```csharp
builder.Logging.AddSeq();
```