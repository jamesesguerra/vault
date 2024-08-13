****
```sql
USE TSQLV6;

SELECT empid, YEAR(orderdate) AS orderyear,
COUNT(*) AS numorders,
FROM Sales.Orders
WHERE custid = 71
GROUP BY empid, YEAR(orderdate)
HAVING COUNT(*) > 1
ORDER BY empid, orderyear;
```

The clauses are logically processed in the following order:
1. FROM
2. JOINS
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
	- EXPRESSIONS IN SELECT e.g. window functions,, subqueries
7. DISTINCT
8. ORDER BY
9. TOP **OR** OFFSET-FETCH

## FROM
In the `FROM` clause, you specify the names of the tables you want to query and table operators that operate on those tables. It's better to qualify the table with the schema name to avoid overhead / the RDBMS accidentally selecting a table from a different schema.
```sql
FROM Sales.Orders
```

## WHERE
In the `WHERE` clause, you specify a predicate to filter the rows returned by the `FROM` phase. Only the rows for which the predicate evaluates to `TRUE`, and not `FALSE` or `UNKNOWN`, will be returned.
```sql
FROM Sales.Orders
WHERE custid = 71
```

## GROUP BY
The `GROUP BY ` clause arranges the rows returned by the previous query phase in groups. The groups are determined by the elements you specify in the clause. It collapses rows that have the same value for the attributes in the clause into 1 row.

If the query involves grouping, all phases after the `GROUP BY` phase will have to operate on groups as opposed to operating on individual rows. This implies that all expressions in the clauses after the `GROUP BY` phase must guarantee to return a single value per group. 
Therefore, elements that participate in the group by are automatically allowed to be used in the subsequent phases since they were grouped into 1 already. 

*"Group all of the rows that have the same values in all the attributes that participate in the `GROUP BY` clause into 1 row that will represent all of them."*
```sql
FROM Sales.Orders
WHERE cusid = 71
GROUP BY empid, YEAR(orderdate)
```

Elements (attributes) that don't participate in the `GROUP BY` are only allowed to participate in the subsequent phases as inputs to an aggregate function like `COUNT`, `SUM`, `AVG`, `MIN`, or `MAX`

### Aggregate functions
All aggregate functions ignore `NULL`s with one exception -- `COUNT(*)`. `COUNT(*)` counts the number of rows in a group whereas `COUNT(<attribute-name>)` returns only known values.

```sql
-- for a qty column with values 30, 10, NULL, 10, 10 --

COUNT(*)               -- returns 5
COUNT(qty)             -- returns 4
COUNT(DISTINCT qty)    -- returns 2
```

## HAVING
Whereas the `WHERE` is a row filter `HAVING` is a group filter. This phase is processed after the rows have been grouped, so you can use aggregate functions in the predicate. 

```sql
SELECT empid, YEAR(orderdate) as orderyear
FROM Sales.Orders
WHERE custid = 71
GROUP BY empid, YEAR(orderdate)
HAVING COUNT(*) > 1;
```

## SELECT
The `SELECT` clause is where you specify the attributes (columns) you wanna return in the result table of the query. If the name of the attribute doesn't apply manipulation, its name stays the same, but you have the option to alias them with `AS`. For expressions that do apply manipulation such as functions, there won't be a column name unless you alias them. 

## DISTINCT
SQL doesn't follow the relational model in that result tables *can* have duplicates. Even with a primary key defined, a query can return a table with duplicates (for the `Orders` table, even when it has a primary key `orderid`, the resulting table can still include duplicates if the `orderid` isn't used in the `SELECT` phase ).

```sql
SELECT DISTINCT empid, YEAR(orderdate) AS orderyear
FROM Sales.Orders
```

## ORDER BY
The `ORDER BY` clause sorts the rows for presentation purposes. It's the last phase to be executed in a query. One of the most important points to understand is that in SQL, table, be it a table in a database or a table returned by a query, is a set and therefore has no guaranteed order. The only way for you to guarantee the order is with an `ORDER BY`. The result then wouldn't be considered a table anymore, but it would be called a **cursor**.

When a `DISTINCT` clause in the `SELECT` statement is specified, you're restricted in the values of `ORDER BY` only to the columns you use in the `SELECT` clause. 

## TOP
`TOP` is a proprietary T-SQL feature that limits the number of rows your query returns. Its specification relies on two elements:
1. the number or percent of rows to return
2. the ordering

When the `TOP` filter is specified, the `ORDER BY` clause serves a dual purpose. One is to define presentation ordering for the rows in the query result. Another is to define for the `TOP` option which rows to filter.

**Problem:** what if you need to filter rows with the `TOP` option based on some ordering, but still return a relational result ie rows that aren't sorted in any specific order. You need **table expressions**

```sql
-- selecting the top 50 most recent orders
SELECT TOP 50 *
FROM Sales.Orders
ORDER BY orderdate DESC;

-- selecting the top 1 percent most recent orders
SELECT TOP (1) PERCENT *
FROM Sales.Orders
ORDER BY orderdate DESC;
```

Without the `ORDER BY` clause, `TOP` won't have an ordering specification and will return whichever `n` rows it happens to physically access first. 

Using `TOP` with `ORDER BY` also means that the query can be nondeterministic if the column you "order by" isn't unique. If multiple rows have the same value, there's no guarantee which column will be included in a the rows returned by the `TOP` filter if for example, it's only looking for one more row. To fix this, you have to make the `ORDER BY` list unique by specifying a tie-breaker.

For example, you can add another column which is unique so that in case of ties, the row with the greater (depends) ID value will be preferred.

```sql
SELECT TOP (5) orderid, orderdate, custid, empid
FROM Sales.Orders
ORDER BY orderdate DESC, orderid DESC;
```

You can also choose to return ties instead of specifying a tie breaker by using the `WITH TIES` option

```sql
SELECT TOP (5) WITH TIES orderid, orderdate, custid, empid
FROM Sales.Orders
ORDER BY orderdate DESC;
```

## OFFSET-FETCH
The `OFFSET-FETCH` clauses are a somewhat alternative to the `TOP` filter in that you can return / filter out a specific number of rows. The advantage of `OFFSET-FETCH` is that it's an SQL standard, and also adds a skipping mechanism with the `OFFSET` part. It's also important to note that using `ORDER BY` is required to use `OFFSET-FETCH` to ensure a deterministic value, and not just fetch whatever rows it happens to physically access first.

`OFFSET-FETCH` also supports using `ROW` & `ROWS` and `FIRST` & `NEXT` interchangeably to allow the user to have the query make sense grammatically.

However, the disadvantage is that T-SQL doesn't support percent and the `WITH TIES` option as of this writing.

```sql
-- simulating pagination where there are 10 results shown per page and the user is on the 2nd page
SELECT *
FROM Sales.Orders
OFFSET 10 ROWS FETCH NEXT 10 ROWS ONLY;

-- simulating the TOP filter
SELECT *
FROM Sales.Orders
OFFSET 0 ROW FETCH FIRST 1 ROW ONLY;
```

## Window functions
Window functions are functions that, for each row in the table rows, operates on a subset or window of the entirety of the table rows. This subset is specified by the `OVER()` function and partitioned (sliced, windowed) by the `PARTITION BY` option

```sql
SELECT orderid, custid, val,
	ROW_NUMBER() OVER(PARTITION BY custid
					  ORDER BY val) AS rownum
FROM Sales.OrderValues
ORDER BY custid, val;
```

## Predicates, logical and arithmetic operators
You can embed predicates within certain T-SQL clauses such as `WHERE`, `HAVING`, and `CHECK`. These predicates can be combined with logical operators such as `AND`, `OR`, `NOT`, and comparison operators like `==`, `<>`, `<`, `>`.

Predicates also include the `IN` operator, which checks if a single value is one of many options specified in the `IN` operator

```sql
SELECT empid, firstname, lastname
FROM HR.Employees
WHERE lastname IN('Davis', 'Doyle');
```

`BETWEEN` checks if a value is between 2 other values, inclusive of those 2 values.

```sql
SELECT orderid, empid, orderdate
FROM Sales.Orders
WHERE orderid BETWEEN 10300 AND 10310;
```

Meanwhile, `LIKE` is a pattern-matcher that checks whether a string follows a certain pattern

```sql
SELECT empid, firstname, lastname
FROM HR.Employees
WHERE lastname LIKE N'D%';
```

Arithmetic operators such as `+`, `-`, `*`, `/`, and `%` are also supported. You can use these in predicates as well as in the `SELECT` statement to compute the combination of other columns to produce a new column.

```sql
SELECT orderid, productid, qty, unitprice, discount,
	qty * unitprice * (1 - discount) AS val
FROM Sales.OrderDetails;
```

## CASE
Case expressions are just like switch expressions in C# in that they test a scalar value against different predicates and return a value if there's a match, or if there isn't, an `ELSE` clause can be included to be a default value. `NULL` will be returned for the rows with no match if no `ELSE` clause is specified.

This type of `CASE` expression is called simple since you're simply testing a single value for equality against different values.
```sql
SELECT productid, productname, categoryid,
	CASE categoryid
		WHEN 1 THEN 'Beverages'
		WHEN 2 THEN 'Condiments'
		WHEN 3 THEN 'Confections'
		WHEN 4 THEN 'Dairy Products'
		WHEN 5 THEN 'Grains/Cereals'
		WHEN 6 THEN 'Meat/Poultry'
		WHEN 7 THEN 'Produce'
		WHEN 8 THEN 'Seafood'
		ELSE 'Unknown Category'
		END AS categoryname
FROM Production.Products
```

You can also perform a "searched" `CASE` expression wherein you don't specify a single value up for equality comparisons. Instead, you specify several predicates which can include different columns as well.

```sql
SELECT orderid, custid, val,
	CASE
		WHEN val < 1000.00
			THEN 'Less than 1000'
		WHEN val BETWEEN 1000.00 AND 3000.00
			THEN 'Between 1000 and 3000'
		WHEN val > 3000.00
			THEN 'Greater than 3000'
		ELSE 'Unknown'
		END AS valuecategory
FROM Sales.OrderValues;
```

### CASE alternatives
There are functions available that act as a shorthand of the `CASE` expression.

**ISNULL & COALESCE** (standard SQL)
`ISNULL` is like a null-coalescing function that takes in 2 arguments. It tests the first argument for `NULL` and if it isn't, it returns the value of the column in the first argument. If it *is* null, it returns the second argument as a somewhat default value.

`COALESCE` is just like `ISNULL` but it accepts 2 or more arguments. It returns the firs argument that isn't `NULL`, or `NULL` if every argument is `NULL`.

```sql
SELECT empid, firstname, lastname, ISNULL(region, 'No region') as region_name
FROM HR.Employees;

-- like var region_name = region ?? "No region"; in C#
```

**IIF & CHOOSE** (non-standard SQL)
`IIF(<predicate>, <expr-1>, <expr-2>)` tests for the predicate and returns the first argument if the predicate returns `TRUE` and returns the second argument otherwise. 

---
## NULLs
Because SQL supports `NULL` markers to denote missing / empty values, it also supports 3-valued predicate logic involving `TRUE`, `FALSE`, and `UNKNOWN`. When a logical expression's (predicate) value is known, it can only result in `TRUE` or `FALSE`. But if the value of the predicate is `NULL`, it's value will be `UNKNOWN`.

```sql
salary < 0; -- salary = 1000 ? true : false
salary < 0; -- salary = NULL ? UNKNOWN
```

The rule SQL has for query filters such as `WHERE` and `HAVING` is "accept true", meaning, it will only accept rows for which a predicate evaluates to `TRUE`. Rows that evaluate to `FALSE` or `UNKNOWN` will be discarded. Meanwhile, the rule it has for `CHECK` constraints is "reject false", meaning it'll accept not only `TRUE` values, but also `UNKNOWN`.

To test if a value is / isn't `NULL`, SQL provides the `IS NULL` and `IS NOT NULL` predicates.

```sql
SELECT empid, firstname, lastname, region,
	IIF(region IS NOT NULL, 'region is null', 'region is not null') as region_null
FROM HR.Employees;
```

**NOTE:** There is a difference between `column = NULL` and `COLUMN IS NOLL` or `column <> NULL` and `COLUMN IS NOT NULL` because comparing 2 `NULL`s will result in `UNKNOWN`. So if you want to only retrieve rows for which a column is `NULL`, use `IS NULL` to return a `TRUE` or `FALSE` since the rule of query filters is "accept true" and it won't accept rows which result in `UNKNOWN`.

