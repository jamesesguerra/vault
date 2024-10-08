Most of C's statements fall into three categories:
- **selection** - `if`, `switch`
- **iteration**- `while`, `do`, `for`
- **jump** - `break`, `continue`, `goto`

In C89, comparison expressions yield integers, either a 0 for false, and 1 for true. However, C99 added support for booleans by including the `<stdbool.h>` header. 
```c
#include <stdbool.h>

int main() {
	bool isTrue = true;
	bool isFalse = false;
}
```

You also have the option of defining constants that represent boolean values
```c
#define TRUE 1
#define FALSE 0
```

... or even aliasing a type to better represent that the constant stores booleans only
```c
#define BOOL int
```

Using comparison operators such as `<` and `>` on constants yield booleans. This is why you can't do something like `4 < x < 6` inC# since because comparison operators are left-associative, the first expression to be evaluated, `(4 < x)` will yield a boolean. In C however, booleans are integers and can thus be compared to other numbers, although not giving the result expected.

#### Cascaded if-statements
For some cases, you wanna test a series of conditions against a value. This is where cascaded if statements come in aka the `else if`. In C, there is actually no `else if` statement. It seems as though there is, but in reality, an else if is just an if statement tacked on to an else, creating cascading if-else checks. In other words, they're if-statements that have if-statements in their else clauses.

