You can also overload operators for types that don't support them.

```cs
public struct Point {
	...
	public static Point operator + (Point a, Point b)
    {
        return new Point(a.X + b.X, a.Y + b.Y);
    }
}
```

You can also overload the implicit conversion operator 

```cs
public struct Point {
	...
	public static implicit operator Point(Tuple<int, int> tuple) {
		new Point(tuple.Item1, tuple.Item1);
	}
}
```