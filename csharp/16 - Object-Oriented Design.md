### Requirements
The first step of building object-oriented systems is understanding what the software needs to do. Some good tips are to:
- put the requirements into words e.g. writing sentences that describe a feature
- sketch something out

### Designing the Software

#### Noun Extraction
A possible first step is to identify the concepts and jobs that the requirements reveal as they will often lead to the creation of classes in your design. Some class will have to handle a part of the requirements that your software needs to be able to do.

You can start by highlighting the **nouns** and **verbs** that appear in the requirements. For example:

> **requirement** - *"Asteroids drift through space at some specific velocity."*
> **nouns** - asteroid, space, velocity
> **verbs** - drift

#### CRC Cards
Class-Responsibility-Collaborator cards help you flesh out the design by figuring out which objects should exist, what parts of the overall problem each object should solve, and how they should work together. To do this, you use index cards and list three things:

1. the class that the object belongs to
2. the responsibilities it has (can be a thing to know or a thing to do)
3. the object's *collaborators* -- other objects that help the object fulfill its responsibilities

#### Rules to Evaluate a Design
1. **It has to work** - a useful design has to do what it's supposed to do
2. **Prefer designs that convey meaning and intent** - pretend you're just coming back to your code after months and have to tweak it, does the design make sense?
3. **Remove duplication** - a design that contains logic/data duplication is worse than one that does not
4. **Don't add unused/unnecessary elements** - don't add premature optimization / abstractions / features unless needed in the immediate future

#### How to Collaborate
Objects collaborate by calling members on the object they need help from. There are a variety of ways an object can know about its collaborators:

**Creating new objects**
The first way to get a reference to an object is by creating a new instance with the `new` keyword. This is how `AsteroidsGame` can generate new `Asteroids` in the game

**Constructor Parameters**
You can also have something else hand the class the reference when the object is created through the constructor's parameters. It's a popular choice if the object needs another object from the beginning but can't or shouldn't just use `new` to create a new one.
```cs
public AsteroidsGame(Asteroid[] startingAsteroids) {
	_asteroids = startingAsteroids;
}
```

**Method parameters**
If an object only needs a reference to something for a single method, it can be passed in as a method parameter
```cs
public class AsteroidDriftingSystem {
	public void Update(Asteroid[] asteroids) {...}
}
```

**Asking another object**
You can also get a reference to an object by asking a third collaborator to supply the reference. In this case, the `AsteroidsGame` instance contains a public `Asteroids` property that returns the list of asteroids
```cs
public void Update(AsteroidsGame game) {
	foreach (Asteroid asteroid in game.Asteroids)
}
```

**Supplying the reference via property or method**
If you can't supply a reference to an object in the constructor but need it for more than 1 method, you can have the outside world supply it through a property or method call then save it off to a field for later use
```cs
public Asteroid[] Asteroids { get; set; } = new Asteroid[0];
```

**Static members**
A final approach would be to use a static property, method, or field, to create global state which can be reached from any class.

### Questions:
1. **Is there an ideal way for classes to collaborate i.e. to be related to one another?**
	- I believe it depends on the use-case, but general design patterns such as using interfaces and abstract classes, dependency injection, and using composition makes it so that your classes are loosely-coupled, which is the ideal scenario.


