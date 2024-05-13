Generics allow your custom types and methods to be parameterized by other types in order for them to be more flexible by being able to work with any type.

Use case: making your own list type out of arrays. If you make `SimpleList` class that allows you to add, remove, and get integers, you cannot use that same class for strings, floats, or other types. You have to copy paste code with the same logic just to make a class to work with another type. 

But by using generics, you can pass in the type when the object is created, so it'll adjust itself according to that type.

```cs
var numbers = new SimpleList<int>();
var words = new SimpleList<string>();

class SimpleList<T> {
	public void Add(T item) {...}
	public void Remove(int index) {...}
	public T GetAtIndex(int index) {...}
}
```

### Generic methods
You can also parameterize methods with types so that they'll work with any type

```cs
var numbers = new List<int>() { 1, 2, 3 };
numbers.AddToFront(2); // no need to parameterize as compiler can infer from list and argument type
numbers.AddToFront("string"); // compiler is confused which type is at fault
numbers.AddToFront<int>("string"); // parameterizing helps the compiler know that the argument type is at fault

static class ListExtensions {
	public static void AddToFront<T>(this List<T> list, T item) {
		list.Insert(0, item);
	}
}
```


### Type Constraints
You can limit the type of the type parameter of methods, since not all types can be worked with in the same way (you can create a new int with a parameterless constructor, but not all types can be created this way since some don't have parameterless ones)

By using type constraints, you reduce the generic-ness of your generic classes and methods, but you can do more with the parameterized types

```cs
IEnumerable<T> CreateCollectionOfRandomLength<T>(int maxLength) where T : new() {
	...
	result.Add(new T()); // <- can't create new objects with types that dont have parameterless constructors, so we limit them using constraints
}
```