**Collections** are types that provide a way to store and manipulate groups of related objects.

### IEnumerable
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