### Interfaces
The main tool we use to introduce abstraction is an **interface**. Interfaces are abstract structures that you use to declare a specific responsibility in the system. They have no implementation, just a statement of what *needs* to be implemented for whoever is going to implement that responsibility. In other words, interfaces say what needs to happen, and objects say how they should happen.

Interfaces can also be thought of as things you can use to ask for what you need. If a printer module needs a way to sort a list before it prints something, it can ask for the printer interface to delegate that responsibility to some object that can give the printer what it needs.

### Benefits of interfaces
They provide you with less coupling. If you want code that's easy to maintain and extend, you want the ability to change the implementation of a module without affecting higher-level modules that depend on it. By doing so, you introduce less changes and there are less stuff to recompile.

### 3-Tier Architecture
An layered architectural pattern used for creating highly maintainable and scalable applications
1. **presentation layer (controllers)** - entry point; handles the HTTP request / response directly; delegates work to the service layer that will eventually return the final response
2. **business logic layer (services)** - implements the business logic for the app's use cases; calls repository methods to collect data, transform if needed, and return
3. **data access layer (repositories)** - interacts directly with the database to fetch / change data

### Case study
Example: You're working on a feature of an app that allows users to publish a comment. When a comment is published, this comment gets saved into a database, and sent to an email set in the app's configuration.

1. The first thing you want to do is identify the responsibilities that need to be carried out.
- the first one is the **service**, the high-level object that implements the use case; we can call it `CommentService`
- the next one is the **repository**, the object that directly communicates with the database, we can call it `CommentRepository`
- lastly, the **proxy**, the object that establishes a communication with something outside the app, we call it `CommentNotificationProxy`

![[Pasted image 20241227231655.png]]

2. Create interfaces for the `CommentService` to depend on. Again, we don't want the `CommentService` to depend on concrete implementations of the repository and proxy. So that in the future, in case we want to use something other than a database, or a push notification proxy instead of an email notification, we don't have to change anything in the `CommentService`.

![[Pasted image 20241227231834.png]]

3. Create a POJO (plain old Java object) to represent the comment. POJOs are used to model the data that the app uses.
```java
public class Comment {
	public String author;
	public String text;
}
```

4. Decide on what objects need to be added to the Spring context. We add them to the container if (1) the object has a dependency that needs to be injected by Spring and (2) the object is a dependency itself. This means that we only add stereotype annotations for the classes that Spring needs to create instances of and add them to the context, so it wouldn't make sense to add interfaces.

5. Create the classes and interfaces. For service classes, we use the `@Service` annotation. And because it only has 1 constructor, we don't need to add `@Autowired` explicitly to wire the beans up.

```java
@Service
public class CommentService {
	private final CommentRepository commentRepository;
	private final CommentNotificationProxy notificationProxy;

	public CommentService(CommentRepository commentRepository, CommentNotificationProxy notificationProxy) {
		this.commentRepository = commentRepository;
		this.notificationProxy = notificationProxy;
	}

	public void publishComment(Comment comment) {
		commentRepository.storeComment(comment);
		notificationProxy.sendComment(comment);
	}
}
```

6. For repositories, we use the `@Repository` annotation.
```java
@Repository
public class DBCommentRepository implements CommentRepository {
	@Override
	public void storeComment(Comment comment) {
		// communicate with the DB
	}
}
```

7. For everything else, we use the generic `@Component` annotation
```java
@Component
public class EmailCommentNotificationProxy implements CommentNotificationProxy {
	@Override
	public void sendComment(Comment comment) {
		// send email notification
	}
}
```

And done! By using interfaces with dependency injection, we let Spring do the work of creating instances for us. We no longer have to manage the creation of dependencies ourselves, which can be a tedious task that's hard to manage.


### Using `@Primary` and `@Qualifier`
When you 2 beans with the same type, an exception will be thrown if you don't make a distinction which one should be selected. If you simply want one to be selected over others when the distinction can't be made, use `@Primary`

```java
@Component
@Primary
public class EmailCommentNotificationProxy implements CommentNotificationProxy {} 
```

But if you want to make the distinction by a qualifier instead, use `@Qualifier`.
```java
@Component
@Qualifier("PUSH")
public class PushCommentNotificationProxy implements CommentNotificationProxy {}
```

```java
@Service
public class CommentService {
	// ommitted fields

	public CommentService(@Qualifier("PUSH") CommentNotificationProxy proxy) { }
}
```

prev: [[3 - Wiring Beans]]
next: [[5 - Bean Scopes and Life Cycles]]