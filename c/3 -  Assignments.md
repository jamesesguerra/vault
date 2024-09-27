#### Simple assignment
Unlike other programming languages, assignment statements produce a value, therefore are also considered expressions. When assigning a value `e` to a value `v`, as in `v = e`, the value produced by the assignment statement is the value of `v` after the assignment. This is why you can do something like:

```c
int x, y, z;
x = y = z = 10;
```

In this example, `z` is first assigned 10 because the assignment operator is right-associative. `y` then gets assigned the value of `z` after the assignment, which is 10, and so on.

##### L-values
Assignment operators are also different from other operators in that their left operand must be an L-value. Lvalues are objects stored in memory ie not a constant or a result of a computation.

Examples of lvalues include:
- variables

#### Compound Assignment
Compound assignments are assignments that use the old value of a variable to compute its new value. These are your `+=`, `-=`, `*=`, and `/=`.

These statements aren't purely for syntactic sugar though. For example, `a += 5` is different from `a = a + 5` in that `a` is only evaluated / fetched from memory once in the latter example, whereas it's evaluated twice in the second. And so if the rvalue has a side-effect, the behavior is undefined.

#### Postfix & prefix
The difference in postfix and prefix increment / decrement operators is that using postfix operators causes the expression to be evaluated before the increment / decrement takes place. On the other hand, prefix operators causes the expression to be evaluated after the increment / decrement.

```c
int i = 0, y = 0;

++i; // 1
y++; // 0
```