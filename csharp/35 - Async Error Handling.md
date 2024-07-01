### Thread and Task Exceptions
Exceptions thrown by one thread can, by default, only be caught on the same thread. If another thread throws an exception, you cannot catch and handle it from the main thread, and so the app will crash

But if a task throws a thread, it will not be caught, but it won't crash the app. Only if you wait for a task with `task.Wait()` will its exceptions be propagated to the thread that started the task (the main thread). The exception thrown from the task will be wrapped in an `AggregateException` since more than 1 exception can be thrown within a task. 

```cs
Task task;
try
{
    task = Task.Run(() => MethodThrowingException());
    task.Wait();
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}
```

So you wouldn't block the main thread when you wait for the task just so you can handle the exception, you use `ContinueWith` together with the `TaskContinuationOptions` enum. This is similar to a `.catch()` in JavaScript promises.
```cs
var task = Task
            .Run(() => MethodThrowingException())
            .ContinueWith(faultedTask =>
            {
                Console.WriteLine("Exception caught " + faultedTask.Exception.Message);
            }, TaskContinuationOptions.OnlyOnFaulted);
```

You can handle different types of exceptions using the `AggregateException`'s `Handle` method:
```cs
var task = Task
            .Run(() => Divide(2, null))
            .ContinueWith(faultedTask =>
            {
                faultedTask.Exception.Handle(ex => {
	                if (ex is ArgumentNullException) {
		                Console.WriteLine("Arguments can't be null");
		                return true;
	                } else if (ex is DivideByZeroException) {
		                Console.WriteLine("Can't divide by zero");
		                return true; 
	                }
	                Console.WriteLine("Unexpected exception type");
	                return false;
                });
            }, TaskContinuationOptions.OnlyOnFaulted);
```