### Memory Management
- Modern computers have large amounts of memory available, but it's not unlimited
- Memory must be carefully managed and you have to clean up after you use it
- .NET takes care of cleaning up your memory, but it's still important to understand the important language decisions that were made for this to be possible
- The gatekeeper of memory is the OS -- they provide your programs memory when they start up and you can negotiate for more
- The OS doesn't care how you use your memory as long as you don't use another program's memory

**The Stack**
- We use a stack as the first memory management strategy since it models the allocation and deallocation of space for each method that is called, while still being able to go back to methods that are still alive
- Each method call puts a new open box on the stack in which you can access its variables and data
- This covers the box below it, making the calling method's data temporarily inaccessible
- When you return from the method, the box is taken off the stack, making the box below it accessible again, and you can once again call a new method
- A line demarcates the area of in-use memory, every memory location below it has data that the program can make sense of, and everything above it is **garbage** which doesn't mean anything to the program

**Fixed-size Stack Frames**
- Freeing up memory for reuse is straightforward for stacks and value types since the bytes you want to reserve for value types are known ahead of time
- You can simply move the stack pointer up and down as methods return / are called since their stack frames are predictable in size -> new methods are then free to just replace the memory locations taken by those old stack frames
- That's why for types like strings and arrays, you can't use a stack since you don't know how much memory to reserve for them ahead of time

**The Heap**
- We then use the heap if we want to store arbitrarily-sized data 
- A heap is a random assortment of allocated data with no rigid organizational patterns
- When we look for a spot for a reference type in memory, we use the heap and grab a reference to it so that we have a way to find it in the heap (since there's no structure), and we store that reference in the stack instead

### Value Types and Reference Types
- **Value Types** - are types that contain there data in place, and have a known, fixed size
- **Reference Types** - are types that only hold a reference to the data that do not have a predictable size i.e. two variables that have the same type aren't guaranteed to have the same size
- If you copy a value type into another value type, the variable will contain the data itself
- If you copy a reference type into another reference type, the variable will contain a reference to the same object:

```cs
int i1, i2;
int[] a1, a2;

i1 = 5;
a1 = new[] { 1, 2, 3 };

i2 = i1;
a2 = a1;

i2 = 4;
a2[0] = 0;

Console.WriteLine(i1); // 5
Console.WriteLine(i2); // 4
Console.WriteLine(a1); // { 0, 2, 3 } a1 was also changed!
Console.WriteLine(a2); // { 0, 2, 3 }
```

**Value and Reference Semantics**
- **Value semantics** - two things are considered equal if their values are the same i.e. the bits stored in their memory locations are the same
```cs
int a = 88;
int b = 88;

Console.WriteLine(a == b); // True
```

- **Reference semantics**
	- two things are considered equal if they store the same reference i.e. the bits stored in their memory reference the same object in the heap
	- an exception to this is the string, which has value semantics
```cs
int[] a = { 1, 2, 3 };
int[] b = { 1, 2, 3 };

bool areEqual = a == b; // False!

b = a;

areEqual = a == b; // True
```

**The `ref` keyword**
The `ref` keyword gives what would be a value-type parameter, reference type semantics ie making something pass by reference instead of pass by value

```cs
var x = 5;

AddOne(ref x); // 6

void AddOne(ref int num) {
	++x;
}
```

### Cleaning Up Heap Memory
- To clean up memory, you notify the heap that your program will no longer need some heap object and the heap can then reuse the space
- The hard part isn't in releasing the memory but knowing *when* to release it
- **Memory leak** - happens when our program uses memory that it fails to clean up, and so it can't be reused by something else
- **Dangling pointers** - happens when a reference points to some unallocated (already freed) memory location; and it's possible that two parts of your program will reference the same memory for two different things
- The .NET runtime manages our memory for us safely with **garbage collection**, a process that wakes up periodically and scans for anything the program can no longer reach

```cs
int[] a = new int[10];
a = new int[5]; // the array initialized to a above is no longer accessible here! luckily our runtime will return the memory it used to the heap
```

### Questions:
1. **Why do we store reference types on the heap and not the stack?**
	- Reference types are stored on the heap because their sizes are arbitrary and cannot utilize the way stacks deallocate memory by simply moving the stack pointer up and down.
2.  **What is the difference between `==` and `.Equals()`?**
	- The difference is that `==` compares the references of two things whereas `.Equals` compares the content.

### Links:
1. https://www.youtube.com/watch?v=BeuNvhd1L_g