 Wiring beans essentially means establishing a relationship between them. We want to remember that object instances often delegate their behavior to other instances, having them carry out some of their functionality. They do this by often storing, sometimes creating, a reference to it. In this sense, we see a HAS-A relationship between the instances.

And so wiring beans is essentially just giving one bean a reference to another bean that's in the Spring context.

### 2 ways of wiring beans
For each way, you simply add the instances to the Spring context first, then wire the beans after
- **wiring** - linking by directly calling the methods that create them
- **auto-wiring** - enable Spring to provide us a value through a method parameter

### Wiring
In this example, we have a HAS-A relationship between a `Person` and a `Parrot`. We want the person to store a reference to the parrot we have in our context. We do this by simply using a setter and pass in the value created by the method that creates the parrot bean.

When done this way, only one `Parrot` instance is actually created. If the `Parrot` isn't in the context yet, it creates the bean, adds it to the context, then returns it to the bean method of the person. If it is, then it simply uses the `Parrot` instance that is already in the context.
```java
@Configuration  
public class ProjectConfig {  
    @Bean  
    Parrot parrot() {  
        var p = new Parrot();  
        p.setName("Koko");  
        return p;  
    }  
  
    @Bean  
    Person person() {  
        var p = new Person();  
        p.setName("Ella");  
        p.setParrot(parrot());  
        return p;  
    }  
}
```

### Auto-wiring
#### Auto-wiring in `@Bean` methods
With auto-wiring, we don't need to explicitly call the `@Bean` method of the instance we want to link. We simply add it as a parameter and tell Spring to inject it with an instance that's already in the context. This is called Dependency Injection (DI) since it involves Spring setting a value for us for a specific parameter.

```java
@Configuration  
public class ProjectConfig {  
    @Bean  
    Parrot parrot() {  
        var p = new Parrot();  
        p.setName("Koko");  
        return p;  
    }  
  
    @Bean  
    Person person(Parrot parrot) {  
        var p = new Person();  
        p.setName("Ella");  
        p.setParrot(parrot);  
        return p;  
    }  
}
```

#### Using `@Autowired`
The more modern approach to defining and wiring beans, which is the most common in real-world projects, is to use stereotype annotations together with the `@Autowired` annotation. By using this annotation, you instruct Spring to inject the appropriate instances either in a field, a constructor, or through the setter with dependency injection. But ideally, you want Spring to inject it in the constructor.

1. **Injecting in fields**
Injecting instances into the fields is fine, but one thing you can't do is mark the field as `final`. This is the main reason why you want to use DI with constructors.
```java
@Component
public class Person {
	@Autowired
	private Parrot parrot;
}
```

2. **Injecting in the constructor (recommended)**
```java
@Component
public class Person {
	private final Parrot parrot;

	@Autowired // starting Spring v4.3, you can omit if you only have 1 constructor
	public Person(Parrot parrot) {
		this.parrot = parrot;
	}
}
```

3. **Injecting in setters (not recommended)**
```java
@Component
public class Person {
	private Parrot parrot;

	@Autowired
	public void setParrot() {
		this.parrot = parrot;
	}
}
```


#### Using `@Qualifier`
When you have two beans with the same type (when using `@Bean` beans), you can choose to distinguish between them with `@Primary`, and you can also be more explicit about which bean you want to be injected using `@Qualifier`.

```java
@Configuration
public class ProjectConfig {
	@Bean
	public Parrot parrot1() {
		return new Parrot("Parrot 1");
	}

	@Bean
	public Parrot parrot2() {
		return new Parrot("Parrot 2");
	}

	@Bean
	public Person person(@Qualifier("parrot2") Parrot parrot) {
		var p = new Person();
		p.setParrot(parrot); // sets Parrot 2
		return p;
	}
}
```

prev: [[2 - Defining Beans]]
next: [[4 - Abstractions]]