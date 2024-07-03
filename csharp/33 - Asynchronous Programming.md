### Processes and Threads
A **process** is an executing instance of a program that contains its own memory space and resources, operating independently of other processes.

A **thread** is an execution unit within a process. A thread is a worker that is given some job to execute. Multiple threads have their own private stack but share the same heap.

| Process                      | Thread                                     |
| ---------------------------- | ------------------------------------------ |
| A container for threads      | Contained within a process                 |
| Can contain multiple threads | Belongs to a single process                |
| Heavyweight                  | Lightweight                                |
| Mostly isolated              | Shares some data with other threads (heap) |

### Asynchrony vs Multithreading
**Multithreading** is the capability of a program to execute multiple threads, allowing for efficient utilization of the system's resources.

**Asynchrony** refers to the occurrence of events independent of the main program flow. They're actions executed in a non-blocking way, allowing the main program flow to continue processing. So there are a graph of tasks where some tasks depend on the result of others, as each task completes it invokes the code that schedules the next task that can run, given the results of the just-completed task.

**Example** - cooking eggs and toast
- **synchronous** - you first make the toast and wait for it to finish, after, you make the eggs
- **asynchronous, single-threaded** - you make the toast and set a timer for when it'll finish, you do the same for the eggs
- **asynchronous, multi-threaded** - you hire two workers, 1 to make the toast and 1 to make the eggs; you make sure they're synchronized since they share the same resources, and you have to pay them

Many tasks aren't CPU-bound, so you just need 1 thread and register a listener for asynchronous tasks that are IO-bound. You just wait for it to arrive and do something else in the meantime. Once it arrives, you schedule the continuation of the completed task as your next thing on your to-do list. 

But for CPU-bound tasks, you hire as many threads as there are cores, assign one task to each thread, and have each core do nothing else but compute the result as quickly as possible. 

### Threads and Cores
The difference between a thread and a core is that cores are the workers, while threads are the tasks they perform. 

Foreground threads (the main thread) keep the app running, while background threads don't.

You can view the number of cores in your machine using
```cs
Environment.ProcessorCount
```

You can view the ID of the main thread with
```cs
Thread.CurrentThread.ManagedThreadId;
```

You can start new threads with
```cs
Thread thread1 = new Thread(() => PrintPluses(30));
Thread thread2 = new Thread(() => PrintMinuses(30));

thread1.Start();
thread2.Start();
```

#### Thread pools
Creating and starting new threads takes a while and consumes memory, so doing this for 100 threads will be costly

It's better to use a pool of threads where each thread can be assigned a task and then put back into the pool to be reused by other tasks. This makes it so that the price of creating a thread will only be paid once.

```cs
ThreadPool.QueueUserWorkItem(PrintPluses);
```

### Task Parallel Library (TPL)
Working with threads and thread pools are at a low level of abstraction. You should use a higher-level with tasks. Tasks represent a unit of work that can be executed **asynchronously** on a separate thread. It resembles a thread work item but on a higher level of abstraction.  

They allow for more efficient use of resources since they use thread pools behind the scenes. It also has a rich set of methods allowing you to wait for them to be finished, cancel a running task, execute something after completion, and handling exceptions.

#### Creating and Running New Tasks
Creating new tasks and starting them
```cs
var task1 =  new Task(() => PrintPluses(30));
var task2 =  new Task(() => PrintMinuses(30));

task1.Start();
task2.Start();
```

Shorthand is just running them immediately
```cs
var task1 = Task.Run(() => PrintPluses(30));
var task2 = Task.Run(() => PrintMinuses(30));
```

Returning a result from a task. In this case, the task can produce an `int`, but it's not an `int` yet, so it's a `Task<int>`
```cs
Task<int> taskWithResult = Task.Run(() => CalculateLength("hello world"));

static int CalculateLength(string input) {
	Thread.Sleep(2000);
	return input.Length;
}
```

#### Retrieving the Value of a Task
To retrieve the value of a task that returns a result, you access its `Result` property
```cs
Console.WriteLine(taskWithResult.Result);
```

However, getting the result like this is a blocking operation which freezes the application until the result is produced.  If you want to mimic this behavior with a task that doesn't return anything, use the `task.Wait()` method
```cs
var task = Task.Run(() => {
	Thread.Sleep(1000);
	Console.WriteLine("Task is finished");
});

task.Wait();
Console.WriteLine("After the task...");
```

You can also wait for multiple tasks to be completed before continuing with the program flow
```cs
var task1 = Task.Run(() =>
{
    Thread.Sleep(1000);
    Console.WriteLine("Task1 is finished");
});

var task2 = Task.Run(() =>
{
    Thread.Sleep(3000);
    Console.WriteLine("Task2 is finished...");
});

Task.WaitAll(task1, task2);
Console.WriteLine("After the task...");
```

You can also set up a quick retrieval of a result from a task with `Task.FromResult()`
```cs
Task<int> task = Task.FromResult(10);
Console.WriteLine(task.Result); // 10
```

#### Continuing with Another Task
In order for you not to block the main thread, you have to use `ContinueWith()` which returns a task, and place the code that depends on the completion of the task inside the body of the anonymous function you pass it. This is similar to `then` in JavaScript promises.
```cs
var taskContinuation = Task
	.Run(() => CalculateLength("hello world"))
	.ContinueWith((taskWithResult) => {
		Console.WriteLine("Length is " + taskWithResult.Result);
	})
```

If you want to schedule a task after a set of tasks have been completed, you can use the `Task.Factory.ContinueWhenAll()` method that takes an array of tasks and a lambda to run after the completed tasks. This is so that you can do something if you wanted to schedule something after calling `Task.WaitAll()`.
```cs
var tasks = new[]
{
    Task.Run(() => CalculateLength("hello world")),
    Task.Run(() => CalculateLength("holla")),
    Task.Run(() => CalculateLength("hi")),
};

var continuationTask = Task.Factory.ContinueWhenAll(
    tasks,
    completedTasks =>
    {
        Console.WriteLine(string.Join(", ", completedTasks.Select(task => task.Result)));
    });
```

Multiple continuations with error handling:
```cs
var task = Task.Run(() => Divide(3, null));

task.ContinueWith((successfulTask) =>
{
    Console.WriteLine("Great! result successfully computed " + successfulTask.Result);
}, TaskContinuationOptions.OnlyOnRanToCompletion);

task.ContinueWith((faultedTask) =>
{
    Console.WriteLine("Something wrong happened: " + faultedTask.Exception.Message);
}, TaskContinuationOptions.OnlyOnFaulted);
```

#### Cancelling Tasks
In order to cancel a task, the code that requesting the cancellation and the code of the task being cancelled must cooperate. They can do this with a shared cancellation token

```cs
var cancellationTokenSource = new CancellationTokenSource();

var task = Task.Run(() => NeverEndingMethod(cancellationTokenSource), cancellationTokenSource.Token);

string userInput;

do
{
    Console.WriteLine("Enter something");
    userInput = Console.ReadLine();
} while (userInput != "cancel");

cancellationTokenSource.Cancel();

Console.WriteLine("Program is finished");

void NeverEndingMethod(CancellationTokenSource cancellationTokenSource)
{
    while (true)
    {
        if (cancellationTokenSource.IsCancellationRequested)
        {
            return;
        }

        Console.WriteLine("Working...");
        Thread.Sleep(1500);
    }
}
```

### Task Lifecycle
1. **Created** - task has been created with `task.Start()` or `Task.Run()`
2. **Waiting for Activation** - task has been started with either `task.Start()` or `Task.Run()`, or a task is waiting to be started in a `ContinueWith` method context
3. **Waiting to Run** - task is waiting to be picked up by the scheduler
4. **Running** - task is running
5. **Waiting for Children to Complete** - a task that spawns another task inside its unit of work may need to wait for its child task to finish
6. **Canceled / Ran to Completion / Faulted**


