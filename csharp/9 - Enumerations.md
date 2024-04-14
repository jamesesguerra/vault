
### Enum Basics
- An **enumeration** is a type whose choices are one of a small list of possible options, emphasis on the word small (a few tens maybe hundreds)
- Enumerations are used for exhaustive lists where you don't leave anything out
- They're better than using ints and strings since the compiler can check for invalid values, and documentation purposes since it tells you what are the list of values that are allowed

```cs
enum Season {
	Winter,
	Spring,
	Summer,
	Fall
}
```
#### Using an Enumeration
```cs
Season current; // declare it like any other type
current = Season.Winter; // pick a specific value like this
```
### Underlying Types
- Underneath the hood, enumerations are built on `int`s
- By default, they're given in the order they appear starting from 0, but you can assign custom numbers