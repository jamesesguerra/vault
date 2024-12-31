> **web apps** - any apps you use by accessing a web browser

**Two main designs for implementing a web app:**
1. apps where the backend provides a fully prepared view in response to a client's request (traditional); browser directly interprets the data received and displays it
2. apps using frontend-backend separation, backend only serves raw data and the frontend instructs the browser how to display it

### Servlets
A web browser uses HTTP to communicate with your server. What your app needs to know how to do is to translate the HTTP request and response to a Java app. This something is a **servlet container**: a translator of HTTP messages for your Java app. In this manner, your app doesn't have to take care of implementing the communication layer.

On the other hand, **servlets** are nothing more than Java objects that directly interact with the servlet container. When an HTTP request is received by the servlet container, it translates the message, and passes the object as a parameter to the servlet's object method.

When using Spring, you don't need to know how servlets work since Spring takes care of this for you. It creates 1 servlet for all the paths. But you do need to know that **servlets are the entry point to your app's logic**. Its how the request enters your app and how the response goes through Tomcat and back to the client.

![[servlet-infrastructure.png]]

### Enter Spring Boot
To create a Spring app, you need to configure a servlet container, create a servlet instance, and make sure to configure the instance such that Tomcat calls it for any client request. This is a pain in the ass, which is why Spring Boot was created. It offers:
- **simplified project creation** - a project initialization service can be used to configure an empty Spring app
- **dependency starters** - configures must-have dependencies by default
- **autoconfiguration** - based on dependencies; less code to write

#### Spring Initalizr (project initialization service)
After creating a project with Spring Initializr, you'll see several key configurations:

1. **the Spring app main class**
The main class of a Spring Boot app is annotated with the `@SpringBootApplication` annotation.
```java
@SpringBootApplication
public class Application {}
```

2. **Spring Boot POM parent**
One of the things this parent POM node provides is compatible versions for the dependencies you'll add to your project. This gives the benefit that in most cases, you won't need to specify a version for a dependency, you just let Spring Boot choose a compatible version.

3. **Spring Boot Maven Plugin**
This plugin is responsible for adding part of the default configurations you'll observe in the project.

4. **Properties file**
The last thing Spring Initializr added to the project is `application.properties` inside the `resources` folder. This file configures property values your app needs during execution.

#### Dependency Starters
Dependency starters are like groups of dependencies that are compatible with one another, with an intention of serving a specific capability. Without dependency starters, you'd have to download each dependency for the kind of app you're building and ensure they're compatible with one another. But with a dependency starter, such as `spring-boot-starter-web` (they usually start with `spring-boot-starter`), it contains all the needed dependencies for the web specific capability. These dependencies include stuff like Tomcat, Spring Context, Spring AOP, etc.

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
</dependency>
```

---

### Spring MVC
To add a web page to your app, you follow two steps:
1. Write an HTML document with the content you want to be displayed by the browser. The folder which you want to add it to is `resources/static`.

2. Write a controller with the action for the web page you created. Controllers are components of the web app that contain methods (also called actions) executed for a specific HTTP request.

To create a controller, you add the `@Controller` annotation to a class. This is a stereotype annotation much like `@Service`. You then use the `@RequestMapping` annotation to associate a method / action to an HTTP request path. You then return the name of the HTML file as a string.

```java
@Controller
public class MainController {

	@RequestMapping("/home")
	public String home() {
		return "home.html";
	}
}
```

#### MVC Architecture

![[spring-mvc-architecture.png]]

prev: [[6 - Aspects]]
next: [[8 - Implementing Web Apps]]