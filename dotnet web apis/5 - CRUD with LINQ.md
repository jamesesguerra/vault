To perform CRUD operations, you need a reference to the `DbSet`s exposed by your `ApplicationDbContext`. To do this, you inject it in your C# class.

```csharp
public class BoardGameRepository() {
	private readonly ApplicationDbContext _context;

	public BoardGameRepository(ApplicationDbContext context) {
		_context = context;
	}
}
```

### Read
You then use the context and grab the "table" you want data from, and materialize it, effectively executing the SQL the query is meant to perform to the database. For performance benefits, use the asynchronous version of `ToArray()`
```csharp
public async Task<IEnumerable<BoardGame>> Get() {
	var query = _context.BoardGames;

	return await query.ToArrayAsync();
}
```

#### Paging
To do server-side paging, your action method needs to know three things:
- **PageIndex** - the zero-based index of the page (page number)
- **PageSize** - the number of records contained on each page
- **RecordCount** - the total count of records, by which the total number of pages can also be calculated

```csharp
[HttpGet("/")]
public async Task<IEnumerable<BoardGame>> Get(int pageIndex = 0, int pageSize = 10) {
	var query = _context.BoardGames
		.Skip(pageIndex * pageSize)
		.Take(pageSize);

	return new RestDTO() {
		Data = await query.ToArrayAsync(),
		RecordCOunt = await _context.BoardGames.CountAsync()
	}
}
```

#### Sorting
You can either sort on the DBMS, and then take what you need, or take all the records and sort programmatically on the server. The more preferred approach is to sort on the DBMS because it's more efficient, not to mention that you don't have to get all records and only return a subset.

```csharp
var query = await _context.BoardGames
	.OrderBy(b => b.Name)
	.ToArrayAsync();
```

This is good if you know in advance by what column you want to sort by. But if you want to support multiple columns for sorting, you'd have to do something ugly to create and use different lambdas for each sort column. To resolve this, download the package: `System.Linq.Dynamic.Core` to easily pass in a string as a sort column instead.

```csharp
var query = await _context.BoardGames
	.OrderBy("Name ASC")
	.ToArrayAsync();
```

#### Filtering
```csharp
public async Task<RestDTO<BoardGame[]>> Get(  
    int pageIndex = 0,  
    int pageSize = 10,  
    string sortColumn = "Name",  
    string sortOrder = "ASC",  
    string? filterQuery = null)  
{  
    var query = _context.BoardGames.AsQueryable(); // cast to IQueryable so you can chain linq methods conditionally
    var recordCount = await query.CountAsync(); // count number of filtered rows before applying the paging
    
    if (!string.IsNullOrEmpty(filterQuery))  
        query = query.Where(b => b.Name.Contains(filterQuery));  
    
    query = query  
        .OrderBy($"{sortColumn} {sortOrder}")  
        .Skip(pageIndex * pageSize)  
        .Take(pageSize);  
}
```

### Create
```csharp
public async Task Post() {
	var boardGame = new BoardGame();
	_context.BoardGames.Add(boardGame);

	await _context.SaveChangesAsync();
}
```

### Update
In terms of updating data, the choice of using `PUT` or `POST` depends on whether the HTTP request will be idempotent or not. In the case where you need to update a `LastModifiedData` property of a record, this means that this operation is non-idempotent, making `PUT` not an appropriate candidate.

#### DTOs
Entity classes are wrappers for interacting with database tables. They might contain a lot of data that clients shouldn't see. For this reason, you should create a **DTO**. Creating a DTO is the best practice whenever you want to send data to and from your client.

```csharp
[HttpPost("/")]
public async Task<RestDTO<BoardGame?>> Post(BoardGameDTO model) {
	var boardGame = await _context.BoardGames.FirstOrDefaultAsync(b => model.Id == b.Id);

	if (boardGame is not null) {
		if (!string.IsNullOrEmpty(model.Name)) boardGame.Name = model.Name;
		if (model.Year.HasValue && model.Year.Value > 0) boardGame.Year = model.Year.Value;  
  
		boardGame.LastModifiedDate = DateTime.Now;  
		_context.BoardGames.Update(boardGame);  
		await _context.SaveChangesAsync();
	}

	return new RestDTO<BoardGame?>() { Data = boardGame };
}
```

### Delete
```csharp
[HttpDelete]  
public async Task<RestDTO<BoardGame[]?>> Delete(IEnumerable<int> idList)  
{  
    var boardGames = await _context.BoardGames  
        .Where(b => idList.Contains(b.Id)).ToArrayAsync();  
        
    if (boardGames.Count() != 0)  
    {
	    foreach (var boardGame in boardGames)  
	    {
		    _context.BoardGames.Remove(boardGame);  
        }
        await _context.SaveChangesAsync();  
    }  
    
    return new RestDTO<BoardGame[]?>()  
    {  
        Data = boardGames  
    };
}
```

prev: [[4 - Working with Data]]
next: [[6 - Data Validation & Error Handling]]