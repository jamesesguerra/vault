Table expressions are named query expressions that return a valid relational table. You can treat them as you would an ordinary table in your queries. The four types are:
- **derived tables**
- **common table expressions (CTEs)**
- **views**
- **inline table-valued functions (inline TVFs)**

### Derived Tables
Derived tables are like subqueries defined at the `FROM` phase of an outer query. Their scope is the outer query and so as soon as the outer query finished, you wouldn't be able to access the derived table anymore.

```sql
SELECT *
FROM (SELECT custid, companyname
	  FROM Sales.Customers
	  WHERE country = N'USA') AS USACusts;
```

The inner query used to define a table expression must meet 3 requirements:
1. **Order is not guaranteed** - because it returns a relational result, the query must not have an `ORDER BY` clause unless an `OFFSET`-`FETCH` is also used
2. **All columns must have names** - assign column aliases to every column
3. **All columns must be unique**

One of the neat things you can do with table expressions is that they allow you to reference column aliases inside the inner query, anywhere in the outer query. This is useful if the expression to get the column value is lengthy, and you wouldn't wanna repeat it.

```sql
SELECT orderyear,
	   COUNT(DISTINCT custid) AS numcusts
FROM (SELECT YEAR(orderdate) AS orderyear, custid
	  FROM Sales.Orders) AS O
GROUP BY orderyear; -- <-- --
```

### Common table expressions (CTEs)
CTEs are similar to derived tables, but have a different syntax. An advantage to them is that if you wanna reference another CTE, you don't nest them, and it looks much easier to read. 

```sql
WITH USACusts
AS
(
	SELECT custid, companyname
	FROM Sales.Customers
	WHERE country = N'USA'
)
SELECT * FROM USACusts;
```

### Views
Whereas derived tables and CTEs aren't reusable, views and inline TVFs are since they're stored as permanent objects in the database.

```sql
DROP VIEW IF EXISTS Sales.UsaCusts;
GO
CREATE VIEW Sales.UsaCusts
AS

SELECT
	custid, companyname, contactname, contacttitle,
	address, city, region, postalcode, country, phone, fax
FROM Sales.Customers
WHERE country = N'USA';
GO
```

You can then query the view just as you would any relational table.
```sql
SELECT custid, companyname, region
FROM Sales.USACusts
ORDER BY region;
```

You can also specify a view configuration when you create or alter one. For example, you can choose to encrypt the view definition in the database using the `WITH ENCRYPTION` config option

```sql
ALTER VIEW Sales.USACusts WITH ENCRYPTION
AS

SELECT
	custid, companyname, contactname, contacttitle, address,
	city, region, postalcode, country, phone, fax
FROM Sales.Customers
WHERE country = N'USA'
GO

-- check that it worked --
SELECT OBJECT_DEFINITION(OBJECT_ID('Sales.USACusts'));
```

### TVFs
TVFs can be thought of as parameterized views. 


