> **REST services** offer access to functionality the server exposes through endpoints that a client can call

### REST and MVC
Implementing REST services can be done just how MVC web apps were implemented because they follow the same process. A controller is mapped to an HTTP method and a path. This controller can retrieve a model if needed. They differ in what they return to the client. In MVC, the dispatcher servlet looks delegates to the view resolver to return the view based on the view name returned by the controller. In REST services, **the view resolver disappears**. The dispatcher servlet merely returns directly to the client whatever the controller returns.

![[rest-mvc-architecture.png]]

### Communication Issues
REST is not perfect as it could run into issues brought by:
- networking failure
- controller action taking too long to complete
- too many concurrent calls on one controller action
- sending too much data in a response (sending more than a few MBs)

### Implementing REST Endpoints
REST endpoints look just like web endpoints except they include a `@ResponseBody` annotation. This annotation tells the dispatcher servlet that it won't return a view, and it will return the data sent directly in the HTTP response.

```java
@Controller
public class HelloController {
	@GetMapping("/hello")
	@ResponseBody
	public String sayHello() {
		return "hello!";
	}
}
```

However, this repetition of adding `@ResponseBody` to every controller action becomes cumbersome. This is what the `@RestController` endpoint is for -- it tells the dispatcher servlet that it's an MVC controller and all its action methods are REST endpoints.

```java
@RestController
public class HelloController { ... }
```

#### Sending objects in the response
To send objects in the response body, you merely need to return it from the controller action. When you model the data transferred between two apps, we name this object a **data transfer object (DTO)**. It's important to note that the DTO needs public getters and setters (or public attributes) so that Spring can serialize the response.

```java
public class Country {  
    private String name;  
    private int population;  
  
    public static Country of(String name, int population) {  
        var country = new Country();  
        country.name = name;  
        country.population = population;  
  
        return country;  
    }

	// omitted getters and setters
}
```

```java
@GetMapping("/fr")
public Country france() {
	return Country.of("France", 35000);
}
```

You can also send a list of objects.
```java
@GetMapping("/all")
public List<Country> all() {
	return List.of(Country.of("France", 5), Country.of("Philippines", 30));
}
```

#### Setting the response status and headers
The easiest and most common way to do this is to use the `ResponseEntity` class. It allows you to specify the response body, status, and headers.

```java
@GetMapping("/fr")  
public ResponseEntity<Country> france() {  
    var c = Country.of("France", 25_000);  
  
    return ResponseEntity  
            .status(HttpStatus.OK)  
            .header("continent", "Europe")  
            .body(c);  
}
```

##### Handling Exceptions
But what would you do if the service the controller action uses can throw an exception?

(1) One way you can handle this is catching the exception in the action method and throwing a different configuration of the response when the error occurs.
```java
@PostMapping("/payment")  
public ResponseEntity<?> makePayment() {  
    try {  
        var paymentDetails = paymentService.processPayment();  
  
        return ResponseEntity  
                .status(HttpStatus.ACCEPTED)  
                .body(paymentDetails);  
    } catch (NotEnoughMoneyException e) {  
        ErrorDetails errorDetails = new ErrorDetails();  
        errorDetails.setMessage("Not enough money!");  
  
        return ResponseEntity  
				.badRequest()  
                .body(errorDetails);  
    }  
}
```

(2) Another, more preferred way to handle exceptions is to use a **REST controller advice** -- an aspect that intercepts exceptions thrown by controller's actions and applies custom logic according to the intercepted exception.

This is more preferred as the controller need only handle the happy path. The error path is handled by another component.
```java
@PostMapping("/payment")  
public ResponseEntity<PaymentDetails> makePayment() {  
    var paymentDetails = paymentService.processPayment();  
  
    return ResponseEntity  
            .status(HttpStatus.ACCEPTED)  
            .body(paymentDetails);  
}
```

To create a REST controller advice, mark the class with the `@RestControllerAdvice` annotation. You then specify what exceptions trigger a controller's advice method using `@ExceptionHandler` with the corresponding exception class.
```java
@RestControllerAdvice  
public class ExceptionControllerAdvice {  
  
    @ExceptionHandler(NotEnoughMoneyException.class)  
    public ResponseEntity<ErrorDetails> exceptionNotEnoughMoneyHandler() {  
        var errorDetails = new ErrorDetails();  
        errorDetails.setMessage("Not enough money!");  
  
        return ResponseEntity  
                .badRequest()  
                .body(errorDetails);  
    }  
}
```

**NOTE:** The exception advice method can also take in the exception as a parameter. Spring passes the exception from the controller to the advice, and you can use any relevant details from the exception instance in the response.

![[exception-controller-advice.png]]

prev: [[9 - Web Scopes]]
next: [[11 - Consuming REST Services]]