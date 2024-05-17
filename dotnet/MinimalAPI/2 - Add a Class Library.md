You want to create a class library to handle access your data / database

1. Add `Data`, `DbAccess`, and `Models`  folders
2. Install required packages
	- `Dapper`, `System.Data.SqlClient`, and `Microsoft.Extensions.Configuration.Abstractions`
1. Create your `User` model by creating a `Models/UserModel.cs` file

```cs
public class UserModel
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

3. Create the `DataAccess/SqlDataAccess.cs` file, make sure to generate an interface for it so you can use with dependency injection so if you want to switch databases along the way it'll be easy. This file is for accessing data / executing commands on the database.

```cs
public class SqlDataAccess : ISqlDataAccess
{
    private readonly IConfiguration _config;

    public SqlDataAccess(IConfiguration config)
    {
        _config = config;
    }

    public async Task<IEnumerable<T>> LoadData<T, U>(
        string storedProcedure,
        U parameters,
        string connectionId = "Default")
    {
        using IDbConnection connection = new SqlConnection(_config.GetConnectionString(connectionId));

        return await connection.QueryAsync<T>(storedProcedure, parameters, commandType: CommandType.StoredProcedure);
    }

    public async Task SaveData<T>(
        string storedProcedure,
        T parameters,
        string connectionId = "Default")
    {
        using IDbConnection connection = new SqlConnection(_config.GetConnectionString(connectionId));

        await connection.ExecuteAsync(storedProcedure, parameters, commandType: CommandType.StoredProcedure);
    }
}
```

4. Create file `Data/UserData.cs`. This file is an abstraction for the class that accesses data / executes commands on the database. Each class is a collection of CRUD operations for a model.

```cs
public class UserData
{
    private readonly ISqlDataAccess _db;

    public UserData(ISqlDataAccess db)
    {
        _db = db;
    }

    public Task<IEnumerable<UserModel>> GetUsers()
    {
        return _db.LoadData<UserModel, dynamic>(storedProcedure: "dbo.spUser_GetAll", new { });
    }

    public async Task<UserModel?> GetUser(int id)
    {
        var results = await _db.LoadData<UserModel, dynamic>(storedProcedure: "dbo.spUser_Get", new { Id = id });
        return results.FirstOrDefault();
    }

    public Task InsertUser(UserModel user)
    {
        return _db.SaveData(storedProcedure: "dbo.spUser_Insert", new { user.FirstName, user.LastName });
    }

    public Task UpdateUser(UserModel user)
    {
        return _db.SaveData(storedProcedure: "dbo.spUser_Update", user);
    }

    public Task DeleteUser(int id)
    {
        return _db.SaveData(storedProcedure: "dbo.spUser_Delete", new { Id = id });
    }
}

```