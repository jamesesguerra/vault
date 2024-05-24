A method to check whether 2 references are equal is the `ReferenceEquals` method. It takes 2 references and returns `true` if they point to the same object, and false if not. This method is static and cannot be overriden.

It also doesn't make sense to call them with value types since they will eventually be boxed and always return `false`.

```cs
var john = new Person("John");
var johnCopy = john;
var anotherJohn = new Person("John");

ReferenceEquals(john, johnCopy); // true
ReferenceEquals(john, anotherJohn); // false
```

### `.Equals`
The `Equals` method is one of the two key methods that originates from the `object` class. It uses the `ReferenceEquals` method under the hood. It's also a virtual method and so value types override this since it doesn't make sense to compare the equality of their references, so it overrides it with an implementation that compares their values instead.
- **value types** - compares the data
- **reference types** - compares the references

```cs
1.Equals(1); // true
```

If you aren't satisfied with the default implementation of the `Equals` method, you may override it with what makes sense for the class.

```cs
public class Person {
	...
	// the current Person object is equal to another object if it's also a Person object and if it has the same name
	public override bool Equals(object? obj) {
		return obj is Person otherPerson && otherPerson.Name == Name; 
	}
}
```

However, with this implementation of `Equals`, a value type would have to be boxed in the `obj` parameter and it would bring performance issues. One solution is to create an overload of the method that takes the value type and not the `object` type. 

```cs
public struct Point {
	...
	public bool Equals(Point otherPoint) {
		return otherPoint.X == X && otherPoint.Y == Y;
	}

	public override bool Equals(object? obj) {
		return obj is Point otherPoint && Equals(otherPoint);
	}
}
```

To make sure the overloaded `Equals` gets called in generic methods, make sure to implement the `IEquatable<T>` interface. Generic methods don't know what `T` is and so it can only call the overriden methods and not the overloaded ones.

```cs
struct Point : IEquatable<Point> {...}

static class Extensions
{
    public static bool Contains<T>(this IEnumerable<T> values, T item)
    {
        foreach (var value in values)
        {
            if (item.Equals(value)) return true; 
        }
        return false;
    }
}
```

### The `==` operator
The default behavior of this operator is:
- **value types** - not supported
- **reference types** - compares the references

The reason why you can use this operator with integers is because it has been overloaded.

### `GetHashCode`
This method is 1 of the 3 notable methods that lives on the `object` class. It returns a more-likely-than-not non-unique integer ID that describes an object. It's used in dictionaries and hash sets to get the key it will use for an object. You would wanna override this if you choose to override `Equals`

Default behavior:
- **value types** - uses the combination of its properties and their values (much like `Equals()`)
- **reference types** - uses their references