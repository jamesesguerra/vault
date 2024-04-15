A **null reference** is a variable that holds a reference to `null` or the absence of a value / object.

They are helpful when representing the possibility for there to be no data available for something e.g. a vehicle having a `Character _driver` field -- in which case the driver's seat being empty may be represented by having the `_driver` field point to a null reference.

They are without consequences as trying to access members of a null reference will crash your program.
```cs
string response = Console.ReadLine();
Console.WriteLine(response.Length); // if response is pointing to a null reference, the program will crash when we access its Length field
```

### Null or not?
A key guideline is that **for reference-typed variables, stop and think if null should be an option**.
- If null **IS** allowed, check for null before using its members (fields, properties, methods)
- if null **ISN'T** allowed, ensure you don't accidentally assign null to it

To show that a variable should allow null, a reference-typed variable can have a `?` at the end to indicate that option. Correctly applying (or not applying) the `?` at the end is helpful not just for other programmers to understand the code but also for the compiler to help you spot null-related issues.
```cs
string? name = Console.ReadLine(); // can return null
```

You can check for null using a *null check*, which is simply comparing the reference against `null`:
```cs
string? response = Console.ReadLine();

if (response is not null) Console.WriteLine(response.Length);
```

This null-checking can get tedious if you try to access consecutive members with a dot operator, for example:
```cs
Console.WriteLine(_scoreManager.GetScores()[0].Name); // need to check if score manager and GetScores() and GetScores[0] are null
```

### Null-Conditional Operators
The null-conditional operators `?.` and `?[]` can be used in place of `.` and `[]` to simultaneously check for null while accessing the member.

Each operator evaluates the part before it to see if it's null. If it is, then no further evaluation happens and the whole expression evaluates to `null`. If it isn't, evaluation will continue as normal.

```cs
Console.WriteLine(_scoreManager?.GetScores()?[0]?.Name);
```

### The Null-Coalescing Operator
The null-coalescing operator `??` is a useful tool for providing a fallback value. It takes an expression that *might* be null and provides a value to use as its fallback if it is.

```cs
string? response = Console.ReadLine();
Console.WriteLine(response ?? "no response");
```

There is also a compound assignment operator
```cs
string? response = Console.ReadLine();
response ??= "no response";
```

### The Null-forgiving Operator
Although the compiler is pretty good at inferring which parts of your code can and can't be null, there are some occasions where you might know something about the code that the compiler can't infer from its analysis.

If you're sure that something can't be null, use the `!` operator to tell the compiler, "I know this looks like a potential null problem, but it won't be, trust me".
```cs
string message = MightReturnNullIfNegative(+10)!;
```

### Questions:
1. **If reference-typed variables contain a reference to objects, what do they contain if they point to a null reference?**
	- Nothing, a null reference isn't pointing to anything and so it won't consume any memory.