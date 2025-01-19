> A **database schema** is an overall logical representation of a database including its tables, views, fields, relationships, etc. A **data model** is the abstraction an ORM uses to interact with that structure.

### Choosing a DBMS
A **database management system** is a software program design for creating and managing large sets of structured data. Those data sets are typically called databases.

#### SQL vs NoSQL
##### Schema complexity
The biggest difference between them is their data storage model. In SQL databases, the data is modeled by using tables with a fixed schema of rows and columns, with each table representing a single record type. In NoSQL databases, the data is modeled by using collections of documents with a flexible schema of key/value pairs. Because of the fixed schema nature of SQL databases, we have to declare each table's schema before inserting data.

Their approaches to modeling relationships is also different since SQL databases use primary keys to uniquely identify each record type, which can then be used as references in other tables. In NoSQL databases, the parent-child relationships are embedded in the parent document, resulting in fewer collections (fewer tables).

And so you'd need to consider how the schema complexity increases as you add more child entities to the database. For SQL databases, you'd likely need to add a table and more JOINs, thus requiring more development effort. For NoSQL, the single parent collection gets increasingly more complex.

##### Scaling
SQL databases are designed to run on a single server because they need to maintain the integrity of their tables, indexes, and constraints. This means that they're designed to scale **vertically.** On the other hand, NoSQL databases are designed to scale horizontally, having no single point of failure.

##### Usage scenarios
SQL is ideal for hosting a set of normalized data, when all rows are supposed to have the same structure. Conversely, when we have a big amount of data, or when the structure of the data requires several parent-child relationships, NoSQL is better.

### EF Core
EF Core is an ORM used to help developers interact with a DBMS source in a standardized, structured way. There are several reasons to choose an ORM:
- reduces the development time and the size of the code base
- standardize data retrieval strategies and reduce mistakes
- take advantage of several RDBMS features out of the box without having to learn SQL
- minimize string manipulation methods used to write SQL by hand
- manually adding our own countermeasures against SQL injection

Install required packages when using a SQL Server database provider.
```sh
dotnet add package Microsoft.EntityFrameworkCore.SqlServer 
dotnet add package Microsoft.EntityFrameworkCore.Design # for creating migrations
```

- `DbContext` - database session used for CRUD operations
- `Microsoft.EntityFrameworkCore.SqlServer` - database provider that translates EF Core queries into SQL Server specific commands
- `Microsoft.Data.SqlClient` / `System.Data.SqlClient` - database driver that handles the low-level communication with the SQL Server DB, also handles connection pooling

#### Data modeling
The EF Core data model can be created in 2 ways:
- **code-first** - create the C# entity classes manually and use it to create the database schema, keeping it in sync using migrations
- **database-first** - generating C# classes by reverse-engineering them from an existing database schema, and keeping them in sync with EF's scaffolding tools

#### DbContext
With a code-first approach, the first class you need to create it the `DbContext` class, a database session abstraction that allows you to perform CRUD operations using C# code. You can use the `ModelBuilder` API aka the Fluent API to configure the data model. Fluent API is usually used for model-wide settings, while data annotations are used for entity-specific settings.

```csharp
public class ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : DbContext(options)  
{  
    protected override void OnModelCreating(ModelBuilder modelBuilder)  
    {
	    base.OnModelCreating(modelBuilder);
		// customize model after default conventions here
    }
}
```

**Setting up DbContext**
`DbContext` represents a **unit of work** -- a set of CRUD operations performed against a database during a single business transaction. 
```csharp
builder.Services.AddDbContext<ApplicationDbContext>(options =>  
    options.UseSqlServer(  
        builder.Configuration.GetConnectionString("DefaultConnection"))  
    );
```

#### Navigation Properties - setting up relationships between entities
A navigation property is an entity property that directly references a single related entity or a collection of entities. In the case of using junction tables, you first create nullable properties on the entities, pointing to the tables that they're mediating between.

```csharp
public class BoardGames_Domains  
{  
    [Key]  
    [Required]  
    public int BoardGameId { get; set; }  
    
    [Key]  
    [Required]  
    public int DomainId { get; set; }  
     
    public BoardGame? BoardGame { get; set; }  
    public Domain? Domain { get; set; }  
}
```

Then in each of the main tables, you add a reference back to the junction table
```csharp
public class BoardGame  
{  
    public ICollectino<BoardGames_Domains>? BoardGames_Domains { get; set; }  
    public ICollection<BoardGames_Mechanics>? BoardGames_Mechanics { get; set; }  
}

public class Domain  
{  
    public ICollection<BoardGames_Domains>? BoardGames_Domains { get; set; }
}
```

You can then define a one-to-many relationship between the `BoardGame` entity and the `BoardGames_Domains` entity (using the `BoardGameId` FK), and then a one-to-many relationship between the `Domain` and the `BoardGames_Domains` as well.

```csharp
// inside OnModelCreating
modelBuilder.Entity<BoardGames_Domains>()  
    .HasOne(x => x.BoardGame)  
    .WithMany(y => y.BoardGames_Domains)  
    .HasForeignKey(f => f.BoardGameId)  
    .IsRequired()  
    .OnDelete(DeleteBehavior.Cascade);

modelBuilder.Entity<BoardGames_Domains>()  
    .HasOne(x => x.Domain)  
    .WithMany(y => y.BoardGames_Domains)  
    .HasForeignKey(f => f.DomainId)  
    .IsRequired()  
    .OnDelete(DeleteBehavior.Cascade);
```

#### DbSets
Entity types can be made available through the `DbContext` using a container known as `DbSet`. Each `DbSet` creates a homogenous set of entities that allow us to perform CRUD operations on that entity set. In other words, if the entity class represents a single record in a table, the `DbSet` represents the database table itself.

They're typically exposed as public properties, one for each entity
```csharp
// outside OnModelCreating
public DbSet<BoardGame> BoardGames => Set<BoardGame>();  
public DbSet<Domain> Domains => Set<Domain>();
public DbSet<BoardGames_Domains> BoardGames_Domains => Set<BoardGames_Domains>();
```

#### Migrations
A **migration** is just a file that represents the required set of SQL commands to keep the database schema updated / in sync with our C# entities.

To add an initial migration:
```sh
dotnet ef migrations add Initial
```

To apply the latest migrations. You can also specify the migration name to update / rollback to a specific migration.
```bash
dotnet ef database update
dotnet ef database update <migration>
```

prev: [[3 - RESTful Principles & Guidelines]]
next: [[5 - CRUD with LINQ]]