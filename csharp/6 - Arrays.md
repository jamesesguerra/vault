### Arrays
- a space for a whole collection of values
```cs
int[] scores; // declare an array of ints
scores = new int[5]; // construct the array to hold 5 items
```

#### Accessing items relative to the back of the array:
```cs
int[] scores = { 1, 2, 3, 4, 5 };
Console.WriteLine(scores[^1]); // 5
```

#### Ranges (subarrays)
- the first number in the range is inclusive
- the second number in the range is exclusive
```cs
int[] scores = { 1, 2, 3, 4, 5 };

scores[0..3]; // 1, 2, 3
scores[0..]; // 1, 2, 3, 4, 5
scores[..^1] // 1, 2, 3, 4, 5
```

### Questions:
1. **Why is it that when you initialize an array, you don't have to call `new`?**
	- Not having to type the `new` keyword is a shortcut for having to say `int[] scores = new[] { 1, 2, 3, 4 };` since people found it to be tedious.
2. **How much slower is a `foreach` loop than a `for` loop in C#?**
	- A `foreach` loop is significantly slower... however, more often than not it's not worth the extra performance gains (premature optimization is the root of all evil). What foreach buys you is not only simpler syntax, but also better and more explicit code readability.