1. Create class library project for adding unit tests
2. Install required packages:
- NUnit
- NUnit3TestAdapter
- Microsoft.NET.Test.Sdk

3. Create a new test suite (class)
```cs
public class EnumerableExtensionsTests {}
```

4. Add imports and attributes
```cs
using NUnit.Framework;

[TestFixture]
public class EnumerableExtensionsTests {
	[Test]
	public void Test1() {
	}
}
```

5. Arrange, Act, Assert
```cs
[Test]
	public void Test1() {
		var input = Enumerable.Empty<int>();
		var result = input.SumOfEvenNumbers();
		Assert.AreEqual(0, result);
	}
```

### Naming Unit Tests
The naming convention is: method name + expected behavior + scenario
Example: `SumOfEvenNumbers_ShallReturnZero_ForEmptyCollection`

### Test Cases
You can parameterize a test with different arguments (cases)

```cs
[TestCase(0)]
[TestCase(8)]
[TestCase(-6)]
public void SumOfEvenNumbers_ShallReturnValueOfTheOnlyItem_IfItIsEven(int number)
{
	var input = new int[] { number };
	var result = input.SumOfEvenNumbers();
	Assert.AreEqual(number, result, "tanga");
}
```