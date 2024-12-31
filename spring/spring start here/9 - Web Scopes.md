Aside from being able to declare a bean as a singleton or a prototype, you web apps can also use other bean scopes relevant only to web apps, these are called **web scopes**:

## request scope (tied to a request)
For request-scoped beans, Spring gives you a way to manage the bean life cycle in relation to HTTP requests. A new instance is created for every HTTP request, and the app can use that instance only for the request that created it. Any new HTTP request (from the same or other clients) created and uses a different instances.

**why use it?** - use request-scoped beans if you only want an instance and its data to be available in memory for as long as it takes a certain HTTP request to complete e.g. keeping a user's credentials in a `LoginProcessor` for as long as it takes the login request to finish

**things to consider**
- many instances of the bean will likely be created for the duration of the app
- many instances usually isn't a problem as they're short-lived
- make sure not to implement any time-consuming logic in the constructor or a `@PostConstruct` method
- you can use the instance's attributes to store data related to the request because it's thread-safe
- no need to use unnecessary thread-synchronization techniques
- doesn't get stuck in a singleton bean (unlike prototype beans)

```java
@Component
@RequestScope
public class LoginProcessor { }
```

## session scope (tied to a session)
For session-scoped beans, an instance is created and kept in memory for the duration of the HTTP session. This approach allows you to store information about what users do while they're surfing through the pages of the app. You can think of this as **1 new bean per browser using the app**.

**why use it?** - use sessions-scoped beans if you want to persist data that's needed for a more extended period than a single HTTP request e.g. details of an authenticated user, a shopping cart

**things to consider**
- they have a longer life than request-scoped beans, are less frequently GC'ed, and therefore, you should avoid keeping too much data on the session
- race-conditions can occur as multiple requests share the same session scope
- they're a way to share data among requests, but you should still keep HTTP requests independent from one another
- the instance is only created when a user visits a page that reference a session-scoped instance

```java
@Service
@SessionScope
public class LoggedInUserManagementService { }
```

### application scope (tied to the servlet)
The application scope is similar to the singleton except that you can't have multiple instances of the same type in the container. Also, application-scoped beans are tied to the servlet while singleton-scoped ones are tied to the container.

```java
@Service  
@ApplicationScope  
public class LoginCountService {
```

**why use it?** - use application-scoped beans if you need beans that are explicitly tied to the lifecycle of a web application context

**things to consider**
- it's generally recommended to just stick to singleton beans 
- use a database instead for storing long-lived data

prev: [[8 - Implementing Web Apps]]
next: [[10 - Implementing REST Services]]