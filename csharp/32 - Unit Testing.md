1. Create class library project for adding unit tests
2. Install required packages:
- NUnit
- NUnit3TestAdapter
- Microsoft.NET.Test.**Sdk****

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

### Test Case Source
The constructor of the `TestCase` attribute only takes in simple types, and single-dimensions arrays of simple types. If you wish to use custom types, you have to use the `TestCaseSource` attribute instead.

```cs
[TestCaseSource(nameof(GetSumOfEvenNumbersTestCases))]
public void SumOfEvenNumbers_ShallReturnNonZeroResult_IfEvenNumbersArePresent(
	IEnumerable<int> input, int expected
)
{
	var result = input.SumOfEvenNumbers();

	ClassicAssert.AreEqual(expected, result);
}

private static IEnumerable<object> GetSumOfEvenNumbersTestCases()
{
	return new[]
	{
		new object[] { new List<int> { 100, 200, 1 }, 300 }
	};
}
```

### Testing Exceptions Being Thrown
Aside from testing if two values are equal, you can check if an exception will be thrown from a method call using `Assert.IsThrows<ExceptionType>`

```cs
[Test]
public void SumOfEvenNumbers_ShallThrowException_IfInputIsNull()
{
	IEnumerable<int>? input = null;

	Assert.Throws<ArgumentNullException>(() =>
	{
		input!.SumOfEvenNumbers();
	});
}
```

### Mocks
A mock is an object that pretends to be another object. It has the same interface as the object being mocked, so it can be used in its place.

The object pretends it knows how to do something. This is useful in cases where you wouldn't want to actually do the thing and just mock the behavior. For example, building a sports watch that reads the temperature from an object that implements the `IThermometer` interface. You wouldn't wanna have to go to the Sahara/Antartica to test whether it works. You can just use a mock thermometer that pretends to read the temperature correctly. 

1. Install moq
2. Create the mock object
```cs
var mockDatabaseConnection = new Mock<IDatabaseConnection>();

var personalDataReader = new PersonalDataReader(mockDatabaseConnection.Object);
```

3. Set up the object so that it behaves how you want it to
```cs
mockDatabaseConnection
	.Setup(mock => mock.GetById(5))
	.Returns(new Person(5, "John", "Smith"));
```