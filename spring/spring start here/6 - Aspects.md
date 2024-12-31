> **aspects** - a powerful technique also supported by IoC, aspects are a way the framework intercepts method calls and possibly alters the execution of methods

### Aspects
Simply put, aspects are pieces of logic that the framework executes when you call specific methods of your choice.

When designing the aspect, you need to specify the following:
- **aspect** - the logic you want Spring to execute when you call certain methods
- **advice** - when the logic should be executed e.g. before or after the method is called
- **pointcut** - which method the framework needs to intercept and execute the aspect for them

### Aspects as middleware in .NET
Aspects are like middleware in .NET in that they intercept code to handle cross-cutting concerns such as logging and error-handling. They're both ways to separate code that isn't part of the business logic into a centralized place. The difference is that .NET middleware works at a higher-level, at the request-response cycle of HTTP messages, whereas aspects work at a more **granular** level, that is, at a method-level kind of interception.

### Using Aspects
To use aspects, you need to let Spring manage the objects for which you want to apply aspects to. The bean that declares the method being intercepted is called the **target object**.

![[aspect-terminology.png]]

#### Weaving
When you apply an aspect to a target object, and request it from the Spring context, Spring doesn't directly give you a reference to the bean. Instead, it gives you a **proxy** that calls the aspect logic, and then delegates the method call to the actual method. You now receive the proxy instead of the real bean every time you request it either using `getBean` or via DI. This approach is called **weaving**.

#### Case study
You have an application that implements multiple use cases in its service classes. New regulations require you to log the time it started and ended for each use case.

**Implementing a simple aspect**
To create an aspect, you follow these steps:
1. Enable the aspect mechanism in the app by annotating the configuration class with the `@EnableAspectJAutoProxy` annotation
```java
@Configuration
@ComponentScan(basePackages = { "services" })
@EnableAspectJAutoProxy
public class ProjectConfiguration { }
```

2. Create a class that defines the aspect with `@Aspect`, and add an instance of this class to the Spring context
```java
@Component
@Aspect
public class LoggingAspect { }
```

3. Use an advice annotation to tell Spring when and which method calls to intercept.
The `@Around` annotation tells Spring which method calls to intercept. The syntax inside is called AspectJ. 
```java
@Aspect
public class LoggingAspect {

	@Around("execution(* services.*.*(..))")
	public void log() {  
	}
}
```

4. Implement the aspect logic
The `ProceedingJoinPoint` represents the intercepted method, and calling `joinPoint.proceed()` essentially executes the intercepted method. You have the choice not to even call the intercepted method e.g. if the user isn't authorized to do certain things.
```java
@Around("execution(* services.*.*(..))")
public void log(ProceedingJoinPoint joinPoint) throws Throwable {  
	logger.info("Method will execute");  

	joinPoint.proceed();  

	logger.info("Method executed");  
}
```

##### Obtaining Method Names and Parameters
Aspects are more powerful in that they can not only intercept methods to add additional behavior, but they can also gain access to the method name, parameter, and arguments of the intercepted method. They can do this because again, the `joinPoint` parameter represents the intercepted method.

```java
@Around("execution(* services.*.*(..))")  
public Object log(ProceedingJoinPoint joinPoint) throws Throwable {  
    var methodName = joinPoint.getSignature().getName();  
    var arguments = joinPoint.getArgs();  
  
    logger.info("Method " + methodName +  
            " with parameters " + Arrays.asList(arguments) +  
            " will execute");  
  
    var returnValue = joinPoint.proceed();  
  
    logger.info("Method executed and returned " + returnValue);  
    return returnValue;  
}
```

They can do even more things, such as alter the parameters passed to the intercepted method, and alter the return value returned to the callee, in this case, the `Main` method.

Calling `joinPoint.proceed()` doesn't alter the arguments in any way. But calling it *with* arguments will alter the arguments.

```java
@Around("execution(* services.*.*(..))")  
public Object log(ProceedingJoinPoint joinPoint) throws Throwable {  
    var arguments = joinPoint.getArgs();  
    logger.info(arguments[0].toString());  
  
    var alteredComment = new Comment("James E", "Altered comment");  
    Object[] newArguments = {alteredComment};  
  
    var returnValue = joinPoint.proceed(newArguments);  
  
    logger.info("Method executed and returned " + returnValue);  
    return "ALTERED RETURN VALUE";  
}
```

##### Aspecting Methods with Annotations
As an alternative to using the convoluted AspectJ syntax to tell Spring which method calls to intercept via it's package, method name, etc., you can also use annotation syntax and annotations. 

1. Create the annotation you'll use to mark methods you want to aspect. It's important to add the `@Retention(RetentionPolicy.RUNTIME)` annotation since by default, Java annotations can't be intercepted at runtime.

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ToLog {}
```

2. Mark the methods you want to intercept with the custom annotation.
```java
public class CommentService {

	@ToLog
	public String publishComment(Comment comment) { ... }
}
```

3. Use the following AspectJ pointcut expression syntax with the `@Around` annotation of the aspect.
```java
@Around("@annotation(ToLog)")
public void log(ProceedingJoinPoint joinPoint) throws Throwable { ... }
```

### Advice Annotations
The `@Around` annotation gives the most flexibility in calling the aspect method in that you can do things before or after the intercepted method is called. Although, it's better to use less-powerful but simpler advice annotations if you don't need the extra functionality.

- `@Before` - executes the aspect logic before the execution of the intercepted method
```java
@Before("@annotation(ToLog)")  
public void log() {  
    logger.info("Logging something before executing the method");  
}
```

- `@AfterReturning` - executes the aspect logic after the intercepted method successfully returns, and provides the return value as a parameter to the aspect method
```java
@AfterReturning(value = "@annotation(ToLog)", returning = "returnedValue")  
public void log(Object returnedValue) {  
    logger.info("Method returned " + returnedValue);  
}
```

- `@AfterThrowing` - executes the aspect logic if the intercepted method throws an exception, and provides the exception instance as a parameter to the method
```java
@AfterThrowing(value = "@annotation(ToLog)", throwing = "exception")  
public void log(Exception exception) {  
    logger.info("Exception caught " + exception.getMessage());  
}
```

- `@After` - executes the aspect logic after the intercepted method returns, regardless if it successfully returns or throws an exception
```java
@After("@annotation(ToLog)")  
public void cleanup() {  
    logger.info("Cleaning up resources...");  
}
```

### Aspect Execution Chain
When multiple aspects are weaved to the same method, there might be some overlap for when they are executed. For example, if you have two aspects:
- `SecurityAspect` - intercepts the method, validates parameters, decides whether to further delegate to the intercepted method
- `LoggingAspect` - logs before and after the execution of the intercepted method

There's some overlap between *when* the aspects are executed. That is, for the same method, 2 aspects are planning to execute their logic before they delegate. And when you have two aspects weaved like this, they need to execute one after the other. You can have `SecurityAspect` run first and delegate to `LoggingAspect`, or the other way around. The important thing is to create an execution chain.

#### Order of Execution
The order of aspect execution matters in some cases. For this case, if `SecurityAspect` runs before `LoggingAspect`, and security restrictions don't allow it to be further delegated, `LoggingAspect` won't ever run. And if the expectation is to always log the calls, even those that were rejected due to security reasons, this isn't the way to go, `LoggingAspect` must run before `SecurityAspect`.

![[aspect-execution-chain.png]]

To add order to the aspect execution, use the `@Order` annotation and give it a number that represents an ordinal number. The lower the number, the earlier it gets executed. For aspects that have the same order, their execution is left to Spring. Spring also prioritizes aspects that have an `@Order` annotation over ones that don't.

```java
@Component
@Aspect
@Order(1)
public class LoggingAspect { ... }
```

```java
@Component
@Aspect
@Order(2)
public class SecurityAspect { ... }
```

prev: [[5 - Bean Scopes and Life Cycles]]
next: [[7 - Spring Boot and Spring MVC]]


