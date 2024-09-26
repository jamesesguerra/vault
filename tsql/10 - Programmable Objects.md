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

Using the `SET` statement is safer than this however, since it requires you to use a scalar subquery that returns just one value.
```sql
DECLARE @empname AS NVARCHAR(61);

SET @empname = (SELECT firstname + N' ' + lastname
			    FROM HR.Employees
			    WHERE mgrid = 2);

SELECT @empname AS empname;
```


### Batches
Batches are 1 or more SQL statements that are sent to a server for execution as a single unit. They undergo syntax checking, binding resolution, and optimization as a unit.

The difference between transactions and batches is that transactions are used to ensure ACID properties for several SQL statements, whereas batches are used for grouping multiple SQL statements together.

**Unit of parsing**
Batches are a unit of parsing in that SQL statements in a batch are parsed and executed as a unit. If an error occurs within the batch, the whole batch isn't sent to SQL Server to be executed.

**Variables**
Variables are also local to the batches that they're defined in. Using variables outside will cause an error
```sql
DECLARE @firstname AS NVARCHAR(60) = N'James';
PRINT @firstname; -- succeeds
GO

PRINT @firstname; -- fails
```

**Unit of resolution**
Batches are also units of checking the existence of objects / columns. This is why when you add a column to a table and try to reference it in the same batch, SQL Server will complain because by the end of parsing the batch for bindings, that new column still doesn't exist.
```sql
ALTER TABLE dbo.T1 ADD col2 INT;
SELECT col1, col2 FROM dbo.T1; -- col2 isn't registered yet
```

For this reason, it's best practice to separate DDL and DML statements into different batches
```sql
ALTER TABLE dbo.T1 ADD col2 INT;
GO
SELECT col1, col2 FROM dbo.T1;
```

#### GO  n
The `GO` command is actually just a client command used by SQL Server's client tools. It also supports an argument which indicates how many times to run the batch.
```sql
INSERT INTO dbo.T1 DEFAULT VALUES;
GO 100

SELECT * FROM dbo.T1; -- 1 through 100
```

### Flow elements
#### IF...ELSE
The `IF` statement takes in a predicate as an argument and executes the next line if it results to true. If the result is false / unknown, it executes the line after the `ELSE` statement.

```sql
IF YEAR(SYSDATETIME()) <> YEAR(DATEADD(day, 1, SYSDATETIME()))
	PRINT 'Today is the last day of the year.';
ELSE
	PRINT 'Today is not the last day of the year';
```

To run more than one statement as a result of an if-check, you need to explicitly state the boundaries of the block using the `BEGIN` and `END` keywords. These act sort of like curly braces to demarcate the statements included in the block.

```sql
IF YEAR(SYSDATETIME()) <> YEAR(DATEADD(day, 1, SYSDATETIME()))
	BEGIN
		PRINT 'Today is the last day of the year.';
		PRINT 'Happy new year tomorrow!';
	END;
ELSE
	PRINT 'Today is not the last day of the year';
```

#### WHILE
SQL also has a `WHILE` statement as a looping mechanism, as well as the usual `BREAK` and `CONTINUE` statements.

```sql
DECLARE @i AS INT = 0;

WHILE @i <= 10
	BEGIN
		SET @i += 1
		IF @i = 2 CONTINUE;
		IF @i = 6 BREAK;
		PRINT @i
	END;
```

### Temporary tables
Temporary tables are useful in certain cases when you wouldn't wanna work with permanent tables and you only need the data to be visible in the current batch. Some use cases include storing temporary data and not being able to create permanent tables.

#### Local temporary tables
One obvious scenario for local temporary tables is if you need to store intermediate results temporarily, such as in a loop, and later query that data. To create a local temporary table, name it starting with a `#`. Local temporary tables are only visible in the current scope and other scopes above it. In other words, in the current stack frame and all other stack frames above it.

```sql
CREATE TABLE #OrderTotalsByYear
(
	orderyear INT NOT NULL PRIMARY KEY,
	qty       INT NOT NULL
);

INSERT INTO #OrderTotalsByyear(orderyear, qty)
	SELECT
		YEAR(O.orderdate) AS orderyear,
		SUM(OD.qty) AS qty
	FROM Sales.Orders AS O
		INNER JOIN Sales.OrderDetails AS OD
		ON OD.orderid = O.orderid
	GROUP BY YEAR(orderdate);
```

#### Global temporary tables
Global temporary tables are just like local ones except that they're visible to all sessions and are useful if you want to share data with everyone. They're created using a prefix with `##`.

#### Table variables
Table variables are just like local temporary tables in that they're only visible to the creating session. But because they're variables, their scope is more limited -- to the current batch. It makes sense to use table variables when dealing with a small amount of data (a few rows) since they're more optimal. Otherwise, use local temporary tables.

```sql
DECLARE @MyOrderTotalsByYear AS TABLE
(
	orderyear INT NOT NULL PRIMARY KEY,
	qty       INT NOT NULL
)

-- insert into table in same batch
```

**Table types**
To preserve the definition of the table variable for use later, you can use table types. These are also required for table-valued parameters (TVPs)

```sql
CREATE TYPE dbo.OrderTotalsByYear AS TABLE
(
	orderyear INT NOT NULL PRIMARY KEY,
	qty       INT NOT NULL
)
```

You can then define the table variable like this
```sql
DECLARE @MyOrderTotalsByYear AS dbo.OrderTotalsByYear;
```

### Dynamic SQL
Dynamic SQL is constructing SQL queries as character strings and executing that batch using the `EXEC` command or using the `sp_executesql` stored procedure.

This is useful for several reasons:
- automating admin tasks
- improving performance of certain tasks

#### EXEC command
The `EXEC` command can be used to execute a query stored as a character string, and also to execute stored procedures.

Executing a character string entails entering it in parentheses.

```sql
DECLARE @sql AS VARCHAR(100);
SET @sql = 'PRINT ''This message was printed by a dynamic SQL batch''';

EXEC(@sql);
```

However, a better approach to executing character strings as queries is using the `sp_executesql` SP. This approach is safer as it uses parameters and arguments instead of inserting strings directly to the query. Also, it's more performant as the execution plan (physical processing required to describe which objects to access and how to access them etc) can be cached and reused.

In this approach, the `@stmt` parameter specifies the character string, the `@params` specifies the declaration of input and output parameters, followed by the assignments of I/O separated by commas.

```sql
DECLARE @sql AS NVARCHAR(100);

SET @sql = N'SELECT orderid, custid, empid, orderdate FROM Sales.Orders WHERE orderid = @orderid;';

EXEC sp_executesql
	@stmt = @sql,
	@params = N'@orderid AS INT',
	@orderid = 10248;
```

### Routines (functions)
Routines or functions encapsulate a series of computations in a black box. SQL Server supports 3 types of routines:
- user-defined functions
- stored procedures
- triggers

You can use either T-SQL or .NET code to create routines. When dealing with data manipulation, use T-SQL. When dealing with string manipulation, iterative logic, or computationally-intensive operations, use .NET code.

**User-defined functions**
SQL Server supports scalar UDFs that return a single value, and table-valued UDFs that return a table. You can incorporate UDFs into queries, for example, you can use scalar UDFs where a scalar value is expected, and table-valued UDFs after a `FROM` statement.

You're not allowed to invoke statements that have side effects inside UDFs, such as any schema / data changes.

```sql
CREATE FUNCTION dbo.GetAge
(
	@birthdate AS DATE,
	@eventdate AS DATE
)
RETURNS INT
AS
BEGIN
	RETURN
		DATEDIFF(year, @birthdate, @eventdate)
			- CASE WHEN 100 * MONTH(@eventdate) + DAY(@eventdate)
					  < 100 * MONTH(@birthdate) + DAY(@birthdate)
				  THEN 1 ELSE 0
			  END;
END;
GO
```

Using the UDF:
```sql
SELECT
	empid, firstname, lastname, birthdate,
	dbo.GetAge(birthdate, SYSDATETIME()) AS age
FROM HR.Employees;
```

