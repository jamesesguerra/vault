> Unit testing frameworks help developers write tests more quickly with a known set of APIs, execute those tests automatically, and review the results of those tests easily

#### Steps for writing a unit test
1. Add a new class library project that will contain the unit tests. Name it `[ProjectUnderTest].UnitTests`
2. For a class in `ProjectUnderTest`, create a class with the name `[ClassName]Tests`.
3. For each unit of work, create a test method with the following name: `[UnitOfWorkName]_[ScenarioUnderTest]_[ExpectedBehavior]`. The unit of work name can be as simple as a method name or more abstract if it encompasses multiple methods.

#### NUnit Attributes
NUnit uses an attribute scheme to recognize and load tests. It needs two attributes to know what to run:
- `[TestFixture]` - denotes a class that holds automated unit tests (optional now)
- `[Test]` - denotes an automated test to be invoked

#### Writing a unit test
A unit test comprises three main actions:
1. **Arrange** objects, creating and setting them up as necessary
2. **Act** on an object
3. **Assert** that something is expected

```csharp
[Test]  
public void IsValidLogFileName_BadExtension_ReturnsFalse()  
{  
    // arrange  
    var analyzer = new LogAnalyzer();  
      
    // act  
    var result = analyzer.IsValidLogFileName("filename.foo");  
      
    // assert  
    Assert.That(result, Is.False);  
}
```

It's a good idea to have each step in their own line, separated by new lines so that it's much easier to read.

#### Parameterized Tests
Some tests might seem redundant—where only the input changes, but the assertions remain the same. In such cases, it’s better to refactor them into **parameterized tests** for improved maintainability.

To do this, use the `[TestCase]` attribute. It allows you to pass arguments that match the method’s parameter list. The best part is that you can apply multiple `[TestCase]` attributes to a single test method. Just remember to give these methods more **generic names** to reflect their reusable nature.

```csharp
[TestCase("filename.foo")]  
public void IsValidLogFileName_InvalidExtensions_ReturnsFalse(string fileName)  
{  
    var analyzer = new LogAnalyzer();  
      
    var result = analyzer.IsValidLogFileName(fileName);  
      
    Assert.That(result, Is.False);  
}  
  
[TestCase("filename.slf")]  
[TestCase("filename.SLF")]  
public void IsValidLogFileName_ValidExtensions_ReturnsTrue(string fileName)  
{  
    var analyzer = new LogAnalyzer();  
      
    var result = analyzer.IsValidLogFileName(fileName);  
      
    Assert.That(result, Is.True);  
}
```

You might even think of going one step further by making it even more generic by fusing the methods of the happy and negative paths. But know that this might be taking the step *too* far.

```csharp
[TestCase("filename.foo", false)]  
[TestCase("filename.slf", true)]  
[TestCase("filename.SLF", true)]  
public void IsValidLogFileName_VariousExtensions_ChecksThem(string fileName, bool expected)  
{  
    var analyzer = new LogAnalyzer();  
      
    var result = analyzer.IsValidLogFileName(fileName);  
      
    Assert.That(result, Is.EqualTo(expected));  
}
```

#### Setup and Teardown 
For unit tests, it might be a good idea sometimes that any leftover data or instances from previous tests are destroyed and that the state for the new test is recreated as if no tests have been run before.

- `[SetUp]` - can be put on a method; it causes NUnit to run that setup method each time it runs any of the tests in your class
- `[TearDown]` - denotes a method to be executed once after each test in your class has executed

Think of them as constructors and destructors for each test in the test class. They're normally used to create new instances of the CUT to save a few lines of code creating a new instance in each test class. But they aren't such a good idea. Because then readers of the class would have to look in 2 places back and forth to understand the full picture.

```csharp
private LogAnalyzer analyzer = null;  
  
[SetUp]  
public void SetUp()  
{  
    analyzer = new();  
}  
  
[TearDown]  
public void TearDown()  
{  
    analyzer = null;  
}  
  
[TestCase("filename.foo")]  
public void IsValidLogFileName_InvalidExtensions_ReturnsFalse(string fileName)  
{  
    var result = analyzer.IsValidLogFileName(fileName);  
      
    Assert.That(result, Is.False);  
}
```

There are also corresponding setup and teardown methods for the entire test class with the `[TestFixtureSetUp]` and `[TestFixtureTearDown]` attributes.

> It's almost never a good idea to run teardown methods. This is because it's either an indication that you're doing integration tests if you need to clean up the DB after each test. The only time it makes sense to use them in unit tests is to "reset" the state of a static variable or singleton in memory between tests.

##### Factory Methods
Sometimes, it’s better to use factory methods instead of setup methods to initialize the instances under test. Factory methods offer greater flexibility, especially when dealing with constructors — you can easily create instances using either parameterless or parameterized constructors. In contrast, setup methods always create a new instance before each test, even when a particular test doesn’t need one.

```csharp
[TestCase("filename.foo")]  
public void IsValidLogFileName_InvalidExtensions_ReturnsFalse(string fileName)  
{  
    var analyzer = MakeAnalyzer();  
      
    var result = analyzer.IsValidLogFileName(fileName);  
      
    Assert.That(result, Is.False);  
}

private static LogAnalyzer MakeAnalyzer()  
{  
    return new LogAnalyzer();  
}
```

#### Checking for Expected Exceptions
To check if a specific scenario *should* throw a certain type of exception and message, use `Assert.Throws<T>()`

```csharp
[TestCase]  
public void IsValidLogFileName_EmptyFileName_ThrowsException()  
{  
    var analyzer = MakeAnalyzer();  
  
    var ex = Assert.Throws<ArgumentException>(() => analyzer.IsValidLogFileName(string.Empty));  
  
    Assert.That(ex.Message, Is.EqualTo("filename has to be provided"));  
}
```

It might also be a good idea to check for `Does.Contain()` instead of `Is.EqualTo()` since strings change a lot

```csharp
Assert.That(ex.Message, Does.Contain("filename has to be provided"));
```

#### Ignoring Tests
To ignore test, use the `[Ignore]` attribute.

```csharp
[Ignore("there is a problem with this test")]
```

#### Categories
You can also categorize the test methods, such as grouping them by slow/fast tests or positive/negative outcomes:

```csharp
[Category("Positive")]
```

You can then filter out tests that don't match a specific category when running `dotnet test`:

```sh
dotnet test --filter "Category=Positive"
```

#### Testing System State Changes
The things we've been testing were simple return values. However, you can also test state changes (either of the CUT or its dependencies).

For a test class like this, we can test the `WasLastFileNameValid` property, whether it gets changed after calling `IsValidLogFileName`

```csharp
public class LogAnalyzer  
{  
	public bool WasLastFileNameValid { get; set; }
      
    public bool IsValidLogFileName(string fileName)  
    {  
        WasLastFileNameValid = false;  
          
        if (string.IsNullOrEmpty(fileName))  
        {  
            throw new ArgumentException("filename has to be provided");  
        }  
          
        if (!fileName.EndsWith(".SLF", StringComparison.OrdinalIgnoreCase))  
        {  
            return false;  
        }  
  
        WasLastFileNameValid = true;  
        return true;  
    }  
}
```

First, you have to identify the unit of work you're testing, whether if it's in a property or a method.

```csharp
[TestCase("filename.foo", false)]  
[TestCase("filename.slf", true)]  
public void IsValidLogFileName_WhenCalled_ChangesLastFileNameValid(string fileName, bool expected)  
{  
    var analyzer = MakeAnalyzer();  
  
    analyzer.IsValidLogFileName(fileName);  
      
    Assert.That(analyzer.WasLastFileNameValid, Is.EqualTo(expected));  
}
```

