### Pre-publishing Tasks

#### Using HTTP security headers
In the HTTP request-response cycle, response headers can contain useful info that the client will use to handle several tasks, such as content caching and localization. Response headers that can affect the website's security posture are called **security headers**.

Some security headers are:
- **HTTP Strict Transport Security (HSTS)** - tells the web browser to only access the web server only through HTTPS
- **X-Frame-Options** - protects against clickjacking by preventing frames / iframes from specific sources
- **X-XSS-Protection** - protects against cross-site scripting (XSS)
- **X-Content-Type-Options** - prevents the browser from loading / executing a response that differs from the expected or declared content type
- **Content security policy** - prevents XSS and other code-injection attacks by defining which content the browser should and shouldn't load
- **Referrer policy** - determines whether the URL of the web page that linked the requested resource has to be sent along with the request

```csharp
if (app.Environment.IsDevelopment())
{
	// ...
}
else
{
	// HTTP security headers  
	app.UseHsts();  
	  
	app.Use(async (context, next) =>  
	{  
	    context.Response.Headers.Append("X-Frame-Options", "sameorigin");  
	    context.Response.Headers.Append("X-XSS-Protection", "1; mode=block");  
	    context.Response.Headers.Append("X-Content-Type-Options", "nosniff");  
	    context.Response.Headers.Append("Content-Security-Policy", "default-src ' self' ;");  
	    context.Response.Headers.Append("Referrer-Policy", "strict-origin");  
	    await next();  
	});
}
```

#### .NET Publishing Modes
1. **Framework-dependent** - requires the presence of .NET Framework, which must be installed on the target system; essentially building a .NET app that can run only on a hosting server that has been configured to support it
2. **Self-contained** - doesn't rely on the presence of .NET components on the target system; all components including the .NET libraries and runtime will be included in the production build