### The Compilation Process
First, `.cs` files are compiled by Roslyn into CIL, a human-readable intermediate language that all programming languages supported by .NET compile into.

Next, the IL gets compiled by the JIT compiler into machine code specific to the OS/machine the program runs on. This is beneficial since it doesn't necessarily compile all code, just the ones that are needed.

