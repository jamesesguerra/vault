## Character data types

**Regular**
Regular character data types include `CHAR` and `VARCHAR`. These data types only occupy 1 byte per character and support only 1 additional language apart from English.

These are denoted by `''` .

**Unicode (N)**
Unicode character data types include `NCHAR` and `NVARCHAR`. These data types occupy 2 bytes per character and support multiple languages.

These are denoted by `N''`.

**Variable (VAR)**
Character data types can also vary in length.
- `VARCHAR` and `NVARCHAR` - are better for reading since the size of each literal is only the amount required to store every character; but they are worse for writing in that rows have to be expanded as strings expand.
- `CHAR` and `NCHAR` - are better for writing in that they have a fixed length and don't need as much expanding; there is already some buffer to handle modifications to a string up to the max length specified; but they're worse for reading since the storage required to store a string is always the same even if it occupies only a fraction of the memory reserved for it.

**NOTE** - You can perform casts between data types with `CAST(<value> AS <data-type>)`

### Operators and functions

1.  `+` - concatenation
```sql
SELECT empid, firstname + N' ' + lastname AS fullname
FROM Sales.Customers;

-- for rows with NULL, you can coalesce them into an empty string so that the whole string won't evaluate to NULL
SELECT custid, country, region, city,
	country + ISNULL(N', ' + region, '') + N', ' + city AS location
FROM Sales.Customers;
```

2. `CONCAT` - automatically substitutes `NULL` values with an empty string
```sql
SELECT custid, country, region, city,
	CONCAT(country, N', ' + region, N', ' + city) AS location
FROM Sales.Customers;
```

3. `SUBSTRING` - extracts a substring from a string; it takes in a string, a starting index (1-based), and the length of characters to take starting from the index. Similar functions are `LEFT` and `RIGHT` where you don't specify and index and only specify the number of characters to retrieve from either side
```sql
SELECT SUBSTRING('abcde', 1, 3); -- abc
SELECT LEFT('abcdef', 3); -- abc
SELECT RIGHT('abcdef', 3); -- def
```

4. `LEN` - returns the number of characters in the input string excluding trailing whitespace
```sql
SELECT LEN('hi how are you'); -- 14
```

5. `CHARINDEX` - returns the index of the first appearance of a substring within a string
```sql
SELECT CHARINDEX('ab', 'abc'); -- 1
```

6. `REPLACE` - replaces all occurrences of a substring within a string with another string
```sql
SELECT REPLACE('http://james-esg.netlify.app', 'netlify', 'vercel'); -- james-esg.vercel.app
```

7. `REPLICATE` - repeats a string a certain number of times
```sql
SELECT REPLICATE('abc', 3); -- abcabcabc
```

8. `STUFF` - deletes a certain amount of characters from a starting index and inserts a new string
```sql
SELECT STUFF('xyz', 2, 1, 'abc'); -- xabcz
```

9. `UPPER` & `LOWER` - returns the string in uppercase and lowercase, respectively
```sql
SELECT UPPER('James Esguerra');
SELECT LOWER('James Esguerra');
```

10. `RTRIM` & `LTRIM` - removes trailing whitespace from either end

11. `STRING_SPLIT` - a function unlike the others since it's a table function (not a scalar function) that returns a table and not a single value; the table it returns will have a column `value` with a single row generated for each result of the split function
```sql
SELECT CAST(value AS INT) AS myvalue
FROM STRING_SPLIT('10248,10249,10250', ',') AS S;
```

12. `LIKE` wildcards
- `%` - any number of characters (a string of any size, including an empty string)
- `_` - represents a single character
- `[<list-of-characters>]` - represents a single character that must be one in the list of characters inside the brackets
- `[<character>-<character>` - represents a single character that must be within the specified range of characters
- `[^<list-or-range>]` - represents a character that isn't in the range or list inside the bracket
- `[]` - brackets let you search for characters that are used as wildcards e.g. `%[_]%` to search for underscores

---

## Date and Time data types

**Legacy** - date time components that are inseparable
- `DATETIME`
- `SMALLDATETIME`
**Later additions**
- `DATE
- `TIME`
- `DATETIME2` - bigger date range and better precision than legacy
- `DATETIMEOFFSET` - similar to `DATETIME2` but includes the offset from UTC

### Literals
You want to express date and time data type literals as character strings. When they're used in a predicate with a datetime type, they get implicitly converted to a datetime type as well.

```sql
SELECT *
FROM Sales.Orders
WHERE orderdate = '20160212'; -- character string gets implicitly converted cos orderdate is a datetime type
```

The language setting set on the database can affect how character literals are casted into datetime types e.g. if you specify the date as `2020-02-12`, this gets casted differently to a datetime type in British and US english settings. Because of this, it's best practice to write the date in a language-independent format.

![[language-neutral-dates.png]]

### Working with date / time only
If you're working with date and/or time only and are restricted to the legacy types that have both date and time, you can:
- set the time to midnight (`00:00:00.000`) if you're only working with dates
- set the date to January 1, 1900 (`1900-01-01`) if you're only working with time


**NOTE** - to help SQL Server filter indexes efficiently, you shouldn't manipulate the filtered column, you can revise the query to use range filters instead
```sql
SELECT orderid, custid, orderdate
FROM Sales.Orders
WHERE YEAR(orderdate) = 2015;

SELECT orderid, custid, orderdate
FROm Sales.Orders
WHERE orderdate >= '20150101' AND orderdate < '20160101';
```


### Datetime functions
![[datetime-functions.png]]

### Casting
There are 6 functions you can use for casting:

**Fails query if cast is not possible**
- `CAST` - standard
- `CONVERT` - can specify culture info / character style
- `PARSE` - more expensive than `CONVERT`

**Returns null if cast is not possible**
- `TRY_CAST` - standard
- `TRY_CONVERT`
- `TRY_PARSE`