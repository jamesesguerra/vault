Inheritance allows you to accomplish two things:
1. It allows you to treat the subtypes as the more generalized type whenever necessary
2. It allows you to consolidate common behavior / duplicated code in 2 related classes

### The `object` class
The `object` class is the mother of all classes as it's the base class of everything. Everything it contains will also appear on every object created.

Its two most important methods are `ToString` and `Equals`. `ToString` creates a string representation of any object, and by default it returns the object's type. `Equals` checks if two references point to the same object on the heap.

```cs
object thing = new object();
Console.WriteLine(thing.ToString()); // prints object

object a = new object();
object b = a;
object c = new object();
Console.WriteLine(a.Equals(b)); // true
Console.WriteLine(a.Equals(c)); // false
```

You use the derived class anywhere the base class is expected. However in doing so, the reference isn't able to access any of the members of the derived class and is cut down to its basic essence of being just an `object`.

```ts
object p1 = new Point(2, 2);
Console.WriteLine(p1.ToString()); // ok
object.X // not ok
```

This relationship is only one-way though, as you can't use a base class where a derived class is expected, or even where other derived classes are expected.

```cs
Point p1 = new object(); // not ok
Point p1 = new Line(); // not ok
```

### Constructors
Derived classes inherit most members from a base class, but not constructors. Because constructors put a new object into a valid starting state, a base class's constructor can't make any guarantees about putting a derived class's object into a valid starting state. So derived classes must supply their own constructor and also call the constructors defined in the base class to make the "base" part of themselves first.

To call the base class' constructor, you use the `base` keyword. It's similar to `this` except instead of calling this class's constructors, it calls the base class's constructors instead. You also can't use both `this` and `base` together on a constructor.

```cs
class GameObject {
	...
	public GameObject(int x, int y) {...}
}

class Asteroid : GameObject {
	public Asteroid(int x, int y) : base(x, y) {}
}
```

### Casting and Checking for Types

#### Downcasting
If you need to get a derived type from a base type, you need to cast it first. It's a way of telling the compiler it's safe to treat a base class as a derived class. Casting from a base class to a derived class is called **downcasting**. 

```cs
GameObject imAnAsteroidTrustMe = new Asteroid(3, 4);
Asteroid asteroid = (Asteroid)imAnAsteroidTrustMe;

Console.WriteLine(asteroid);
```

The above example is safe since you can clearly see that `imAnAsteroidTrustMe` points to an `Asteroid` object. But it's generally not safe to cast this way since you can't be 100% sure at runtime if the `GameObject` comes from a function, for example

```cs
Asteroid iMightNotBeAnAsteroid = (Asteroid)CreateGameObject();

GameObject CreateGameObject() {
	return new Bullet(0, 0);
}
```

In this case, you have to check if it's the right type before casting.  There are three ways to do it. The first is to use `.GetType()` and `typeof`

```cs
GameObject asteroid = CreateGameObject();

if (asteroid.GetType() == typeof(Asteroid))
{
    Asteroid forSureAnAsteroid = (Asteroid)asteroid;
}
```

The second and third ways are through the `as` and `is` keywords

```cs
GameObject gameObject = new Asteroid(0, 0);
Asteroid? asteroid = gameObject as Asteroid;

// or

if (gameObject is Asteroid asteroid) { // asteroid variable is already useable here }
```
