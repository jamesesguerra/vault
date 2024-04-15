## Static Members
Static members detach themselves from specific identities of objects. When you mark members as `static`, you tie them to the class itself, essentially **sharing** them between instances.

### Static Fields and Properties
Applying the `static` keyword to a field makes it a static field -- a field that affects every instance of a class. This is why regular, non-static fields are also called instance fields -- since they only affect individual instances of a class.
```cs
class Score {
	private static readonly int PointThreshold = 1000;
	private static int LevelThreshold { get; } = 4;
}
```

### Global State
The combination of making a field public, static, and not read-only is dangerous as it creates global state. **Global state** is data that can be changed and used anywhere in your program. Changes in one part of your program can affect other parts even though they're unrelated.

A good example of this is `Console.ForegroundColor`. When you set it to `ConsoleColor.Red`, everything written to the console after that will be red, until somebody else changes it back.

### Static Methods
Since a static method isn't tied to a specific instance, it can't refer to any non-static (instance) fields, properties, or methods. They're mostly used for **utility methods** that provide some sort of service related to the class, but that isn't directly tied to a specific instance.

They're also used for **factory methods** which create new instances for the outside world as an alternative to calling a constructor. This makes your code more explicit about what you're creating.
```cs
Rectangle square = Rectangle.CreateSquare(5);

class Rectangle {
	...
	public static Rectangle CreateSquare(float size) => new Rectangle(size, size);
}
```

**NOTE:** You can't have static methods refer to instance or non-static members as it wouldn't make sense (having a shared method access something that isn't shared).

### Static Constructors
Static constructors are useful for running logic to initialize static fields and/or properties with default values.

```cs
class Score {
	private static int LevelThreshold { get; set; }
	static Score() {
		LevelThreshold = 4;
	}
}
```

### Static Classes
When a class is nothing more than a collection of related utility methods, fields, or properties, you may want to forbid creating instances of the class by marking it with the `static` keyword. These have properties and methods that wouldn't depend on the kind or instance of a class, every result would be the same no matter what, which is why it wouldn't make sense to make any instances of it.

```cs
// if it weren't a static class
Calculator c1 = new();
Calculator c2 = new();

c1.Add(5, 4); // 9
c2.Add(5, 4); // 9 also, it's behavior is the same for every instance

public static class Calculator {
	public static int Add(int a, int b) => a + b;
}
```