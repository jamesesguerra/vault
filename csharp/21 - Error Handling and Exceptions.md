3 types of errors
- compilation errors
- runtime errors -> the target of error handling
- logic errors

We want to handle runtime errors so that users will know what's happening and can report problems with meaningful descriptions; we also want to handle them so other developers can debug better. They disrupt the normal flow of execution and cause the stack to collapse, but we can track the order of functions being called by looking at each stack frame in the error message, where each frame on top is called after the one below.

We can place runtime-prone code in`try` blocks and *"catch"* them in `catch` blocks

```cs
string response = Console.ReadLine()1;

try {
	int x = int.Parse(response);
	Console.WriteLine("Successfully parsed!");
} catch {
	Console.WriteLine("An error occurred. Log me so you can investigate later on");
} finally {
	Console.WriteLine("Im executed no matter what. Clean up resources used in try block (such as opening a database connection)");
}
```

Error-handling code shouldn't always be the go-to solution as it clutters code and mixes error-handling logic with the business rules. Sometimes, it should be avoided where possible (by using a `int.TryParse()` in this case). We should only use local try-catch blocks if we can actually foresee that a line of code can cause an error, and if we can actually **handle it reasonably and correctly**. It wouldn't make sense to wrap every line of code with them.

### The `Exception` object
We can access the `Exception` object in the catch block by catching it in the parameters. It gives us insight by showing us the error message.

```cs
try {...}
catch (Exception ex) {
	Console.WriteLine(ex.Message);
}
```

Catching the `Exception` object is something we want to avoid since it's too general. We only want to catch specific exceptions. We can also choose to catch multiple exceptions. Where each exception goes into its own "exception bucket" where we can handle them accordingly. Handling the most specific exceptions should be on top since only 1 catch block will run and we want the most specific one to be able to catch the exception first.

```cs
try {
	int number = ParseStringToInt(stringToBeParsed);
	var result = 10 / number;
	Console.WriteLine($"10 divided by {number} is {result}");
} catch (DivideByZeroException ex) {
	Console.WriteLine($"Division by zero! Error message: {ex.Message}");
} catch(FormatException ex) {
	Console.WriteLine("Input string is not parsable to int. Error message: " + ex.Message);
} catch (Exception ex) {
	Console.WriteLine("Unexpected error occured...");
} finally {
	Console.WriteLine("Cleaning up resources...");
}
```

Although, there are some situations where catching the base `Exception` object is useful. For example, if we're dealing with an interface and don't know exactly what object  will be plugged into it, we don't know the types of exceptions we should catch / handle. In these cases, it's ok since we aren't handling it and simply logging it, so it also won't be mishandled in any way.

```cs
try {
	return _peopleRepository.Read(personId);
} catch (Exception ex) {
	_logger.Log(ex);
	throw;
}
```

### Throwing exceptions explicitly
We can choose to throw our own exceptions when it makes sense by using the `throw` keyword and create a new `Exception` object. It makes sense to throw our own exceptions if we can't handle invalid input reasonably, or if it's the callees fault.

```cs
int GetFirstElement(IEnumerable<int> numbers) {
	foreach (var number in numbers) {
		return number;
	}
	throw new Exception("The collection cannot be empty");
}
```

We want to throw specific exception types as much as possible. There are many built-in exception types at our disposal

```cs
class Person {
	...
	public Person(string name, int yearOfBirth) {
		if (name == string.Empty) {
			throw new ArgumentNullException("Name cannot be empty");
		}

		if (yearOfBirth < 1900 || yearOfBirth > DateTime.Now.Year) {
			throw new ArgumentOutOfRangeException("Year of birth must be greater than 1900 and < current year");
		}
		
		Name = name;
		YearOfBirth = yearOfBirth;
	}
}
```

We can also catch errors and try to make them more specific by throwing our own

```cs
try {...}
catch (NullReferenceException ex) { // says something is null but doesnt say which
	throw new ArgumentNullException("Argument can't be null", ex); // pass the exception object to a more specific 1
}
```

![[exception-types.png]]

### `throw` vs `throw ex`

When there is an exception, you can choose to handle it in the current catch block, or you can choose to throw a new exception, call `throw ex`, or `throw` to let the callee functions higher up choose what to do with it. You typically want to rethrow exceptions if you want to log the exception without handling it, since you may know for sure what the problem is before rethrowing, and can provide more context than if you're catching it in a global try-catch block.

The difference between `throw` and `throw ex` is that `throw ex` doesn't preserve the stack trace up until the point where the exception was first thrown. All data before the point where it was re-thrown with `throw ex` will be lost since it resets it. On the other hand, `throw` preserves it since the stack trace will point to the method that caused it in the first place. 

It's important to note that the stack trace will show **up until the line where the unhandled exception was originally thrown**. 

### Inner exceptions
If you choose to throw a new `Exception` object after catching one, and let the callee function handle it, the callee function can still access the original exception by accessing the new `Exception` object's `InnerException` property if you pass it in when you make the new exception. The inner exception contains an exception that may have been the underlying cause.

```cs
try {
	int x = 10 / 0;
} catch (DivideByZeroException ex) {
	throw new InvalidOperationException("Can't divide by zero", ex); // pass the original exception to the new one
}
```

### Exception Filters
You can use exception filters to handle the same exception type differently for different properties of the `ex` object, or some other condition.

```cs
try {
	var dataFromWeb = SendHttpRequest(url);
} catch(HttpRequestException ex) : when(ex.Message == "403"){
	_logger.Log("Forbidden");
	throw;
} catch(HttpRequestException ex) : when(ex.Message == "404") {
	_logger.Log("Resource not found");
	throw;
}
```

### Exception Guidelines
- Exceptions should be used for **exceptional behavior**, and not for controlling the flow of the program
- Sometimes a simple if-check can help you avoid using exceptions, as some exceptions are most likely the developers fault (for not checking)
- Catch exceptions when you have something meaningful to do with them such as:
	- retrying an operation
	- adapting a method (returning null if it makes sense)
	- logging with meaningful context
	- rethrowing a more specific and appropriate exception and/or enriching the exception message so that when it's caught at the global try-catch, it has more meaning
- Have a bias for handling exceptions instead of letting them go, but code readability is also important
- Only handle what you can fix ie if you have meaningful resolution steps for the problem
- Don't eat exceptions (`catch(Exception ex) {}`)

![[catching-exceptions.png]]