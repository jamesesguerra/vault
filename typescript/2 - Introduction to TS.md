### C# vs TS 

#### Rethinking the Class
C# is a *class-based mandatory OOP language*, wherein the class is the basic unit of code organization, and is the basic container of all data and behavior at runtime. This can be a good domain model for some problems, but not every domain needs to be represented this way.

In JS, functions can live anywhere and data can be passed freely without being inside a class. This flexibility is extremely powerful. Additionally, certain constructs from C# such as singletons and static classes are unnecessary in TS.

That said, TS can still use classes if you like and are well-suited to model a traditional OOP hierarchy with TS's support for JS classes. It can support interfaces, inheritance, and static methods.

#### Rethinking Types
In C#, any given value has exactly one type -- either `null`, a value, or a reference type. The definition of a type resides in a class somewhere, and you can't use 2 classes with similar shapes in the place of each other unless they implement a common interface or inherit from the same superclass. This is what's called a **reified, nominal type system** -- a system wherein the types written in the code are present at runtime and are related via their declarations (names), not their structures.

But in TS, types are structural. That is, relationships between types are determined by the **properties they contain**, not whether they were declared with some particular relationship.

```ts
interface Pointlike {
	x: number;
	y: number;
}

function ITakePoints(point: Pointlike) {
	console.log(`Im located at (${point.x}, ${point.y})`);
}

const obj = {
	x: 0,
	y: 0,
	name: "Origin"
};

ITakePoints(point);
```

In the example, we can treat `obj` as a `Pointlike` type even if we didn't declare it to be (using  `const obj: Pointlike`) because it has `x` and `y` properties that are both numbers. The TS system also isn't reified as there's no way to tell us that `obj` is `Pointlike` at runtime, and `Pointlike` isn't present in *any* form at runtime.
 
**C#** - there is a 1-to-1 correspondence between a runtime type and its compile-time declaration
**TS** - types are a *set of values* that share something in common; a particular value can belong to many sets at once because types are just sets