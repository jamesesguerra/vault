1. Create `dbo` folder to store tables and stored procedures
2. Create `dbo/Tables` folder and add a table for `User`

```sql
CREATE TABLE [dbo].[User]
(
	[Id] INT NOT NULL PRIMARY KEY IDENTITY, 
    [FirstName] NVARCHAR(50) NOT NULL, 
    [LastName] NVARCHAR(50) NOT NULL
)
```

3. Create `dbo/StoredProcedures` folder and add stored procedures for CRUD operations

```sql
CREATE PROCEDURE [dbo].[spUser_GetAll]
AS
BEGIN
	SELECT Id, FirstName, LastName
	FROM dbo.[User];
END
```

4. Add a publish file to publish the database configuration to a database server: Right click SQL Server DB project -> Publish