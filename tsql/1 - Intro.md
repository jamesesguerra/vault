### Theoretical Background
- SQL was designed to query and manage data in a RDBMS
- a RDBMS is a database management system based on the relational model, which in turn is based on 2 mathematical branches: set theory and predicate logic
- SQL has several categories of statements
	- DDL - object definitions `CREATE`, `ALTER`, `DROP`
	- DML - query and modify data `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`
	- DCL - deals with permissions `GRANT` and `REVOKE`

#### Set Theory
- a set is a collection `M` into a whole of definite, distinct objects `m`
- your focus should be on a collection of objects as a whole rather than individual entries, and tables should be considered a single entity
- distinct means that every element of a set must be unique
- the order in which set elements are listed is not important

#### Predicate Logic
- a **predicate** is a property or expression that is either true or false
- the relational model relies on predicates to enforce data integrity and define its structure
- used for filtering data to define subsets
- a property can define a whole set, which is better than listing down every element of a set e.g. for the predicate "*x* is an integer that is greater than or equal to 0 and less than or equal to 9", the set `{ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 }` can be generated for which the previous predicate holds true

#### The relational model
The relational model is a semantic model for data management and manipulation that is based on set theory and predicate logic. Its goal is to enable consistent representation of data with minimal or no redundancy without sacrificing completeness, and to define data integrity. 

**Propositions, predicates, and relations**
A relation is a a representation of a set, and in SQL, these are tables. A single relation should represent a single set. Relations are made up of a heading and a body. Headings are the columns, and the body is the set of rows.

When designing a data model for the database, you represent all data with relations (tables). You start by identifying propositions that you will need to represent your data. A **proposition** is an assertion or statement that must be true or false. If a proposition is true, it will manifest itself as a row in a table. 

The next thing to do is formalize propositions by defining the structure (the heading of the relation). Attributes are identified by an attribute name and a type name e.g. `firstname character string`. A type constrains an attribute to a certain set of possible values

**Constraints**
One of the greatest benefits of the relational model is being able to define data integrity as part of the model. Data integrity is achieved through rules called **constraints** that are defined in the model.

Examples include:
- null constraints (whether a row can have a null marker as a value of an attribute)
- entity integrity with *candidate keys* (an attribute that can uniquely define a row); you typically assign one of the candidate keys as the primary key as the preferred way to identify a row, and all other keys will be *alternate keys*
- referential integrity with *foreign keys* that is defined on one or more attributes of a relation (the referencing relation) and references a candidate key in another (or possible the same) relation; this key constrains the value of the foreign key to be one of the values that appear under the candidate key of the referenced relation

**Normalization**
Normalization is a formal mathematical process to guarantee that **each entity will be represented by a single relation.** In a normalized database, you avoid anomalies during data modification and keep redundancy at a minimum.

#### Types of Database Systems
You can implement these systems with a SQL Server database and manage and query it with T-SQL.

**Online transactional processing (OLTP)**
The primary focus of OLTP is data modification and not reporting. Transactions mainly insert, update, and delete data. The relational model is targeted at OLTP systems because a normalized model provides good performance for data entry and data consistency. If you need to modify data, you need only modify it in one place, which reduces the chance of errors and has good query performance.

However, OLTP systems are not suitable for reporting because a normalized model involves many tables (one for each entity) with complex relationships. Querying and simple reporting would requiring joining multiple tables and would result in poor performing queries. 

**Data warehouse (DW)**
A data warehouse is an environment designed for data-retrieval and reporting. It's optimized to support data-retrieval by having intentional redundancy, fewer tables, and simpler relationships. 

**ETL**
The process that pulls data from source systems (OLTP and others), manipulates it, and loads it into the data warehouse is called ETL. The ETL process involves the use of a data-staging area between the OLTP and DW. It usually resides in a relational database, and is used as a data-cleansing area. 

#### Databases
A **database** is a container for user objects such as tables, views, and stored procedures, as well as data for an application.

A database contains schemas, and schemas contain objects. A schema is a container for objects such as tables, views, and stored procedures. 

![[schemas.png]]

`USE`
The `USE` statement sets the current database context to that of the specified database. It's important to use this command in scripts that create new objects to ensure that the SQL Server creates them in the correct database.

```sql
USE TSQLV6;
```

`DROP IF EXISTS
This command drops the table if it already exists; this was introduced in SQL Server 2016. If using an earlier version, a different command must be used. 

```sql
DROP TABLE IF EXISTS dbo.Employees;

-- for sql server 15 below
IF OBJECT_ID(N'dbo.Employees', N'U') IS NOT NULL DROP TABLE dbo.Employees;

```

`CREATE TABLE`
Defines the heading of the relation by specifying the name of the relation and its attributes 

```sql
CREATE TABLE dbo.Employees
(
	empid     INT         NOT NULL,
	firstname VARCHAR(30) NOT NULL,
	lastname  VARCHAR(30) NOT NULL,
	hiredate  DATE        NOT NULL,
	mgrid     INT         NULL,
	ssn       VARCHAR(20) NOT NULL,
	salary    MONEY       NOT NULL
);
```

#### Defining Data Integrity
Data integrity enforced as part of the model ie as part of the table definitions, is considered **declarative data integrity**. Data integrity enforced through code ie with stored procedures and triggers, is called **procedural data integrity**.

##### Declarative data integrity
You can enforce declarative data integrity through `CREATE TABLE` or `ALTER TABLE`. 

**Primary-key constraints**
A primary-key constraint enforces the uniqueness of rows and disallows `NULL`s in the constraint attribute. Each table can have only 1 primary key. 

```sql
ALTER TABLE dbo.Employees
	ADD CONSTRAINT PK_Employees
	PRIMARY KEY(empid);
```

**Unique constraints**
A unique constraint enforces the uniqueness of rows to allow you to implement the concept of alternate keys (alternative primary keys). Unlike primary keys, you can have multiple unique constraints within the same table, and the attribute that you apply this constraint to isn't limited to `NOT NULL`.

```sql
ALTER TABLE dbo.Employees
	ADD CONSTRAINT UNQ_Employees_ssn
	UNIQUE(ssn);
```

**Foreign-key constraints**
A foreign-key constraint enforces referential integrity. Its purpose is to restrict the values allowed in the FK columns in the referencing table, to those that exist in the CK (candidate key) columns in the referenced table. 

Foreign keys enforce a no action -- no action means that an attempt to delete a row in the referenced table / update the candidate key values will be rejected if related rows exist in the referencing table. You can define the FK with actions that will compensate for these attempts.

Options
- `ON DELETE
- `ON UPDATE`

Actions
- `CASCADE` - means the operation will be cascaded to the related rows in the referencing table
- `SET DEFAULT`
- `SET NULL`

```sql
ALTER TABLE dbo.Orders
	ADD CONSTRAINT FK_Orders_Employees
	FOREIGN KEY(empid)
	REFERENCES dbo.Employees(empid);
```

**Check constraints**
You can define a check constraint to define a predicate that a row must meet to be entered into the table or to be modified. 

```sql
-- checks if the salary is a positive value --
ALTER TABLE dbo.Employees
	ADD CONSTRAINT CHK_Employees_salary
	CHECK(salary > 0.00);
```

**Default constraints**
A default constraint is associated with a particular attribute. It's an expression that's used as the default value when an explicit value isn't specified for the attribute when you insert a row.

```sql
ALTER TABLE dbo.Orders
	ADD CONSTRAINT DFT_Orders_orderts
	DEFAULT(SYSDATETIME()) FOR orderts;
```