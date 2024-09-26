##### `printf`
This function displays the contents of a string -- known as a format string. A **conversion specification** is a placeholder representing a value to be filled in. The value following the `%` specifies how a value is converted from their internal form (bits) into characters.

The conversion specification is in the form `%m.pX` where:
- `m` - specifies the minimum width of the field
- `.p` - specifies how many digits can appear after the decimal point for floats, and how many extra zeroes will appear before the first digit for integers
- `X` - is a letter that says how to interpret the value given to it ie the conversion specifier

##### `scanf`
`scanf` works essentially the same way as `printf` except in reverse. It's a pattern-matching function that reads input data and tries to convert, and store them into variables. They ignore whitespace, and try to match for a variable given its data type for as long as it can until it reaches a character that's invalid for that data type / receives an error. This function is generally avoided since it has a lot of pitfalls. It's more common to just read the text stream as a whole character array and convert the values from there.

One pitfall is including different characters / white space in the format string. Once `scanf` tries to match a different character to the input string and cannot match, it will terminate and leave the characters in stdin to be read by the next call to `scanf`.

```c
scanf("%d%d%f%f", i, x, y, j);
```