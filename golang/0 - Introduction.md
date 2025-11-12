#### Program Structure
- `package main` - tells the compiler that we want this program to compile and run as standalone, not to be imported by other programs
- `import "fmt"` - allows us to import the `fmt` (formatting) package from the standard library
- `func main()` - defines the `main` function, the entrypoint for a Go program

#### Why Go?
- it's natively-compiled
	- no need to run a VM / runtime along with it, hence a lot less memory
- has a GC
	- no need for manual GC
	- every executable includes a Go runtime that has a GC
	- also means it's slower than C, Rust, C++

