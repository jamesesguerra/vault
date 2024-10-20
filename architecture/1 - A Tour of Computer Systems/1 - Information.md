A program begins as a source code file that a programmer writes in a text editor. For example, a program to print the words "hello world" on the terminal can be written and placed in a file called `hello.c`. The source program is a sequence of bits organized in 8-bit chunks called *bytes*, where each byte represents some text character in the program.

Most computers represent text using the ASCII standard / Unicode. These standards have a mapping for each byte in the source file to a text character. For example, if a byte is equal to 65, it maps to the letter A. Files that consist exclusively of ASCII characters are known as text files, whereas everything else are called binary files.

This representation of a program such as `hello.c` illustrates a fundamental idea: all information in a system--including disk files, programs stored in memory, user data stored in memory, and data transferred across the network, is just a bunch of bits. What differentiates each is the context in which we view them. That is, information / data objects in a computer is just bits + context.

#### The different forms of a program
The `hello.c` program begins life as a high-level C program in plain text since it's what's convenient for programmers. But in order for the system to run the program, the source file needs to be converted to machine-language instructions. These machine instructions are then packaged into an *executable* object program and stored as a binary disk file.

On Unix systems, this translation from source file to object file is done by a *compiler*.

```sh
gcc -o hello hello.c
```

##### The 4 phases of translation
- **preprocessing**
	- the preprocessor reads directives and replaces include directives with the contents of the header file
	- header files contain declarations, constants, type definitions
- **compilation** - the compiler translates the text file into another text file that contains an assembly-language program
- **assembly** - the assembler translates the assembly program into machine-language instructions
- **linking**
	- the linker merges the machine-language instructions of precompiled programs that are used in the source file
	- e.g. if `printf` is used in `hello.c`, the linker merges the contents of `printf.o` 