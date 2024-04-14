### Public and Private Accessibility Modifiers
Every member of a class has an accessibility level that determines where they're accessible from. The `public` keyword makes the member usable anywhere. Whereas the `private` keyword makes it accessible from within the class only.

```cs
Rectangle r = new(5, 5);
r._area = 2000; // can't assign invalid values !
Console.WriteLine(r._area) // cant do this anymore !

class Rectangle {
	private int _width;
	private int _height;
	private int _area;

	public Rectangle(int width, int height) {
		_width = width;
		_height = height;
		_area = _width * _height;
	}
}
```

Each object is a gatekeeper of their own data. They can enforce rules about their data and not allow invalid values from the outside world. 

Another access modifier is `internal`. The main difference between `internal` and `public` is that `internal` types can only be used inside the project it's defined in, whereas `public` types can be accessed anywhere, including other projects.

### Guidelines on using `private` and `public`
1. A class should always protect its data and so fields should almost always be `private`
2. Things should always be as inaccessible as possible while still allowing the class to fulfill its role in the system

### Questions:
1. **How do information hiding and abstraction go together?**
	- They go together because when we want to hide access to a class's internal data / inner workings, we create a public interface by which we can interact with that data. We aren't accessing the data directly and so we cannot depend on how it's implemented. Abstraction *is* that public interface. So instead of thinking of a class in terms of its inner data, we only need to think of interacting / dealing with it in terms of its public interface.