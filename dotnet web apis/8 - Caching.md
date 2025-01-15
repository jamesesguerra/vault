> a **cache** is a hardware or software component that stores data to make future requests that require such data faster -- essentially not having to recreate it from scratch

#### Types of caching
- **client-side and intermediate HTTP response caching** - using HTTP response headers
- **server-side HTTP response caching** - using the `ResponseCachingMiddlware`
- **in-memory caching** - using the `IMemoryCache` interface
- **distributed caching** - using the `IDistributedCache` interface

![[caching-groups.png]]

#### What should you cache?
 - a static website that doesn't do much more than static resources should do client-side or intermediate HTTP response caching
 - a dynamic website, such as a WordPress blog should focus on client-side caching as well since the content doesn't change much
 - a RESTful web API should use HTTP response caching to optimize frequently accessed GET endpoints and/or those that come with default parameters, a good in-memory or distributed strategy could also relieve the burden

	As a general rule, we should always aim to cache at the highest level we can get away with: HTTP response caching for static assets and frequently called endpoints that return the same response, in-memory or distributed caching to reduces DBMS calls.

### HTTP response caching
HTTP response caching is done using the `[ResponseCache]` attribute. You can specify whether the cache should be public or private, or how long it should be stored, or if it should even be cached at all.

```csharp
// cache GET endpoints
[HttpGet]  
[ResponseCache(Location = ResponseCacheLocation.Any, Duration = 60)]  
public async Task<RestDTO<BoardGame[]>> Get([FromQuery] RequestDTO<BoardGameDTO> input)

// but not error handler endpoints
app.MapGet("/error", [ResponseCache(NoStore = true)] (HttpContext context) =>
```

**Implementing a no-cache default behavior**
It's advised to explicitly say which resources is cacheable / non-cacheable. You can implement a default when the `[ResponseCache]` attribute isn't explicitly set by adding custom middleware. This is essentially providing a fallback against human errors.

```csharp
app.Use((context, next) =>  
{  
    context.Response.GetTypedHeaders().CacheControl =   
		new CacheControlHeaderValue() { NoCache = true, NoStore = true };  
  
    return next.Invoke();  
});
```

**Defining and using caching profiles**
Even when you have a default cache configuration, you still need to manually add the `[ResponseCache]` attributes to the endpoints that will override the defaults. To keep things DRY, you can add cache profiles to set up predefined caching directives then apply them by using name-based references.

You can create profiles for if you don't want intermediate proxies to cache, and if you want all to cache for 60 seconds.
```csharp
builder.Services.AddControllers(options =>  
{  
    options.CacheProfiles.Add("NoCache",  
        new CacheProfile() { NoStore = true });  
        options.CacheProfiles.Add("Any-60",  
        new CacheProfile()  
        {  
            Location = ResponseCacheLocation.Any,  
            Duration = 60  
        });  
});
```

You can then use the profiles in the endpoints
```csharp
[ResponseCache(CacheProfileName = "Any-60")]
```

### Server-side response caching
Client-side caching alone still isn't perfect because for one, cache misses can happen. And secondly, it isn't shared. If one client has a cached response for some data, a new client requesting will trigger the action method even and cause the data to be recomputed even if it hasn't changed.

**Response-caching middleware**
When used alone, the `[ResponseCache]` attribute handles HTTP response caching for clients and proxies only. .NET Core has a built-in component that can store HTTP responses in a dedicated internal cache and serve them from that cache instead of activating action methods. This is the **response-caching middleware**.

This middleware allows the app to cache its own responses by the same cache directives used for client-side caching. It will only cache GET or HEAD responses resulting in a 200 OK status.

```csharp
builder.Services.AddResponseCaching();

app.UseCors();
app.UseResponseCaching();
```

To prevent memory-shortage problems, you can fine-tune the middleware's caching strategies
```csharp
builder.Services.AddResponseCaching(options =>
{
	options.MaximumBodySize = 32 * 1024 * 1024; // sets max response body size to 32 MB
	options.SizeLimit = 50 * 1024 * 1024; // sets max middleware size up to 50 MB
})
```

### In-memory caching
In-memory caching allows you to cache arbitrary data in local memory. In other words, caching not just for HTTP responses only, but for potentially heavy operations. The mechanism is based on the `IMemoryCache` interface which you inject. It also doesn't honor the cache headers of the request, making it so that clients can't forcefully invalidate the caching (by doing a hard reload for example).

The instance of `IMemoryCache` is a singleton that can store data based on key-value pairs. 

1. Add the memory cache services. This should be added after all the existing service registrations.
```csharp
builder.Services.AddMemoryCache();
var app = builder.Build();
```

2. Inject the `IMemoryCache` instance
```csharp
public class BoardGamesController {
	private readonly IMemoryCache _memoryCache;

	public BoardGamesController(IMemoryCache memoryCache) {
		_memoryCache = memoryCache;
	}
}
```

3. Implement a caching strategy. You want to create the key based on the parameters the client sent over that will affect the returned data.
```csharp
[HttpGet]  
[ResponseCache(Location = ResponseCacheLocation.Any, Duration = 60)]  
public async Task<RestDTO<BoardGame[]?>> Get([FromQuery] RequestDTO<BoardGameDTO> input) {
	// ...

	BoardGame[]? result = null; // create a variable to hold the result of the entities

	var cacheKey = $"{input.GetType()}--{JsonSerializer.Serialize(input)}";

	if (!memoryCache.TryGetValue(cacheKey, out result)) // get data from DB if not in memory
	{  
	    query = query
		    .OrderBy($"{input.SortColumn} {input.SortOrder}")  
	        .Skip(input.PageIndex * input.PageSize)  
	        .Take(input.PageSize);  
	    result = await query.ToArrayAsync();  
	    memoryCache.Set(cacheKey, result, new TimeSpan(0, 0, 30)); // store in memory for 60 seconds with cache key
	}
	
	return new RestDTO<BoardGame[]?>()  // return result
	{  
	    Data = result,  
	    PageIndex = input.PageIndex,  
	    PageSize = input.PageSize,  
	    RecordCount = recordCount,  
	    Links = new List<LinkDTO>() { }  
	};
}
```

### Distributed Caching
In-memory caching offers a lot of benefits for apps that operate on one server. However, it poses downsides for apps that run on multiple servers since each cached entry will only be available within the server that set it up. This could lead to cache inconsistencies.

Switching from a local cache to a distributed cache offers the following benefits:
- **consistent cached data** - all servers retrieve cache from the same source
- **independent lifetime** - it doesn't depend on a web server ie it won't die if the web server dies
- **performance benefits** - operates on a third-party service and won't consume a web server's local memory

**Choosing a cache provider**
Cached providers are the services that will store the cached data. It boils down to either choosing a **DBMS** (such as SQL Server) or a **distributed key-value store** such as Redis. The `IDistributedCache` interface is the interface by which we can use to set up, configure, and manage cache providers.

**Distributed Cache Extensions**
Unfortunately, the `IDistributedCache` interface doesn't support generic types like the `IMemoryCache` interface does. If you want to store and get data, you need to convert them to string or byte arrays first, which is extra work. You can first create some extension methods to ease this process.

```csharp
public static class DistributedCacheExtensions  
{  
    public static bool TryGetValue<T>(
	    this IDistributedCache cache,
	    string key,
	    out T? value)  
    { 
	    value = default;  
        var val = cache.Get(key);  
        if (val is null) return false;  
        value = JsonSerializer.Deserialize<T>(val);  
        return true;  
    }  
    
    public static void Set<T>(  
        this IDistributedCache cache,  
        string key,  
        T value,  
        TimeSpan absoluteExpirationRelativeToNow)  
    {
	    var bytes = Encoding.UTF8.GetBytes(JsonSerializer.Serialize(value));  
		cache.Set(key, bytes, new DistributedCacheEntryOptions  
        {  
            AbsoluteExpirationRelativeToNow = absoluteExpirationRelativeToNow  
        });
	}
}
```

#### Setting up a Redis provider
1. Go to [the Redis website](https://redis.io/)

2. Create an account and a free database

3. Go the the main page of the database and go to the configuration section

4. Copy the public endpoint and the password

5. Add it as another configuration setting
```sh
dotnet user-secrets set "Redis:ConnectionString" "redis-13468.c53.west-us.azure.redns.redis-cloud.com:13468,password=BS6EXn8mBm9a6oujcvqBJZnl1PjMs5w5"
```

6. Install the package for the Redis cache provider
```sh
dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis
```

7. Configure the Redis cache server
```csharp
builder.Services.AddStackExchangeRedisCache(options =>  
{  
    options.Configuration = builder.Configuration["Redis:ConnectionString"];  
});
```

8. Use the cache
```csharp
public async Task<RestDTO<Mechanic[]?>> Get([FromQuery] RequestDTO<Mechanic> input)  
{  
    var query = context.Mechanics.AsQueryable();  
    
    if (!string.IsNullOrEmpty(input.FilterQuery))  
        query = query.Where(b => b.Name.Contains(input.FilterQuery)); 
         
    var recordCount = await query.CountAsync();  
  
    var cacheKey = $"{input.GetType()}-{JsonSerializer.Serialize(input)}";  
  
    if (!distributedCache.TryGetValue(cacheKey, out Mechanic[]? result))  
    {
	    query = query
			    .OrderBy($"{input.SortColumn} {input.SortOrder}")  
	            .Skip(input.PageIndex * input.PageSize)  
	            .Take(input.PageSize);  
        result = await query.ToArrayAsync();  
        distributedCache.Set(cacheKey, result, TimeSpan.FromMinutes(60));  
    }
    
    return new RestDTO<Mechanic[]?>()  
    {  
        Data = result,  
        PageIndex = input.PageIndex,  
        PageSize = input.PageSize,  
        RecordCount = recordCount,  
        Links = []  
    };
}
```

prev: [[7 - Logging]]
next: [[9 - Authentication & Authorization]]