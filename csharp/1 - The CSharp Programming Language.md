## CSharp

### What is C#?
- a **general-purpose** programming language used to make games, desktop programs, web applications, smartphone apps
- it's at its best when building applications of any sort (why? not good for writing OSs and device drivers since it lacks lower-level programming capabilities)
- also a **cross-platform** language that can run on every major OS
- statically typed, garbage collected, object-oriented programming language with imperative, functional, and event-driven aspects

### What is .NET
- C# is built upon .NET, which is the **entire ecosystem** surrounding C# programs and the programmers that use it
- it's much like the air your program breathes while it's running
- **Base Class Library (BCL)** - a pile of code you can think of as a public library wherein you can search for methods to support your program's needs
- **Software Development Kit (SDK)** - a set of tools that makes programming life easier

#### More Questions
1. **What does it mean for a programming language to be object-oriented? Is it just because they support the creation of an object?**
	- A programming language is object-oriented if it supports the creation of objects that break up the program's state and can "talk" to each other about their state. Another primary feature of OO languages is that you can use abstraction to forget about the implementation details, and use a public interface to connect different modules, thus making them more flexible. Other notable features include encapsulation and hierarchy (inheritance).
2. **How does C# support functional programming?**
	- It supports it by having stuff like expression-bodied members, higher-order functions with the `Func` type, and immutability.
3. **What are examples of things you can find in an SDK?**
	- SDKs include everything you need in order to run a .NET application such as libraries, command line tools, and the .NET runtime.


---
## .NET
### .NET Framework
- development platform for building apps
- includes a Common Language Runtime (CLR) which manages the execution of code and a Base Class Library (BCL) which provides a rich library of classes to choose from
- tied closely to Windows
- legacy feature set used to maintain existing applications only

### Mono, Xamarin, and Unity Projects
- **Mono** - third party .NET Framework that is cross-platform but fell behind
- **Xamarin** - for mobile and desktop apps only
- **Unity** - cross-platform game development platform

### .NET Core
- rewritten .NET Framework to be cross-platform
- decoupling it from its close ties to Windows; removed legacy and non-cross-platform technologies
- includes a cross-platform CLR called CoreCLR and a streamlined BCL called CoreFX
- modularized and open-source

### .NET Support
- **LTS** - stable and require fewer updates over their lifetime; good choice for applications that you don't intend to update frequently
- **Current** - includes features that may change based on feedback; good choice for applications that you are actively developing
- **Preview** - for public testing / adventurous programmers

### Entity Framework Core
- ORM for relational and non-relational DBs

### Intermediate Language (IL)
- C# compiler (Roslyn) converts C# source code into intermediate language code and stores the IL in an assembly (a DLL or EXE file). At runtime, the CoreCLR loads the IL code and the JIT compiler compiles it into native CPU 

### .NET Standard
- .NET Core, .NET Framework, and Xamarin were different platforms with their own quirks and limitations
- standard was created as a specification for a set of APIs that all .NET platforms could implement to indicate what level of compatibility they had