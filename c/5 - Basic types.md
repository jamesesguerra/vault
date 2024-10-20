#### Integers
There are 2 basic numerical types -- **integers** and **floats**. Integers can either be:
- `signed` with the leftmost bit signifying its sign (0 for positive, 1 for negative)
- `unsigned` with the leftmost bit being a part of the magnitude
- `short` 16 bits
- `long` 64 bits

To see the maximum and minimum values for each integer type, you can include the `<limits.h>` header file

#### Floats
C provides 3 float types, types that can store numbers after a decimal point.
- `float` a single-precision floating-point
- `double` a double-precision floating-point
- `long double` an extended-precision floating-point

By default, float constants are stored as double-precision numbers, and are just converted to floats when necessary. You can however make the choice clear:
- `57.0F` - float
- `57.0L` - long double

#### Character Types
Character types can vary from computer-to-computer since different machines have different character sets. Today's most common character set in use is Unicode.

C treats characters as small integers, and so they're treated as integers in computations.

```c
char c;
int i = 1;

c = 'A';
c += i;
c; // 'B'
```