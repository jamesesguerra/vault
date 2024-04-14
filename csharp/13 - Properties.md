### Key Idea
We need properties since information hiding made our code more complex. By restricting access to a class's data, we needed to add getters and setters on top of our instance variables.

We can use properties to reap the benefits of both information hiding and abstraction while still keeping our code simple. A **property** pairs a getter and setter under a shared name with field-like access.

```cs
public float Width
{
    get => _width;
    set => _width = value;
}

Rectangle r = new(5, 5);
r.Width; // i can access like a field now!
```

Many properties provide access to a single field known as its **backing field**
```cs
private float _width;
```

They don't require both getters and setters. Some properties can be `get`-only or `set`-only. `get`-only properties make sense for something you wouldn't wanna change from the outside
```cs
public float Area => _width * _height;
```

They also don't have to have the same access modifiers. A public getter and a private setter would look like this
```cs
public float Width {
	get => _width;
	private set => _width = value; // can't set me from the outside!
}
```

### Auto Properties
Many properties end up looking like this. So for properties that don't need any complex logic, you can use auto properties to make things simpler. You don't need to define bodies for your getters and setters, and it also generates a backing field for you, albeit being inaccessible. 

```cs
// pretty common
private string _name;
public string Name {
	get => _name;
	set => _name = value;
}

// auto properties make things simpler
public string Name { get; set; }
```

You can have immutable or get-only properties that can only be set from within the constructor, or be initialized a value
```cs
// initialize with a value
public string Name { get; } = "John";

public Player(string name) {
	Name = name;
}
```

Read-only also applies to fields by using the `readonly` keyword
```cs
private readonly string _name;
```

### Object Initializers and the `init` accessor
Object initializers are another way of setting your class's initial state. They're similar to doing finishing touches since they run right after the constructor runs. 

You *still* have to supply your object with everything the constructor expects. But with object initializers, you don't have to. You can choose what properties you want to set. The properties you don't set will just get supplied with the default value of that type. That's the main difference between object initializers and constructors.

```cs
class Rectangle {
	public float Width { get; set; }
	public float Height { get; set; }
	public float Area => Width * Height;
}

Rectangle r = new Rectangle { Width = 5, Height = 5 };
```

Allowing properties to be set in an object initializer entails giving them a setter. But if you don't want them to be settable, but you do want them to be able to be initialized with an object initializer, you can use the `init ` accessor
```cs
public float Width { get; init; }

Rectangle r = new Rectangle { Width = 5, Height = 5 };
r.Width = 6; // cant do this anymore
```

The order in which default values get set is property initializer -> constructor -> object initializer

### Questions:
1. **What is the difference between `const` and `readonly`?**
	- `const` is a compile-time constant and is implicitly static, whereas `readonly` is a runtime constant. `const` is also limited to value types while `readonly` can be reference types and instance or static.
