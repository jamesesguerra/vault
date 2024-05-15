### Prerequisites
1. Install MS SQL Server Developer Edition
- You can use Windows authentication on a Windows machine -- if your user is the one who installed it, you can connect without any credentials; if using a Mac or Linux machine, you need a username and password
2. Install Azure Data Studio
- Connect to your local SQL Server instance

### Downloading Packages
1. Install Dapper
```sh
dotnet add package Dapper
```

2. Install Microsoft.Data.SqlClient gives us access to connections to SQL Server instance
```sh
dotnet add package microsoft.data.sqlclient
```

3. Install EntityFramework
```sh
dotnet add package microsoft.entityframeworkcore
```

4. Install EntityFramework.SqlServer to access SQL Server from EFCore as well
```sh
dotnet add package microsoft.entityframeworkcore.sqlserver
```


### Connect from code
1. Make connection string
```cs

// windows
string conSt = "Server=localhost;Database=DotNetCourseDatabase;TrustServerCertificate=true;Trusted_Connection=true";

// mac / linux
string conSt = "Server=localhost;Database=DotNetCourseDatabase;TrustServerCertificate=true;Trusted_Connection=false;User Id=sa;Password=SQLConnect1";
```

2. Create a new connection
```cs
IDbConnection dbConnection = new SqlConnection(connectionString);
```

3. Create query and use Dapper to run query
```cs
var sqlCommand = "SELECT GETDATE()";

dbConnection.Query<DateTime>(sqlCommand);
```