After the first logical phase of a query with `FROM` to specify the main data table, you can "join" other tables with `JOIN`, an operator for tables. `JOIN` takes (an) input table/s, performs logical processing phases, and returns a table result. 

**Types of table operators**
- `JOIN` - standard
- `APPLY` - nonstandard
- `PIVOT` & `UNPIVOT` - nonstandard

### CROSS JOIN
The `CROSS JOIN` is a simple join that simply takes the Cartesian product of the 2 input tables. That is, if table 1 has `m` rows and table 2 has `n` rows, then the number of rows in the table result will be `m x n`.

```sql
SELECT E.empid, C.custid
FROM Sales.Customers AS C
	CROSS JOIN Hr.Employees AS E
```

In the above example, the tables are given aliases to be used in the `SELECT` statement to prefix column names. This is so that there wouldn't be any confusion if the tables shared a column with the same name. If you alias a table, you cannot use the original table name to prefix the columns anymore. You either prefix it with the alias or don't prefix it at all. However, it's best practice to alias the input tables and use them as prefixes anyway.

### INNER JOIN
The `INNER JOIN` is just like the `CROSS JOIN` except it performs a filter that you specify after it gets the Cartesian product. The predicate you specify is also known as the *join condition*.

```sql
SELECT E.empid, E.firstname, E.lastname, O.orderid
FROM HR.Employees AS E
	INNER JOIN Sales.Orders AS O
	ON E.empid = O.empid;
```

#### INNER JOIN examples
There are kinds of inner joins that are known by names.

1. **Composite join** - are joins wherein the join condition is checking if multiple attributes between 2 tables are equal ie the primary key-foreign key relationship is based on more than one attribute. It's called composite because the join condition is based on more than one attribute.
```sql
FROM dbo.Table1 AS T1
	INNER JOIN dbo.Table2 AS T2
	ON T1.col1 = T2.col1
	AND T1.col2 = T2.col2;
```

2. **Non-equi join** - non-equi joins are joins that do NOT involve equality in the join condition, hence, equi joins are joins that do involve equality ie `ON t1.id = t2.id`

3. **Muli-join** - multi-join queries are queries that have more than 1 join; table operators are processed from **left to right** and so the first `JOIN` operates with the left input being the table specified in the `FROM` clause, and the next `JOIN` takes in the result of the previous `JOIN` as its left input
```sql
SELECT
	C.custid, C.companyname, O.orderid,
	OD.productid, OD.qty
FROM Sales.Customers AS C
	INNER JOIN Sales.Orders AS O
		ON C.custid = O.custid
	INNER JOIN Sales.OrderDetails AS OD
		ON O.orderid = OD.orderid;
```

### OUTER JOIN
An `OUTER JOIN` applies the 2 processing phases that `INNER JOIN`s do, plus one more -- adding outer rows. In the syntax of outer joins, you specify a table to preserve with either:
- `LEFT OUTER JOIN`
- `RIGHT OUTER JOIN`
- `FULL OUTER JOIN`

In the 3rd phase, adding outer rows, the query takes all the rows in the preserved table that didn't find matches in the other table, and adds them to the resulting table, placing `NULL` values for the attributes of the unpreserved table.

Example: a query that joins the customers table to orders table to retrieve the customers and their orders, as well as preserving customers that didn't place any order
```sql
SELECT C.custid, C.companyname, O.orderid
FROM Sales.Customers AS C
	LEFT OUTER JOIN Sales.Orders AS O
		ON C.custid = O.custid;
```

#### ON vs WHERE
The `ON` and `WHERE` clauses seem similar since they both filter out rows based on a predicate. However, their difference is that `ON` filtering is non-final. That is, it filters matching rows, but non-matching rows can be added back from the preserved table. On the other hand `WHERE` filtering is final, and runs after all table operators. 