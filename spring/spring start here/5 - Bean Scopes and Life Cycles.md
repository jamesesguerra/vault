> **scopes** - are different ways Spring creates and manages the life cycle of a bean
### Singleton
The singleton bean scope is the default scope Spring uses and is the most common in production apps.

When spring creates a singleton bean, it loads the context and assigns the bean a name, a bean ID if you will
- **the catch**: you can have more instances of the same type in the context if they have different names
- for Spring, the singleton concept means unique per name, but not unique per app

**singleton in the traditional sense:**
- you may have only 1 instance of a specific class in the app's memory

**singletons in Spring:**
- you may have only 1 instance of a specific bean
- you can have multiple instances of the same class if they use different names e.g.

```java
public class ProjectConfiguration {
	@Bean
	public CommentService commentService1() {
		return new CommentService();
	}

	@Bean
	public CommentService commentService2() {
		return new CommentService();
	}
}
```

#### Proving instances are the same
When using `@Bean` annotations, the name of the bean in the context is the name of the bean method. When using stereotype annotations, the bean names are the names of the classes.
```java
var c1 = context.getBean("commentService1", CommentService.class);
var c2 = context.getBean("commentService2", CommentService.class);

System.out.println(c1 == c2); // returns True
```

#### Immutability
Singleton beans must be **immutable**, i.e. its fields must be `final` / have no fields at all. Beans / object instances are considered **thread-safe** this way because multiple threads can access the same instance without risk of any interference because the bean's behavior doesn't depend on instance-specific data that can change during execution.
- If an app uses multiple threads to carry out an action, a race condition might occur since the 2 threads are accessing a shared resource of the same object instance
- The developer needs to properly synchronize the thread to avoid race conditions, but this is generally considered as bad practice

#### Eager and lazy instantiation
**eager** - the default behavior; Spring creates the singleton beans when it creates the context

```java
@Service
public class CommentService {
	public CommentService() {
		System.out.println("Creating the comment service");
	}
}
```

in this case, just instantiating the context would cause the line to be printed out since it created the beans
```java
var context = new AnnotationConfigApplicationContext(ProjectConfig.class);
```

**lazy** - Spring creates the singleton bean that first time the instance is referenced

```java
@Service
@Lazy
public class CommentService { ... }
```

the line is only printed out when the instance is referenced
```java
var context = new AnnotationConfigApplicationContext(ProjectConfig.class);

var commentService = context.getBean(CommentService.class);
```

**When to use one over the other?**
- In general, stick to the default as it saves some time with checking whether a bean was already instantiated, plus it's easier to observe issues
- Use lazy instantiation if your app uses a lot of memory when loading different services. This might be a sign of bad design though, as it's a sign your app is better off designed in a modular way

### Prototype
The prototype scope creates a new instance of a specific class every time it's referred to. This makes it thread-safe as each thread would essentially be working with two separate instances of a class.

```java
public class ProjectConfiguration {
	@Bean
	@Scope(BeanDefinition.SCOPE_PROTOTYPE)
	public CommentService commentService() {
		return new CommentService();
	}
}

// or
@Service
@Scope(BeanDefinition.SCOPE_PROTOTYPE)
public class CommentService { }
```

```java
var c1 = context.getBean(CommentService.class);  
var c2 = context.getBean(CommentService.class);  
  
System.out.println(c1 == c2); // returns False
```

#### Guidelines
- In general, you should stick to singleton beans when you can. But if you have a class that has mutable instances, use prototype beans.
- Be wary of injecting prototype-scoped beans into singleton-scoped beans as the singleton bean will always use the same instance of the prototype bean. Instead, reference the prototype bean from the context explicitly every time like this

```java
@Service
public class CommentService {
	@Autowired
	private ApplicationContext context;

	public void sendComment(Comment comment) {
		var p = context.getBean(CommentProcessor.class);
		// ...
	}
}
```

prev: [[4 - Abstractions]]
next: [[6 - Aspects]]