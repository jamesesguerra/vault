A **transaction** is a unit of work that might include multiple activities that query and modify data, and that can also change the data definition.

You can define the beginning of transactions *explicitly*
```sql
BEGIN TRAN;
```

and the end with
```sql
COMMIT TRAN; -- if you wanna commit --

ROLLBACK TRAN; -- if you wanna undo --
```

for example:
```sql
BEGIN TRAN;
	INSERT INTO dbo.T1(keycol, col1, col2) VALUES(4, 101, 'C');
	INSERT INTO dbo.T2(keycol, col1, col2) VALUES(4, 101, 'C');
COMMIT TRAN;
```

If you don't mark the transaction boundary explicitly, SQL *implicitly* treats each individual statement as a transaction ie SQL server commits the activity at the end of each statement.

#### Transaction properties (ACID)

**Atomicity**
- a transaction is a *unit of work*
- either all of the changes in the transaction take place or none do
**Consistency**
- the state of the data that the RDBMS exposes as concurrent transactions modify and query it
- you can only change data in predictable ways
**Isolation**
- ensures that transactions access only consistent data
- you can control what consistency means to your transactions through *isolation levels*
- two models handle isolation: locking and a combination of locking and row versioning
**Durability**
- data changes are always written to the transaction log before they are written to the database on disk
- a transaction is therefore considered durable once it's written to the log even though it hasn't made it to the disk yet
- on startup / recovery, the DB does a redo: writing to disk what is in the log but not on the DB yet, and an undo: undoing changes to the DB that wasn't written to the log




