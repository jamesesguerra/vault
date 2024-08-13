 
Dapper is used to connect to our DB and make queries on that DB.

### 2 kinds of queries
Ones that can return data to us, and ones that can change data in the DB.

1. `Query` - returns multiple rows of data
```cs
IEnumerable<T> stuff = dbConnection.Query<T>(query);
```

2. `QuerySingle` - returns a single row
```cs
var oneStuff = dbConnection.QuerySingle<T>(query);
```

3. `Execute` - executes the given SQL command to change the data; returns the rows affected
```cs
var affectedRows = dbConnection.Execute(insertQuery);
```
