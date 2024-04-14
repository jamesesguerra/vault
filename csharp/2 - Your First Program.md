### Creating a new Project
- a C# project is composed of your **source code** and **configuration**
- **source code (.cs)** - the instructions your write in C# for the computer to run
- **configuration (.csproj)** - the instructions you give to the compiler to tell it how to translate C# code into binary code (such as what version of .NET to target and the output file type)

### Compiling a Program
- a computer can't execute your C# program directly, you need to transform it into something it can run
- **compiling** -  the process wherein a compiler takes C# code and turns into binary instructions a computer can run; the final result being a *.exe* or *.dll* file (assembly files)

### Syntax and Structure
- **syntax** - a set of rules that tell you how to create a working program in a programming language
- **literals** - a chunk of code that defines a specific value, precisely as written i.e. when I enter the literal `"hello"` or `5`, I am literally talking about those values, as opposed to variables which point to some other value
- **identifiers** - allow you to refer to some existing code element 
- **keywords** - are words that have a special meaning in a programming language
- **dot operator** - allows you to access members of a code element that's deeper down in the hierarchy e.g. `Console.WriteLine`

#### Classes & Methods
- **classes (a person on a team)**
	- are entities that solve a single problem well
	- `Console`'s job is to interact with the console window, don't ask it to do anything else
	- composed of two things: **data they need to do their job**, and **tasks they can perform**
- **methods**
	- are the tasks a class can perform
	- **method invocation** - the act of asking a method to run with `()` and occasionally some data
	
#### Namespaces
- are mainly a code organization tool which classes live in
- **`using` directive** - allows you to use class identifier names without sticking the namespace in front of it, it tells the compiler "if you see an identifier, you can look in this namespace for it"

#### The Base Class Library
- the BCL contains namespaces, that contain many classes, each containing their own methods that provide every C# program with the fundamental building blocks
![[Pasted image 20240414200617.png]]

#### Program and Main
- the compiler takes our code and inserts it into a method `Main` inside a class called `Program`
- the `Main` method is also marked with a black arrow to indicate that `Main` is our "entry point" into the application, as it is the first thing to run
- no other methods will run before `Main` / unless `Main` calls them

#### Statements & Expressions
- **statement** - a single step or instruction for the computer to run, terminated by a `;`
- **expressions** - are bits of code that your program must evaluate / process to determine their value

#### Variables
- **variables** - are containers for data
- they're so-called variables since their values can change as the program runs
```cs
string name; // declaring a variable
name = "Ritlin"; // assigning the variable with a value
Console.WriteLine("Hello " + name); // using the variable in an expression
```

### Compiler Errors, Warnings, Debugging
- **errors** - your code can't compile since the compiler can't transform it into something a computer can understand
- **warnings** - your compiler thinks your code looks suspicious, but can compile and run it nonetheless
- **debugging** - your code is up and running, but it's not doing what you intended it to do

### Build Configurations
- configuration data is organized into different build configurations where each configuration provides different information to the compiler on how to build things
- **Release** - configuration which has optimizations turned on to allow your code to run faster without changing what it does
- **Debug** - no optimizations but makes it harder for you to hunt down problems since the release configuration removes unused stuff, among others

### Challenge Questions:
1. **How many statements do you think a program can contain?**
	- I think it's virtually unlimited, as much as the editor can handle without running out of memory

### Questions
1. **What is the difference between *.exe* and *.dll* files?**
	- *.exe* files are standalone files that launch a separate application of its own and lives in its own address space. *.dll* files need a host to run, and is used to have a collection of methods and classes which can be reused from some other application./



