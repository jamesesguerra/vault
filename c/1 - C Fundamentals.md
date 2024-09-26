#### Writing a simple program
A program's executable code goes inside the `main` method. To print nicely formatted output, you can use the `printf` function, which is made available by C's standard input/output library.

```c
#include <stdio.h>
```

###### Compiling and linking
Before executing a program, several things need to take place:
- **preprocessing** - the program is given to a preprocessor, which obeys commands that begin with `#`, also known as directives; directives are things that can make modifications to a program
- **compiler** - a compiler then translates the program to machine instructions (object code)
- **linking** - a linker finally combines the object code with any necessary additional code in order to make an executable that will run (such as the code for `printf`)

The command used to compile a program depends on the OS. On Linux / MacOS, this is the `gcc` command. Linking happens automatically.
```sh
gcc hello.c -o hello.out
```

##### General form of a simple program
C programs rely on 3 key elements: directives, functions, and statements

They usually have the form
```c
<directives>

int main() {
	<declarations>
	<statements> / <functions>
}
```

##### Directives
Directives tell the preprocessor to alter the code in some ways. For example, the `#include` directive states that *information* inside `<stdio.h>` is to be included before the program is compiled. `<stdio.h>` is called a **header** which contains information about some part of the C standard library.

##### Functions
Functions or procedures are the main building block for code organization in C. They can either be written by a programmer or supplied by the library. They can also either return something or nothing at all.

Functions are more like procedures in the programming sense in that they aren't always pure. They are used mainly for grouping **statements** under one name. Statements are just commands to be executed by the program.

##### Variables
Variables are a mechanism for storing data temporarily. Each variable must have a **type** -- which dictates how much data it can hold and whether or not it can store a fractional part. Variables must also be declared before they're used. You can then assign it a value after being declared.

```c
int main() {
	int x, y, z;
	float a;

	x = 5;
	a = 2.3f;
}
```

##### Reading Input
To read input, you can use the `scanf` function. It's the counterpart of `printf` in the C standard library. Like `printf`, you need to specify what kind of input will be received by using a format string. It then stores the input inside a variable you specify.

```c
int x;
scanf("%d", &x);
printf("%d", x); // prints out whatever x is
```

##### Constants
To create constants, you can use the `#define` preprocessing directive to define a **macro definition**. When the program is given to a preprocessor, it replaces all the values where the constant is used with the value you give it.

```c
#define INCHES 166
#define RECIPROCAL_OF_PI (1.0f / 3.14159f)
```