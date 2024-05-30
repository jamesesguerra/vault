### Characters

```cs
var isUppercase = char.IsUpper('A'); // true
var isLetter = char.IsLetter('4'); // false
var isDigit = char.IsDigit('a'); // false
var isWhiteSpace = char.IsWhiteSpace('\n'); // true
var uppercaseA = char.ToUpper('a');
```

#### Character Encoding
Because `char` are `int`s under the hood, who decides the mapping from `int` to `char`? This is where character encoding comes in, and there's a standard for using characters in computing. Unicode is the organization that does this. C# characters use UTF-16 (16-bit Unicode transformation format). 

You can check the encoding on your computer using:
```cs
Console.WriteLine(Console.OutputEncoding.EncodingName);
```

### Strings
Strings use an array of `char` types underneath the hood. The reason why we can seemingly modify them even though they're arrays is because strings are immutable and a new string is created when we modify a string.

```cs
string text = "abc";
text += "d"; // a new string "abcd"
```


#### String Builder
Because strings are immutable, operations that repeatedly modify a string will be costly since a new string will be created for each modification, and each string that isn't used anymore will have to be disposed by GC. So an operation like this would be costly:
```cs
string text = "aaa";

for (int i = 0; i < 1000000; i++) {
	text += "a";	
}
```

This is where the `StringBuilder` class comes in. The `StringBuilder` class can add and remove pieces from a string efficiently since it has a buffer. The buffer allows for expansion of the string without having to create a new one every time it's modified. It'll only create a new buffer when the old buffer is full, and copy the contents of the previous buffer into the new one.
```cs
var text = new StringBuiler();
text.Append("aaa");

for (int i = 0; i < 1000000; i++ {
	text.Append("a");
})

text.ToString();
```

#### String Interning
Strings employ the flyweight design pattern because they minimizes the memory usage of an application by sharing as much data as possible between similar objects. Even though strings are reference types, strings that have the same content actually point to the same reference in the heap to save memory.
```cs
var a = "text";
var b = "text";

var isEqual = ReferenceEquals(a, b); // true
```

