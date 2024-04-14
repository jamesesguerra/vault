### Representing Data in Binary
- types are important since everything we want to represent needs a scheme for using the bits to represent them in our circuits
- you can't take bits and bytes used to represent an `int` and reinterpret them as a `string` and expect to get meaning out of it
- C# just also reuses the schemes already established in the computing world

### Integers
- there are different integer types that use varying amounts of bits and therefore make you use more / less memory depending on the type
- **common integer types**
	- `short` - uses 2 bytes or 16 bits; -32,000 to 32,000
	- `int` - uses 4 bytes or 32 bits; -2 billion to 2 billion
	- `long` - uses 8 bytes or 64 bits; -9 quintillion to 9 quintillion
- each of the common integer types have unsigned equivalents (`ushort`, `uint`, `ulong`) that exclude negative values, but are able to store twice as many positive values

### Choosing Between Types
- in modern programming, rarely does saving a byte of space matter since you have a lot to work with
- it's best to just use `int`, `uint`, `long`, and `ulong`

### Characters and Strings
- **char** - a single character represented by 2 bytes; closely related to `int`
- **string** - aggregates many characters into a sequence

### Floating-Point Types
- **floating-point types** - represent real numbers or numbers with a fractional component
- they're called floating point types because it contrasts with fixed-point types which have a fixed number of digits for the integer part and the decimal part; in floating-point types, the decimal can appear anywhere 
- the more bits you use, the more precise you can be and the more magnitudes you can represent; the more precision you have, the more you can differentiate numbers i.e. if you only 6 digits of precision, you can't differentiate between 10_000 and 10_000.0001
- **common floating-point types**
	- `float` - 4 bytes; 6-7 digits of precision
	- `double` - 8 bytes; 15-16 digits of precision
	- `decimal` - 16 bytes; 28-29 digits of precision
- `float`s and `double`s are faster, but if you need **extreme precision**, use `decimal`s
- `double`s can represent the biggest big numbers and the smallest small numbers, but aren't as precise as `decimal`s, use `decimal`s for calculations involving money

![[data-types.png]]

### Questions
1. **Does the compiler do any optimizations by using types that use less memory if the numbers in the program aren't big?**
	- I don't think so, but it does have to make adjustments when converting say a `short` into an `int` when a method only takes `int`s
2. **What is precision and magnitude in floating-point types?**
	- **Precision** is how much of a number that can be represented by a data type. If you have less precision, you won't be able to distinguish between the numbers that are obviously different since part of the digits are being truncated (assuming that the decimal part is more than 23 digits) and so they appear to be the same number. Less precision also has implications with making calculations. The classic example is adding 0.1 and 0.2, which is a fairly trivial task for us, but not for computers. Computers can't recognize recurring numbers and when they add the 2 numbers, they truncate the decimal part which exceeds 23 digits. And so the resulting number will be something 0.30000000000000004 as opposed to just 0.3. In most cases, this won't matter since it is very close enough to the point where they're virtually the same number. But in terms of money, they're still different and suddenly become awfully important. In these cases, it's best to use the `decimal` data type. On the other hand, **magnitude** is the range of numbers you can represent with a given data type.
3. **Why does a `bool` use up a whole byte if it can just use a single bit to be represented in memory?**
	- The simpler answer is because memory locations are exactly 1 byte, and therefore a computer can't reference anything less than a byte.
4. **Is there a difference using the `Convert` class and the built-in parse methods for number types?**
	- No difference you should be worried about since `Convert.ToInt32` calls `int.Parse` internally. The 1 difference you may want to be wary about is that `Convert.ToInt32` returns a 0 if the argument is null, and `int.Parse` raises an exception.
