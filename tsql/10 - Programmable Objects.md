### Variables
**Variables** are used to store values to be used later on. They can be used in the same batch they were declared. A **batch** is one or more T-SQL statements sent to SQL Server to be executed as one unit.

To declare a variable, you use the `DECLARE` statement to declare its name and type. You then use `SET` to set a value.
```sql
DECLARE @i AS INT;
SET @i = 10;
```

You can also do both in the same line like this
```sql
DECLARE @i AS INT = 10;
```

and also assign it the result of a scalar expression like a subquery
```sql
DECLARE @empname AS VARCHAR(61);

SET @empname = (SELECT firstname + N' ' + lastname
			    FROM HR.Employees);
```

#### Setting multiple values
Because the `SET` statement can only set one value at a time, there will be some overhead when accessing different fields of the same row. For this reason, you can use a special non-standard T-SQL syntax to assign multiple values at a time. Setting variables this way has predictable behavior when there is only row returned. For rows that return multiple results, the variables that will be set will be equal to the last row.

```sql
DECLARE @firstname AS NVARCHAR(20), @lastname AS NVARCHAR(20);

SELECT
	@firstname = firstname,
	@lastname = lastname
FROM HR.Employees
WHERE empid = 3;
```



