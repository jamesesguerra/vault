## Prototypes
Every object in JS has a built-in property called its **prototype**. The prototype is also an object so it also has its own prototype, making a **prototype chain**. The chain ends when a prototype contains `null` for its own prototype.

When you try to access a property of an object, it will search that object first but if it can't find it, it will search the object's prototype. If the property still can't be found, then the prototype's prototype is searched, and so on until the property is found or the end of the prototype chain is reached, in which case `undefined` is returned

To check what the prototype of an object is: 
```js
Object.getPrototypeOf(myObject); // Object { }
```

![[prototypes.png]]

### Shadowing Properties
If you define a property in an object that is already defined in that object's prototype, that version will be used instead of the one in the object's prototype.

```cs
const myDate = new Date();

console.log(myDate.getYear()); // 124

myDate.getYear = () => "shadowed!";

console.log(myDate.getYear()); // shadowed
```

### Setting Prototypes
You can set an object's prototype by using `Object.create()`. We can create a new `dog` object and set its `prototype` to `animal` so that it provides its implementation for the `eat` method.

```cs
const animal = {
	eat() { console.log("munch") }
};

const dog = Object.create(animal);
dog.eat(); // munch
```

Another way of setting an object's prototype is using constructors. Every function also has a property named `prototype`. And if you call a function as a constructor (using the `new` keyword), this property is set as the prototype of the newly constructed object

```js
const personPrototype = {
	greet() { console.log(`Hi, my name is ${this.name}`) };
}

function Person(name) {
	this.name = name;
}

// assigning the person prototype to be the prototype of every new object that's created using the Person constructor
Object.assign(Person.prototype, personPrototype);

const james = new Person("James");
james.greet(); // "Hi, my name is James"
```

### Differences between Class-based OOP and Prototypes
The first difference is that in class-based OOP, **classes and objects are two separate constructs**, and objects are always created as instances of classes. There is also a distinction between the feature used to define a class (class syntax) and the feature used to instantiate an object (constructors). Using JS prototypes, objects are created without any class separation, either using a function or a class literal.

The other difference is that although a prototype chain looks like an inheritance hierarchy and behaves like it in some ways, it's still different. When a subclass is instantiated, it combines members defined in the subclass together with ones defined further up the hierarchy. In prototype chains, **each level of the hierarchy is represented by a separate object**. This makes its behavior more like delegation in that an object asks another object to perform a task on its behalf.

---

## Classes
Classes aren't a new way of combining objects. They still use prototypes, but they make it easier to set up a prototype chain rather than having to use constructors and their `prototype` properties.

```js
class Person {
	name = ''; // init to default value

	constructor(name) {
		this.name = name;
	}

	introduceSelf() {
		console.log(`${this.name}`);
	}
}

const james = new Person("James");
```

#### Inheritance
Some things I noticed about the example:
1. Fields, including the ones created by the `Person` prototype, are defined on the object itself; however, methods aren't -- they're defined on the prototypes created by the classes ie `introduceSelf` and `grade` aren't defined on `walsh`, they're defined on `walsh.__proto__` (the `Professor` prototype)
2. The prototype chain is: `walsh` -> `Professor` -> `Person` -> `null`

```js
class Professor extends Person {
	teaches;

	constructor(name, teaches) {
		super(name);
		this.teaches = teaches;
	}

	introduceSelf() {
		console.log(`My name is ${this.name} and I will be your professor for ${this.teaches}`);
	}

	grade(paper) {
		const grade = Math.floor(Math.random() * (5 - 1) + 1);
		console.log(grade);
	}
}

const walsh = new Professor("Walsh", "Psychology");
```

### Encapsulation
We want to make the student's `year` private so that no outside code can be tied to it and break if changes are made. We make it private using the `#` symbol so that no outside code can access it.

```cs
class Student extends Person {
	#year;

	constructor(name, year) {
		super(name);
		this.#year = year;
	}

	introduceSelf() {
		console.log(`Hi Im ${this.name} and im year ${this.#year}`);
	}
}

summers.#year = 4; // not accessible cos of the private identifier
```

Private methods can also be created using the same identifier scheme

```cs
class Example {
	somePublicMethod() {
		this.#somePrivateMethod(); // works here;
	}

	#somePrivateMethod() {
		console.log("You called me?");
	}
}

const example = new Example();
example.#somePrivateMethod(); // but not here
```