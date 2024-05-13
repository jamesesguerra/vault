LINQ allows you to manipulate and query different kinds of data and collections. They're extension methods that mostly take in a `IEnumerable<T>` as a parameter, and return `IEnumerable<T>`. This means you can compose these methods into a pipeline for code composition:

```cs
var evenNumbersOrderedAscendingly = numbers.Where(number => number % 2 == 0).OrderBy(number => number);

// or if the element in the collection is also an IEnumerable, you can call another linq method inside it

var anyWordsAllWhitespace = words.Any(word => word.All(letter => char.IsWhiteSpace(letter)));
```

### Deferred Execution
LINQ queries only run when they need to, so you think of them more as **queries** and not **data**

```cs
var words = new List<string> { "a", "bb", "ccc", "ddd", "eeee", "fffff" };

var shortWords = words.Where(word => word.Length < 3);

// if we print shortWords' contents here, it would be a and bb

words.Add("e");

// if we print it here, it would be a, bb, and e, even though it seems like short words values were already set in stone; you should think of shortWords more of a query that can query the words list, and not as data containing the result of the query
```

If you print `shortWords` contents after the addition of `"e"`, it would be `a`, `bb`, and `e`, even though it seems like short words values were already set in stone; you should think of `shortWords` more of a query that can query the words list, and not as data containing the result of the query. 

Although you can materialize the query with `ToList()`, in which case it should be treated as data
```cs
var shortWords = words.Where(word => word.Length < 3).ToList();
```

### Important Ones

	1. Where - filter
	2. Any - returns true if any of the items passes the condition
	3. All returns true only if all items pass the condition
	4. Count - like Where but counts the items instead of returning them, use Count() if you want to count all items
	5. Contains - returns true if the collection contains an item
	6. OrderBy - orders a collection ascendingly by a specific property or just by the element itself (x => x)
	7. OrderByDescending - same as OrderBy but orders descendingly
	8. ThenBy / ThenByDescending - follows OrderBy without resetting the order
	9. First / Last - returns the first / last element / element that passes the condition
	10. FirstOrDefault / LastOrDefault - same as First / Last but returns the default value for the type if no element matches the criteria
	11. Distinct - removes duplicates
	12. Select - map