You can query the metadata of objects such as tables and schemas as well.

### Catalog views
Catalog views provide a way for you to query metadata about objects in a database.

For example, you can list the tables in a database along with their schema names with the `sys.tables` view
```sql
SELECT SCHEMA_NAME(schema_id) AS table_schema_name,
	   name AS table_name
FROM sys.tables;
```

you also get metadata about the columns in a table with the `sys.columns` view
```sql
SELECT
	name AS column_name,
	TYPE_NAME(system_type_id) AS column_type,
	max_length,
	collation_name,
	is_nullable
FROM sys.columns
WHERE object_id = OBJECT_ID(N'HR.Employees');
```

### Stored Procedures
There are also stored procedures that you can call to give you back metadata. For example, the `sys.sp_tables` stored procedure returns a list of objects that can be queried in the current database.