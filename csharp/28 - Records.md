Records are reference types that have value-based equality, with built in `IEquatable` method definitions for the `Equals(T)` and `Equals(object?)` methods, as well as equality and inequality operators. It also has overrides for `GetHashCode` and `ToString`.

Positional records are immutable

```cs
public record Point(int X, int Y);
```

### Record Structs
Records structs are different since they're value types and mutable by default.

```cs
public record struct Rectangle(int A, int B;

// you can also make them immutable

public readonly record struct Rectangle(int A, int B);
```