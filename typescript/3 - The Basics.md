### Dynamic typing
The only way in JS to know what values will result in improper / proper behavior is through dynamic typing -- running the code to see what happens.

In this function, we can observe that it'll only work if given a an object with a callable `flip` property. The only way we'll know what `fn` does is to call it with a particular value and see what happens. This makes it harder to predict what your code is going to do while you're writing it.

```js
function fn(x) {
	return x.flip();
}

// although you can do this, it's still hard to do in every corner of your code and is messy
function fn(x) {
	if (Object.hasOwn(x, "flip") && (typeof x?.flip == "function")) {
		return x.flip()
	} else {
		return "I don't have a (callable) flip property!";
	}
}
```

### Static type-checking
Performing invalid operations on a certain value will cause your program to throw errors. Static type-checking describes the **shapes** and **behaviors** of what our values will be when we run our code. It uses this information and tells us if things will eventually / potentially go wrong at runtime.

```ts
const message = "hello world";
message(); // typescript already gives an error here since it knows message is not callable
```

Even code that *should* throw an error in JS but doesn't  is also checked by TS

```ts
const obj = {
	name: "James"
};

console.log(obj.age); // would be undefined in JS, but gives an error in TS
```

#### Emitting with Errors
One of TS's core values is that most of the time, you will know better. And so even if there's an error, it will still emit JS code when you compile it. This is useful for example if you're migrating vanilla JS code to TS and many type-checking errors get in the way even if the code was already working.

You can choose to be more defensive against these and tell TS to act more strictly with the `tsc --noEmitOnError` compiler option.

#### Types
If you don't tell TS what the types of your values are, it's still able to infer the type. This is **implicit typing**, and it's a feature of TS. But you can choose to add *type annotations* to your variables making it use **explicit typing**. These types are erased in the compiled JS.

```ts
function greet(name: string, date: Date) {
	console.log(`Hello ${name}, today is ${date.toDateString()}`);
}

greet("Rasha", Date());
```

### Downleveling
TS has the ability to rewrite code from newer version of ECMAScript to older ones. The process of moving from a newer version of ES down to an older one is called *downleveling*.

You can tell the compiler to target a specific standard using the `--target flag`

```sh
tsc --target es2015 index.ts
```

### Strictness
Different users come to TS for different reasons. Some want the full type-checking experience while others just want an opt-in experience that checks some parts of their code and some decent tooling. You can customize TS's strictness settings turning it into more of a **strictness dial** than a switch. 

There are 2 major strictness settings you should know about:

`noImplicitAny`
Using default settings, if TS can't infer the type it will fall back to the most lenient type: `any`. This is the plain JS experience, but it defeats the purpose of using TS. Turning this setting on gives you more validation and tooling since more of your program is typed.

`strictNullChecks`
By default, values like `null` and `undefined` are assignable to any other type. This setting makes handling them more explicit, and spares us from worrying about whether we handled `null` or `undefined`.

### Questions:
1. **Why is it better to use implicit typing rather than explicit typing?**
	- There are pros and cons to each, but what makes implicit typing appealing is that it's less verbose and easier to maintain. This doesn't mean you shouldn't have any explicit types anymore, but for trivial types such as strings, numbers, and booleans, implicit typing is preferred.
