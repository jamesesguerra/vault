The Common Language Runtime (CLR) is a complete, high level virtual machine designed to support a broad variety of programming languages and interoperation among them.

It's responsible for things such as:
	- JIT compilation
	- managing the memory & garbage collection
	- providing error handling
	- dealing with threads

These are things programmers would have to be responsible for if a runtime didn't exist to help them with it. Low-level tasks such as memory management would have to be dealt with manually.

### What are runtimes?
Every program (source code) has dependencies on its runtime environment. The most obvious one is that it's written in a particular **programming language**, so the runtime has to support it. They also need a **runtime library** that allow them to interact with other resources of the machine, such as user input, files, network communications etc. All programs also need to be converted in some way (either interpretation or compilation) to a form that the native hardware can execute directly. There are so many dependencies that implementers of programming languages just defer to other standards to specify them. For example, C++ doesn't specify the format of a C++ executable. Instead, each C++ compiler is bound to a particular hardware architecture (eg x86) and to an OS.

The difference:
- each C++ compiler is bound to a particular hardware architecture and OS which describes the format of the executable file and specifies how it will be loaded
- CLR / JVM specifies exactly how to lay the bits down in a file, so that your CLR EXE isn't tied down to a particular architecture / OS


### Garbage Collection
- Freeing up memory by removing unused objects
- Runs on a separate thread that can potentially stop other threads for a moment

**Memory Defragmentation** is the process of moving objects in memory together to create bigger chunks / blocks of free memory. If this isn't carried out, it might be hard to find a place in memory for some objects since they can't fit into any one space even if the overall available space in memory actually is enough.

If an object survives the first mark-and-sweep pass through of the GC, it moves up to a next generation. Generations include 0, 1, 2, where each increasing generation gets checked for GC less often. There is also a large objects heap where objects greater than 85kb gets placed in immediately, and are pinned in memory so it won't be defragmented as this would be expensive.

A memory leak happens when some piece of memory isn't being cleaned up, even if the object occupying it is no longer in use, therefore no other object can occupy it.


### Dispose
The 2 types of resources are **managed** and **unmanaged**. Managed resources are managed by the CLR, and most resources are managed. They're resources that are known by the GC, and so we don't need to worry about cleaning them up since it's done automatically. On the other hand, unmanaged resources are not known by the CLR and so it's unable to manage them. Resources like these include DB and network connections, as well as file handlers. Because they aren't managed, it's up to developers to dispose them themselves.

The `Dispose` method is used to free up unmanaged resources. To implement this method, implement the `IDisposable` interface on the class that has unmanaged resources