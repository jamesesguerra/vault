1. Copy your database connection string to the `appsettings.json` file in the API. To find your DB connection string, click the DB in the SQL Server Object Explorer and open Properties

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "Default": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=MinimalAPIUserDB;Integrated Security=True;Connect Timeout=30;Encrypt=False;Trust Server Certificate=False;Application Intent=ReadWrite;Multi Subnet Failover=False"
  }
}
```

2. Add a project reference in the API project to the class library project
3. Add the services to the dependency injection system in the `Program.cs` file of the API project

```cs
builder.Services.AddSingleton<ISqlDataAccess, SqlDataAccess>();
builder.Services.AddSingleton<IUserData, UserData>();
```

4. (Optional) Create a `GlobalUsings.cs` file to store `using` statements to files you think you'd reference a lot in different files

```cs
global using DataAccess.Data;
global using DataAccess.Models;
```

5. Create a new class with a public extension method to configure the API endpoints

```cs
public static void ConfigureApi(this WebApplication app)
{
    // add all of the API endpoint mappings
    app.MapGet("/Users", GetUsers);
    app.MapGet("/Users/{id}", GetUser);
    app.MapPost("/Users", InsertUser);
    app.MapPut("/Users", UpdateUser);
    app.MapDelete("/Users/{id}", DeleteUser);
}
```

6. Add the methods to handle the requests

```cs
private static async Task<IResult> GetUsers(IUserData data)
{
    try
    {
        return Results.Ok(await data.GetUsers());
    }
    catch (Exception ex)
    {
        return Results.Problem(ex.Message);
    }
}

private static async Task<IResult> GetUser(int id, IUserData data)
{
    try
    {
        var results = await data.GetUser(id);
        if (results is null) return Results.NotFound();

        return Results.Ok(results);
    }
    catch (Exception ex)
    {
        return Results.Problem(ex.Message);
    }
}

private static async Task<IResult> InsertUser(UserModel user, IUserData data)
{
    try
    {
        await data.InsertUser(user);
        return Results.Ok();
    }
    catch (Exception ex)
    {
        return Results.Problem(ex.Message);
    }
}

private static async Task<IResult> UpdateUser(UserModel user, IUserData data)
{
    try
    {
        await data.UpdateUser(user);
        return Results.Ok();
    }
    catch (Exception ex)
    {
        return Results.Problem(ex.Message);
    }
}

private static async Task<IResult> DeleteUser(int id, IUserData data)
{
    try
    {
        await data.DeleteUser(id);
        return Results.Ok();
    }
    catch (Exception ex)
    {
        return Results.Problem(ex.Message);
    }
}
```