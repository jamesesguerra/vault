#### Declaring and initializing variables
You can define variables the lame way:
```go
var num1 int
num1 = 42
```

...Or the better way, with the walrus `:=` operator; a limitation of this is that you can only use it in a function. The advantage is that it's more concise and has type inference
```go
messageStart := "Happy birthday! You are now"
age := 21
```

If you don't initialize a variable with a value, it'll take on its *0* value.
```go
var myInt int // 0
var myFloat float64 // 0
var myBool bool // false
var myString string // ""
```

You can also declare and initialize variables on the same line
```go
manufacturer, mileage := "Toyota", 86400
```

**Constants** can be defined like so. Keep in mind that you can't use the walrus operator with constants, and that you can only initialize it with primitive types.
```go
const premiumPlanName = "Premium Plan"
```

#### Converting between types
Converting between types can be done as easily as:
```go
myFloat := 42.4
myInt := int64(myFloat)
```