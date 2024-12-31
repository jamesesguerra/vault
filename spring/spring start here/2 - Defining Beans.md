> **Why do we need to add beans?** So that we let Spring manage the creation and lifecycle of dependencies for us, and so that it can augment these dependencies with extra features

### Adding Beans using `@Bean`
1. Install the `spring-context` dependency

2. Create an instance of the Spring context
```java
var context = new AnnotationConfigApplicationContext();
```

3. Define a configuration class
Configuration classes are annotated with `@Configuration`. They provide several spring-related configurations for the project
```java
import org.springframework.context.annotation.Configuration;

@Configuration
public class ProjectConfig { }
```

4. Create a method that returns the bean, and annotate the method with `@Bean`. Method names should be nouns, as they reflect the "thing" you are getting from the context.
```java
import org.springframework.context.annotation.Bean;

@Configuration
public class ProjectConfig {
	@Bean
	Parrot parrot() {
		return new Parrot("Koko");
	}
}
```

5. Change the instantiation of the Spring context to use the project configuration class
```java
var context = new AnnotationConfigApplicationContext(ProjectConfig.class);
```

6. Verify that the beans were added to the container
```java
var koko = context.getBean(Parrot.class);
System.out.println(koko.getName());
```

#### Beans with the same type
If you have beans that have the same type, Spring wouldn't be able to tell which bean you want to return from the context. In these cases, you should use the name of the method as an argument to `getBean`. You can also provide a custom name different from the method. In which case, you can only retrieve that bean from the context using that name, and not the method anymore.

```java
@Bean(name = "Koko")
Parrot parrot1() {
	return new Parrot("Koko");
}

@Bean
Parrot parrot2() {
	return new Parrot("Miko");
}

var miko = context.getBean("parrot2", Parrot.class);
```

Although, you also have the choice to add a primary bean with the `@Primary` annotation as well. When you have multiple beans with the same type and don't specify which one you want, Spring defaults to the primary bean.
```java
@Bean
@Primary
Parrot parrot1() {
	return new Parrot("Koko");
}
```

### Adding Beans using Stereotype Annotations
Spring offers multiple stereotype annotations. The most basic one is `@Component`.

With stereotype annotations, you add the annotation above the class for which you need an instance of in the Spring context. When doing so, you've marked the class as a *component*.

One thing to note is that when using this method, you only have access to the instance after it's created, and so it's your duty to assign any values to the object instances you get from the Spring context. This also means that you can only have one instance of a certain type in the container.

1. Mark a class as a component which you want to add an instance of to the Spring context
```java
@Component
public class Parrot { }
```

2. Use the `@ComponentScan` annotation to tell Spring where to look for classes annotated with `@Component`. Now, your project configuration class doesn't need all the bean methods.
```java
@Configuration
@ComponentScan(basePackages = "main")
public class ProjectConfig { }
```

#### Using `@PostConstruct` with Stereotype Annotations
Using the `@Bean` annotation, we had control over how the instance was created. With stereotype annotations, we can only access it after it has been created. If we want to do something right after Spring creates the bean, we can use the `@PostConstruct` annotation to annotate a method we want to call after the bean of the class has been created. 

1. Add the Maven dependency
```xml
<dependency>  
    <groupId>one.gfw</groupId>  
    <artifactId>javax.annotation-api</artifactId>  
    <version>1.3.2</version>  
</dependency>
```

2. Use the `@PostConstruct` annotation
```java
public class Parrot() {
	@PostConstruct
	public void init() {
		this.name = "Koko";
	}
}
```

### Programmatically Adding Beans
The thing you can't do with the previous two approaches is add beans conditionally. You might want to add beans conditionally based on the app's current configuration. You can do this by using a method of the context called `registerBean`.

In this case, you wouldn't need the `@Component` and `@ComponentScan` annotations anymore. You just need the `@Configuration` annotation on your project config class.

```java

public class Main(String[] args) {
	var parrot = new Parrot();  
	parrot.setName("Koko");  
	  
	Supplier<Parrot> parrotSupplier = () -> parrot;  
	context.registerBean("parrot1", Parrot.class, parrotSupplier);  
	  
	var suppliedParrot = context.getBean("parrot1", Parrot.class);  
	System.out.println(suppliedParrot.getName());
}
```

prev: [[spring/spring start here/1 - Introduction|1 - Introduction]]
next: [[3 - Wiring Beans]]