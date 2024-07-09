
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
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY

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

*"Group all of the rows that have the same values in all the attributes in the `GROUP BY` clause into 1 row that will represent all of them."*
```sql
FROM Sales.Orders
WHERE cusid = 71
GROUP BY empid, YEAR(orderdate)
```

