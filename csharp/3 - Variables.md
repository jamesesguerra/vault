### What are Variables
- **variables** allow us to store data in temporary memory for use later
- physically, they're volatile memory in RAM that sticks around while your program is running but is wiped out as soon as your program ends
- are named locations instead of the raw 16-bit memory addresses that are tedious to write out and remember
- variables have 3 things: a **name**, **type**, and a **value**
- a variable's type tells the compiler how many bits to reserve for its value

### Questions
1. **How do you declare AND initialize multiple variables on the same line?**
```cs
// not like this
int x, y, z = 3, 4, 5;

// like this
int x = 3, y = 4, z = 5;
```