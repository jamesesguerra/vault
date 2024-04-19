### The primitives
The 3 very commonly used primitives are `string`, `number`, and `boolean`

```ts
let name: string = "James";
let x: number = 0;
let isValid: boolean = false;
```

### Arrays
To specify the type of an array, you can use the syntax `number[]`. There's also another syntax to use generics which is also valid and is of the form `T<U>`.

### `any`
This is the default type of JS. Using `any` bypasses type-checking and you'd use it if you know the environment better than TS and you don't want a particular value to cause type-checking errors, or if you don't wanna write a long type just to convince TS that a particular line of code is ok e.g. if you're prototyping and you're too lazy to define custom business data types.

When a value is of type `any`, you can access any properties of it, call it like a function, or anything that's syntactically legal.

```ts
let obj: any { x: 0 };

obj.foo();
obj();
obj = "hello";
const n: number = obj;
```

A similar type in C# is the `dynamic` type. `any` and `dynamic` are similar in that they'll both resolve their operations at runtime.

### Functions
You can add type annotations to each parameter. Even if type annotations aren't added, TS will still check if it's passed the right number of arguments.

```ts
function greet(name: string, date: Date) {
	console.log(`Hello ${name}`);
};

greet(4); // oh no, name is not a string and you're missing the second argument
```

And you can also annotate the return type, but this is  also optional since it can infer the return type from the return statement, if any. If you're returning a promise, you should use the `Promise` type.

```ts
function getMyFavoriteNumber(): number => 6;

function getFavoriteNumber(): Promise<number> => 6;
```

#### Contextual Typing
TS can also infer the type of parameters in an anonymous function passed to a `forEach` loop. It uses the type of the array along with the `forEach` loop to determine the type `s` will have

```ts
let anonymous = (s) => s; // s has type any here

let numbers = [1, 2, 3];
numbers.forEach((s) => s); // the same anonymous function is passed, but in this case, s has type number inferred from the forEach loop
```

### Object types
To define an object type, simply list its properties and their types. You can also denote some properties as being optional by adding a `?` at the end of the property. You will then have to check for `undefined` if you access it, and using the null-conditional operator `?.` makes this easy.
 
```ts
function printCoordinates(point: { x: number, y: number, someOptionalProperty?: string }) {
	console.log(`Im a point at (${x}, ${y})`);
	
}

printCoordinates({ x: 5, y: 0 });
```