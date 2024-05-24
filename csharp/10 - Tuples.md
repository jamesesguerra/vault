**NOTE** - Most C# programmers only use tuples occasionally

### Basics of Tuples
Tuples are simple composite types -- types that are composed of multiple data elements

To create a tuple, you list the values and data types (or you can just use var in some cases) in parentheses, separated by commas
```cs
(string, int, int) score = ("R2-D2", 12420, 15);
// or 
var score = ("R2-D2", 12420, 15);
```

You can access a tuple's elements like so
```cs
Console.WriteLine(score.Item1); // R2-D2
```

The type of a tuple is determined by the type and order of the "type" parts of it
```cs
(string, int, int) score1 = ("R2-D2", 12420, 15);
(string, int, int) score2 = score1; // i can do this
(int, string) score3 = score1; // but not this
```

#### Tuple Element Names
You can *pretend* to have alternative and more meaningful names for the individual parts of your tuple. These names aren't part of the tuple itself and are merely cosmetic

```cs
(string Name, int Points, int Level) score = ("R2-D2", 12420, 15);

// or if you use var

var score = (Name: "R2-D2", Points: 12420, Level: 15);
```

#### Tuples and Methods
Tuples are just like any other type -- you can use them as parameters in a method

```cs
void DisplayScore((string Name, int Points, int Level) score) {
	Console.WriteLine($"{score.Name} {score.Points} {score.Level}");
}
```

...or use them as a return type
```cs
(string Name, int Points, int Level) GetScore() => ("R2-D2", 12420, 15);
```

#### Deconstruction 
You can take all the parts of a tuple and separate them into variables by using deconstruction or unpacking

```cs
var score = (Name: "R2-D2", Points: 12420, Level: 15);
(string name, int points, int level) = score;

// using discards if the level weren't important
(string name, int points, _) = score;
```

A clever usage of tuple deconstruction is swapping the contents of 2 variables
```cs
double x = 4;
double y = 2;
(x, y) = (y, x);
```

![[tuples.png.png]]