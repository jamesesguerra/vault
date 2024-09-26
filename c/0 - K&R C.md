A hello world program:
```c
#include <stdio.h> // for standard I/O

int main() {
	printf("Hello world!"\n);
	return 0;
}
```

- `printf` takes a string and outputs its argument into the terminal
- string formatting:
	- `%d` - decimal
	- `%f` - floating point
	- `%5f` - floating point 5 characters wide
	- `%5.1f` - floating point 5 characters wide with 1 character after the decimal point allowed
	- `%o` - octal
	- `%x` - hexadecimal
	- `%c` - character
	- `%s` - character string (char array / string)
	
#### main method
C programs consists of a bunch of functions. Like C# and Java, C programs must always have a function to "get the ball rolling", in the form of a main method. This is why it's important to have a main method in every program.
```c
int main() { }
```

#### Variables and arithmetic
Variables must be declared before they're used, usually at the start of the function before any statements are executed. A *declaration* announces the properties of variables.

```c
int lower, upper, step;
```

Data types include: `char`, `short`, `long`, `double`. The sizes of these objects are also machine-dependent.

**Constants**
You can also avoid using magic numbers by defining constants with `#define`.
```c
#define LOWER 300
```

#### Loops
The `for` loop is just a generalization of the `while` loop in that it contains not just the loop condition (predicate) in its argument list, but also the loop iteration, and loop increment steps.

```c
for (<initialization>, <condition>, <increment>) {}
```

#### Character I/O
Text I/O regardless of where it originates is dealt with as streams of characters. A **text stream** is a sequence of characters divided into lines where each line consists of 0 or more characters followed by a newline character.

```c
c = getchar(); // reads 1 character from the input stream
putchar('c'); // prints a character
```

**File copying**
Character types, although appearing like actual characters on the screen, are actually stored as bit patterns internally just like everything else.  

The reason why `int` is used instead of `char` is because it can store more data. `int`s can hold 2 or 4 bytes whereas `char`s can only hold 1 byte. And `getchar()` returns a special value that denotes the end of the file called `EOF`, which must be distinguishable from any other valid data. And so the data type to be used must be big enough to hold any data and `EOF`.

```c
int main() {
	int c;

	while ((c = getchar()) != EOF) {
		putchar(c);
	}
}
```

#### Functions
The main method can return something as it is also a function just like any other. It can return to its caller, which is the environment in which the program was executed, the result of running the program. Typically, a return value of 0 implies that nothing went wrong; and non-zero values signal incorrect termination.

```c
int power(int m, int n) {
	int product = 1;
	
	for (int i = 0; i < n; i++) {
		product = m * product;
	}
	
	return product;
}
```

Arguments in C functions are pass by value when their data types are the fundamental data types in C (chars, integers, floating point numbers). When necessary, you can pass the *address* of a variable to a function, effectively making it so that a function can alter the value of its arguments outside the function. This is done by using pointers. Like C#, passing an array argument makes it so that the argument is automatically pass by reference, and the function can change the array itself, not just a local copy.

#### Character Arrays
It's very important to note that strings in C are arrays of characters. To mark the end of the string, C uses a special symbol `\0`.
```c
char c[100];

int getaline(char s[]) { }
```

You can then print the character array as a string using the `%s` string formatter.
```c
printf("%s", c);
```

#### External variables & scope
