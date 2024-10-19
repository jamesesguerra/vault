**EF Core** is an ORM library that provides an object-oriented way to access databases. It communicates with the database for you and maps the results to .NET classes and objects.

In EF Core:
- classes map to tables
- properties map to columns
- references map to foreign-key relationships between tables
- objects map to rows in a table

#### How it works
You start by creating your application's `DbContext` which serves as the entry point for interactions with EF Core. You also use this to configure EF Core. It then looks for any `DbSet` properties on the `DbContext` to create an internal representation of the database. The class used on `DbSet` will be inspected, as EF Core loops through all its properties and adds it into its internal model. This includes modeling relationships in the database if there are references between .NET classes.

#### Adding EF Core to an application
It's best practice to have a service layer which your endpoints call. The service layer encapsulates all the data access and business logic to keep concerns separated so that the endpoint doesn't access EF Core directly.

Adding EF Core to an application has requires multiple steps:
1. Choose a database provider e.g. Postgres, SQLite, SQL Server
2. Install the EF Core NuGet packages
3. Design the app's `DbContext` and entities that make up your data model
4. Register the app's `DbContext` in the DI container
5. Use EF Core to generate a migration describing your data model
6. Apply the migration to the database to update the database's schema

##### Choosing a DB provider
Adding a DB provider is a matter of adding support with the right NuGet package.
- **Postgres** - Npgsql.EntityFrameworkCore.PostgreSQL
- **SQL Server** - Microsoft.EntityFramework.SqlServer
- **MySQL** - MySql.Data.EntityFrameworkCore
- **SQLite** - Microsoft.EntityFrameworkCore.SQLite, Microsoft.EntityFrameworkCore.Design

##### Building a data model
Creating the app's data model is just a matter of creating POCO classes that conform to EF Core conventions:
- primary keys are suffixed with `Id` e.g. for a `Recipe` class you'd have a `RecipeId` property
- foreign keys would have the primary key property of its referenced table
- the table holding a primary key would have an `ICollection<T>` property to indicate it has a one-to-many relationship with another table

```csharp
public class Recipe {
	public int RecipeId { get; set; }
	public required string Name { get; set; }
	public required ICollection<Ingredient> Ingredients { get; set; }
}

public class Ingredient {
	public int IngredientId { get; set; }
	public int RecipeId { get; set; }
	public required string Name { get; set; }
}
```

You then want to create your app's database context by inheriting from `DbContext` and expose the top-level entities in your app so that EF Core can map and discover the entity. In this case, mapping the `Ingredient` entity isn't required since it's already exposed on the `Recipe` entity, and EF Core can model it correctly. However, to access the `Ingredient` entities in the DB, you have to go through the `Recipe` entity's `Ingredients` property.

```csharp
public class AppDbContext : DbContext {
	public AppDbContext(DbContextOptions<AppDbContext> options)
	: base(options) { }
	
	public DbSet<Recipe> Recipes { get; set; }
}
```

##### Register to DI Container
Once you have the app's database context and entities in place, you need to add the `AppDbContext` to the DI container so that you can access the database from the service layer. 

```csharp
var connString = builder.Configuration.GetConnectionString("DefaultConnection");
builder.Services.AddDbContext<AppDbContext>(options => options.UseSqlite(connString));
```

In this case, you put your database connection string in the `ConnectionStrings` section of your `appsettings.json`. For SQLite, that would look something like this
```json
{
	"ConnectionStrings": {
		"DefaultConnection": "Data Source=recipe_app.db"
	}
}
```

##### Migrations
The difficulty with using databases along with your application is that your application code is tied to a particular version of the database, and you need to ensure that the two are in sync. Rolling back a deployment with code is easy, but tricky with databases since they contain data. 

**Migrations** help with this and are files in your application that defines how the data model changed . They provide a record of how your database schema evolved over time as part of your app, so that the schema is always in sync with your app's data model.

1. Install .NET tool
```sh
dotnet tool install --global dotnet-ef
```

2. Create your first migration
```bash
dotnet ef migrations add InitialSchema
```

This command generates 3 files:
- **migration file** - describes the actions to take on the database such as creating a table or adding a column
- **migration designer.cs file** - describes EF Core's internal model of your data model at the point in time when the migration was generated 
- **model snapshot file** - describes EF Core's current internal model and is updated when you add another migration

Creating migration files doesn't change anything on the database. You have to apply the migrations to a database by running this command. This, in turn, creates a database depending on whether it's already created or not.
```sh
dotnet ef database update
```

##### Creating an entity with EF Core
1. Make the service layer class (register service in DI container)
```csharp
public class RecipeService {
	readonly AppDbContext _context;
	
	public RecipeService(AppDbContext context) {
		_context = context;
	}

	public async Task<int> CreateRecipe(CreateRecipeCommand cmd) {
		var recipe = cmd.ToRecipe();
		_context.Add(recipe);
		await _context.SaveChangesAsync();
		return recipe.RecipeId;
	}
}
```

2. Add the endpoint
```csharp
app.MapPost("/recipes", async (CreateRecipeCommand input, RecipeService recipeService) => {
	var id = await recipeService.CreateRecipe(input);
	return Results.CreatedAtRoute("view-recipe", new { id });
});
```

##### Loading entities
To load entities, you use LINQ queries and chain them together

1. Make the service layer method
```csharp
public async Task<List<RecipeSummaryViewModel>> GetRecipes() {
	return await _context.Recipes
		.Where(r => !r.IsDeleted)
		.Select(x => new RecipeSummaryViewModel
		{
			Id = x.RecipeId,
			Name = x.Name,
			TimeToCook = $"{x.TimeToCook.Hours}hrs {x.TimeToCook.Minutes}mins",
			NumberOfIngredients = x.Ingredients.Count()
		})
		.ToListAsync();
}
```

2. Make the endpoint
```csharp
app.MapGet("/recipes", async (RecipeService service) =>
{
	return await service.GetRecipes();
})
```

