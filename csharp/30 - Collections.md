**Collections** are types that provide a way to store and manipulate groups of related objects.

### `IEnumerable`
The `IEnumerable` interface is a must when creating your own collections as it provides a way to iterate over the collection. It doesn't provide methods to modify the collection. Instead, it merely provides a `GetEnumerator` method which returns an object that points to the currently iterated item in the collection.

The `GetEnumerator` method returns an object that implements the `IEnumerator` interface. This object has methods like `MoveNext` and `Reset`, and keeps track of the current object with its `Current` property. The `foreach` loop is just syntactic sugar for these methods.

```cs
var words = new string[] { "aaa", "bbb", "ccc" };

IEnumerator enumerator = words.GetEnumerator();
object currentWord;

while (enumerator.MoveNext()) {
	currentWord = enumerator.Current;
	Console.WriteLine(currentWord);
}
```

### Creating Custom Collections
What can you do when creating custom collections?
- Implement `IEnumerable` and create a new type that implements `IEnumerator` so that you can iterate over the collection in a `foreach` loop
- Create custom indexers using `public object this[int index]`
- Add collection initializers like `new CustomCollection { "a", "b" }` by adding a public `Add` method to our custom collection as well as a parameter-less constructor


### `ICollection` and `IList`
The `ICollection` interface contains more methods such as `CopyTo` to copy items from one collection to another:

```cs
var numbers = new List<int>() { 1, 2, 3, 4, 5 };
var array = new int[10];
numbers.CopyTo(array, 2); // copy the items from numbers to array starting at index 2
```

The `IList` interface extends `ICollection` and adds more required methods such as an indexer, `IndexOf`, `Insert`, and `RemoveAt`

### Read-only Collections
You can make a collection seem like it's readonly by using the `IEnumerable` interface since it has no methods for adding or removing elements. However, one can still cast it to a list, or some other collection that implements the `ICollection` interface to modify it. 

```cs
var planets = new ReadPlanets(); // returns an IEnumerable of planets
var asList = (List<string>)planets;
asList.Clear(); // clears the same reference as the planets variable
```

You can prevent any modification to the collection by using the `ReadOnlyCollection` type, which is like a read-only list.
```cs
IEnumerable<string> ReadPlanets()
{
    var planets = new List<string>
    {
        "Alderaan", "Coruscant", "Bespin"
    };

    return new ReadOnlyCollection(planets);
}
```

### More Useful Methods / Performance Optimizations
You can generate a list of integers using the `Enumerable.Range` method
```cs
var numbers = Enumerable.Range(0, 100).ToArray();
```

If you wanna make a `List` based on another collection, you can pass it to the `List`'s constructor instead of adding them manually for a performance boost. But if you already have a list created and you want to add a lot of items to it at once, you can use the `AddRange` method.
```cs
var numberList = new List<int>(numbers);
numberList.AddRange(anotherCollection);
```

And if you know the size of the list upfront, it's better to pass the size to the constructor to prevent resizing
```cs
var numberList = new List<int>(100);
```

As well, by default, `List`'s don't trim the excess slots of the internal array by itself as it assumes that if the collection needed a lot of space, it might need that much space also in the future. It does this to prevent resizing. But if you know better and you're sure it won't need to be resized that much anymore, you can trim the excess memory slots
```cs
numbers.TrimExcess();
```

### HashSet
Use a `HashSet` if you want fast key lookups (much like a dictionary without a value) and if you want to maintain a collection with no duplicates
```cs
var validWords = new HashSet<string>();
```

### Queues
See also priority queues
```cs
var queue = new Queue<string>();
queue.Enqueue("a");
var first = queue.Dequeue(); // return and remove
var second = queue.Peek(); // return only
```

### Stacks
```cs
var stack = new Stack<string>();
queue.Push("a");
var first = queue.Pop(); // return and remove
var second = queue.Peek(); // return only
```

---

### The `yield` keyword
There might be collections that take up a lot of resources, but our code would only need to access a small part of it either once or at a time. This is a waste of resources, and we need a way to access just a small part of it at a time.

```cs
var skipAndTakeSome = GenerateEvenNumbers().Skip(5).Take(10);
var justTheFirst = GenerateEvenNumbers().First();

IEnumerable<int> GenerateEvenNumbers()
{
    var result = new List<int>();
    for (int i = 0; i < int.MaxValue; i += 2)
    {
        result.Add(i);
    }
    return result;
}
```

You can instead use the `yield` statement to return an iterator. An **iterator** is an object that can produce a sequence of items. This means that it will only produce a value if and when it's asked for it i.e. when it's being iterated over by a `foreach` loop.

Iterator methods must return `IEnumerable` and must have a `yield` statement inside.

```cs
IEnumerable<int> GenerateEvenNumbers()
{
	// method to generate items in the sequence
    for (int i = 0; i < int.MaxValue; i += 2)
    {
        Console.WriteLine($"Yielding: {i}");
        yield return i;
    }
}
```