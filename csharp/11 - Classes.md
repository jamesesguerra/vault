### Objects and Classes
- **Objects** are like mini programs that are responsible for a slice of the entire program
- They contain data which are information that it must remember, and methods which are the capabilities it can perform when requested
- **Classes** are categories of objects or a blueprint for objects that belong to the class

### Instances of Classes
You create classes by using the `new` keyword and its constructor

```cs
Score score = new Score();
```

### Constructors
Constructors are like methods that get new instances into a legitimate starting state

```cs
class Score {
	public string name;
	public int points;
	public int level;

	public Score() {
		name = "Unknown";
		points = 0;
		level = 1;
	}
}
```

You add parameters to let the outside world provide initial values
```cs
class Score {
	...
	public Score(string n, int p, int l) {
		name = n;
		points = p;
		level = l;
	}
}
```

You can opt to initialize fields inline with default values, which runs before any constructor code
```cs
class Score {
	public string name = "Unknown"
	public int points = 0;
	public int level = 1;
}
```

Calling other constructors with `this`
```cs
class Score {
	...
	// call my other constructor with this parameter list
	public Score() : this("Unknown", 0, 1) {}

	public Score(string name, int points, int level) {
		this.name = name;
		this.points = points;
		this.level = level;
	}
}
```