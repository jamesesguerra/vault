Whereas classes allow you to define custom reference types, structs allow you to define **custom value types**. They're useful for representing small data-related concepts that don't have a lot of behavior

```cs
public struct Point {
	public float X { get; }
	public float Y { get; }

	public Point(float x, float y) {
		X = x;
		Y = y;
	}
}

Point p1 = new Point();
```

#### When should you choose structs over classes?
- If you want value semantics
- If the type is small and data-centric, with little to no behavior; you want them to be small since they're stored on the stack and will be copied
- You want value-based equality checking

### Boxing and Unboxing
C# uses a unified type system, that is, a type system where you can reference anything as `System.object`. 

**Boxing** is the process of "boxing" or putting a value type inside an object when it is assigned to an object reference, namely an `object` variable. 

```cs
int number = 5;
object boxedNumber = 5; // 5 gets "boxed" here since it's a value type being assigned to a reference type
```

**Unboxing** is the process of converting the boxed object back to its "value type state". Unlike boxing, this step must be done explicitly and must be cast to its exact original value type before it was boxed.

```cs
int number = 5;

object boxedNumber = number;

int unboxedNumber = (int)boxedNumber;
```



