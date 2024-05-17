Much like dapper, but you don't have to write SQL statements. You can treat your tables as entities in your program. There's more setup since you have to tell it where to look and how it can access stuff.

### Puzzle pieces 
`DBContext` 
- a base class you can inherit from to build your connections 

`OnConfiguring`
- override this, it fires whenever you create an instance of the class that inherits from `DBContext` 
- use `UseSqlServer`
- tells it where the DB is

`OnModelCreating` 
- fires whenever we create a model
- this is how you tell EF where to look for a table in the DB
- `DBSet` - your set of data inside the DB, you connect to it by correlating it back to one of your models 
- `Entity` - connects the `DBSet` to the table + other information like PK

Interactions
- `DbContext.Model.Add` - the ability to add records to table
- `DbContext.SaveChanges` - to save changes after adding record
- `DbContext.Model.ToList` - to pull data out of table