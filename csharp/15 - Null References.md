A **null reference** is a variable that holds a reference to `null` or the absence of a value / object.

They are helpful when representing the possibility for there to be no data available for something e.g. a vehicle having a `Character _driver` field -- in which case the driver's seat being empty may be represented by having the `_driver` field point to a null reference.

They are without consequences as trying to access members of a null reference will crash your program.
```cs
string response = Console.ReadLine();
Console.WriteLine(response.Length); // if response is pointing to a null reference, the program will crash when we access its Length field
```
