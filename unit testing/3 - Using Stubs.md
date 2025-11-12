> You use stubs when your test can't control what a dependency returns to your code under test or how it behaves.

#### Introduction to stubs
An **external dependency** is an object in your system that your code under test interacts with and over which you have no control. Commons examples are filesystems, threads, memory, time, and so on.

In programming, you use stubs to get around the problem of external dependencies. A **stub** is a controllable replacement for an existing dependency in the system. By using a stub, you can test your code without dealing with the dependency directly.

The main difference between stubs and mocks is that you assert against the mock object, whereas you do *not* assert against a stub.

#### Example: a filesystem dependency
The main reason why you'd wanna use stubs is because testing against an external dependency might break the test even though the code's logic is perfectly valid.

For example, if a class under test's behavior depends on the file system and you test that class, it now becomes an integration test and comes with all the problems associated with it.

Here are the steps to stub any external dependency:
1. **Find the interface:** Identify the external dependency your unit interacts with.
2. **Add indirection:** Put a layer (class or interface) between your unit and the real dependency so your unit doesn’t call it directly.
3. **Replace with a stub:** In tests, swap the real dependency with a controllable fake so you can test your unit in isolation.

In this case, we can create a layer of indirection by not having our CUT interact with the filesystem directly. Instead, we can create a separate interface (such as `IExtensionManager`). Then, we replace the instance of this class with a fake stub class we can control in the test methods.

So now, the `LogAnalyzer` doesn't depend directly on the filesystem, it depends on a class that implements `IExtensionManager`, which then depends on the filesystem.

```csharp
public class LogAnalyzer  
{  
    private IExtensionManager _fileExtensionManager;  
  
    public LogAnalyzer(IExtensionManager fileExtensionManager)  
    {  
        _fileExtensionManager = fileExtensionManager;  
    }  
  
    public bool IsValidLogFileName(string fileName)  
    {  
        return _fileExtensionManager.IsValid(fileName);  
    }  
}
```

Now in the test method, we can use a fake extension manager that we can control using a property. And use constructor injection to inject the fake extension manager in our test.

```csharp
[Test]  
public void IsValidFileName_NameSupportedExtension_ReturnsTrue()  
{  
    var fakeExtensionManager = new FakeExtensionManager();  
    fakeExtensionManager.WillBeValid = true;  
    var logAnalyzer = new LogAnalyzer(fakeExtensionManager);  
  
    var result = logAnalyzer.IsValidLogFileName("short.ext");  
      
    Assert.That(result, Is.True);  
}

public class FakeExtensionManager : IExtensionManager  
{  
    public bool WillBeValid { get; set; }  
  
    public bool IsValid(string fileName)  
    {  
        return WillBeValid;  
    }  
}
```

> Alternatives to using constructor injection include property injection and factory classes. Property injection is suitable when some dependencies are optional. While using factory classes to control the dependencies is suitable when you want to centralize object creation logic.

##### Simulating exceptions from fakes
You can also simulate exceptions by having an `Exception` property on the stub that you can control.

```csharp
public class FakeExtensionManager : IExtensionManager  
{  
    public Exception ExceptionThrown { get; set; }  
    public bool WillBeValid { get; set; }  
  
    public bool IsValid(string fileName)  
    {  
		if (ExceptionThrown is not null)  
		{  
			throw ExceptionThrown;  
		}  
	
		return WillBeValid;  
    }  
}
```

