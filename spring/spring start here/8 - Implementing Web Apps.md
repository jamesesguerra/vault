### Dynamic Views
To make views dynamic, you need to send more information apart from the view name from the controller to the view resolver. You need to send data that will dynamically be displayed in the view.

1. Add the Thymeleaf templating engine via a dependency starter. This allows you to easily send data from the controller to the view and display the data in a specific way.
```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-thymeleaf</artifactId>  
</dependency>
```

2. Add the data you want the controller to send to the view using a `Model` parameter on the action method.
```java
@RequestMapping("/home")
public String home(Model page) {
	page.addAttribute("username", "Katy");  
	page.addAttribute("color", "red");  
	return "home.html";
}
```

3. Add the view to the `resources/templates` folder instead. This is because we're now using a templating engine.

4. Add an import in the HTML file to use Thymeleaf specific features.
```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

5. Use the `th` prefix along with the `${key}` syntax to access the data from the controller and dynamically change the view.
```html
<h1>Welcome!  
    <span th:style="'color: ' + ${color}" th:text="${username}"></span>  
</h1>
```

#### Getting data on the HTTP request
In most case, the way you'll send data from the client is through the following ways:

1. **query parameters**
To bind query parameters, you add a parameter to the action method and annotate it with `@RequestParam`. Query parameters are required by default, but you can make them optional. You can also omit them completely if their names match a DTO's name, like using `[AsParameters]` in .NET
```java
@RequestMapping("/home")
public String home(@RequestParam(required = false) String color, Model page) { ... }

public String products(Product product, Model page) { ... }
```

2. **path variables (route parameters)**
Unlike query parameters, path variables shouldn't be used with optional values.

```java
@RequestMapping("/home/{color}")
public String home(@PathVariable String color, Model page) { ... }
```

 3. **HTTP request headers**
4. **HTTP request body**

#### HTTP verb annotations
To use the same annotation for different HTTP verbs, use the `@RequestMapping` annotation. This defaults to matching GET requests. But if you want it to match to something else, you need to add parameters to the annotation.

```java
@RequestMapping("/products")
@RequestMapping(path = "/products", method = RequestMethod.POST)
```

To use dedicated annotations for HTTP verbs (recommended), use `@GetMapping`, `@PostMapping`, etc.

prev: [[7 - Spring Boot and Spring MVC]]
next: [[9 - Web Scopes]]