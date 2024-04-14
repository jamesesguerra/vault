### Special Number Values
- each of the 11 number types defines a couple of special values you may find useful
- all 11 define a `MaxValue` and `MinValue`
- `double` and `float` also define `PositiveInfinity` and `NegativeInfinity` as well as `NaN` which is used when a computation results in an impossible value, such as division by zero

### Division by Zero
- if you divide an int by 0, a `DivideByZeroException` will be raised that will crash your program if unhandled
- if you divide floating-point types by 0, it won't raise an exception as it will assume you're trying to divide by an infinitesimally small number, and will return `PositiveInfinity`, `NegativeInfinity`, or `NaN`, depending on the numerator

### Roundoff Errors
- a `float` cannot distinguish between 10_000 and 10_000.00001 since it only has 6 or 7 digits of precision
- all floating-point types have this problem, just at different scales
```cs
float a = 10_000f;
float b = 0.00001f;
float sum = a + b; // = just 10_000 since it can't see the decimal part
```
### The `Math` Class
- `Math.PI` and `Math.E`
- `Math.Pow(x, 2)` and `Math.Sqrt(x)`
- `Math.Min(x, y)` and `Math.Max(x, y)`
- `Math.Clamp(x, lowerBound, upperBound)` - keeps a variable in a certain range

### Questions:
1. **Why doesn't `decimal` also define a `PositiveInfinity` and `NegativeInfinity` like `float` and `double`?**
	- Because that's how it's specified in the standards.
1. **Why do I *have* to use `f` and `m` to denote `float`s and `decimal`s?**
	- The suffix determines the kind of real literal that number represents.