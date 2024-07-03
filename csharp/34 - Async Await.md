Code with tasks and continuations looks awkward with lambdas, so we use `async` and `await` keywords

```cs
var task = RunHeavyOperation();

Console.WriteLine("Continuing Program");
Console.ReadKey();

static Task RunHeavyOperation()
{
	return Task
			.Run(() => HeavyOperation())
			.ContinueWith(task => Console.WriteLine("The result is " + task.Result));
}   

static string HeavyOperation()
{
    Console.WriteLine("Starting heavy operation...");
    Thread.Sleep(2000);
    return "3000";
}
```

### await
- the `await` keyword tells the code to start the method being `await`ed, but not to wait there and move back to the method that called the awaited method if it's not yet done
- it can only be used with tasks of any type and custom await-able types
- it also unwraps the `Task` result of result being returned by the awaited method
### async
- the `async` enables the use of the `await` keyword within a method
- they always return tasks of any type, so even if you don't return anything from an async method, it will return a non-generic one behind the scenes; if you do return a value, it will wrap it with a `Task`

Async/await doesn't spawn new threads, but continuations (the code after the await) use a different thread from the thread pool to run them

```cs
var task = RunHeavyOperation();

Console.WriteLine("Continuing Program");
Console.ReadKey();

static async Task RunHeavyOperation()
{
    string result = await HeavyOperation();
    Console.WriteLine("the result is " + result);
}

static async Task<string> HeavyOperation()
{
    Console.WriteLine("Starting heavy operation...");
    await Task.Delay(2000);
    return "3000";
}
```


TLDR
If you use the `await` keyword, you will go back to the caller method while waiting for the completion of the awaited task. But the caller method can also await something, and you'll go back even further. That way, the program can continue while waiting for some tasks finish.