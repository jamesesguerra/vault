
You would wanna use subqueries to avoid having to store intermediate table results in variables if you need them in another query. Subqueries or inner queries can be used within outer queries to take the place of constants and variables, and are evaluated at runtime.

### Types of Subqueries
#### Self-contained subqueries
Self-contained subqueries are subqueries that aren't dependent or reliant on the outer query. That is, you can highlight them within the outer query to test that they function, and it will work. 

**Scalar queries**
Scalar self-contained subqueries only return a single value, and can be used anywhere a single value is expected, such as in a `WHERE` or `SELECT` clause. If a scalar subquery returns more than one value, the query will fail. 

```sql
SELECT orderid, orderdate, empid, custid
FROM Sales.Orders
WHERE orderid = (SELECT MAX(O.orderid)
				 FROM Sales.Orders AS O);
```

**Multi-valued queries**
Multi-valued queries return more than one value and can be used wherever multiple values are expected, such as in an `IN` predicate. In this case, the query would never fail because the query would be valid if the subquery were to return 0, 1, or multiple values.

```sql
SELECT orderid, orderdate, empid, custid
FROM Sales.Orders
WHERE orderid IN (SELECT MAX(O.orderid)
				  FROM Sales.Orders AS O);
```

You might've noticed you can also used joins to do the same thing as subqueries. There's no rule of thumb that says either is better. The trick is to do what is more intuitive at first, and if need be, optimize by rewriting a join to use subqueries, or vice versa.

#### Correlated Subqueries
Correlated subqueries rely on some attribute from the tables that appear in the outer query. Therefore, you can't simply highlight them alone to test that they work properly. 

```sql
-- getting the orders with the maximum order ID for each customer
SELECT custid, orderid, orderdate, empid
FROM Sales.Orders AS O1
WHERE orderid = (SELECT MAX(O2.orderid)
				 FROM Sales.Orders AS O2
				 WHERE O2.custid = O1.custid)
```

### EXISTS
The `EXISTS` predicate accepts a subquery as an input. If the subquery returns 1 or more rows, the `EXISTS` predicate returns true. Otherwise, it returns false.

Using the `*` in the `SELECT` clause is permissible when it comes to the subquery used in the `EXISTS` predicate since you only care about the number of rows that are returned.

```sql
SELECT custid, companyname
FROM Sales.Customers AS C
WHERE country = N'Spain'
	AND EXISTS
		(SELECT * FROM Sales.Orders AS O
		 WHERE O.custid = C.custid);
```

It's safer to use `NOT EXISTS` than `NOT IN` because `NOT IN` doesn't filter out unknowns. And because `NOT EXISTS` is based on 2-valued predicate logic, it returns either true or false, and naturally filters out unknowns / nulls. On the other hand, `NOT IN` doesn't filter out nulls, and the whole predicate can result to unknown since `(FALSE OR FALSE OR TRUE OR FALSE... OR UNKNOWN)` will always result in unknown, and won't return any rows.


