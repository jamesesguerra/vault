Clients applications such as browsers and mobile devices are usually the consumers of REST services. However, REST services can also be consumed by other backend components.

### 3 ways to consume REST with Spring

#### OpenFeign
OpenFeign is a tool offered by Spring Cloud. It's the recommended approach for calling other backend systems from an app.

To do this, you merely define an interface where you declare methods that consume REST endpoints. You annotate the methods to define the path, HTTP method, and eventually parameters, headers, and body of the request. This makes it so that you're essentially copying the method signature and annotation configuration of the REST endpoint you're consuming.

From there, OpenFeign implements that interface and provides the implementation as a bean in the Spring context. You can inject this bean anywhere you need it in your app.

**Case study:** You want to process a payment from a client, but you have to call an external payments service from your own service. This is what the payment service's action method looks like:

```java
@PostMapping("/payment")  
public ResponseEntity<Payment> createPayment(  
    @RequestHeader String requestId,  
    @RequestBody Payment payment  
) {  
    logger.info("Received payment with request ID " + requestId + "; Payment amount: " + payment.getAmount());  
  
    payment.setId(UUID.randomUUID().toString());  
  
    return ResponseEntity  
            .status(HttpStatus.OK)  
            .header("requestId", requestId)  
            .body(payment);  
}
```

![[openfeign-architecture.png]]

1. Add the OpenFeign dependency starter
```xml
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-openfeign</artifactId>  
    <version>4.2.0</version>  
</dependency>
```

2. Create the proxy interface also called the OpenFeign client. `@FeignClient` tells OpenFeign it has to provide an annotation for this contract. The `name` attribute of the annotation assigns a name to the proxy which OpenFeign internally uses, while the `url` attribute specifies the base URI of the request. 

   Each method you add to the interface is a separate method call.

**NOTE:** You can reference properties in a project's `application.properties` file with the `${property_name}` syntax.
```java
@FeignClient(name = "payments", url = "${name.service.url}")
public interface PaymentsProxy {
	@PostMapping("/payment")
	Payment createPayment(@RequestHeader String requestId, @RequestBody Payment payment);
}
```

3. Enable the OpenFeign functionality and tell it where to find the proxies by annotating the main application class with `@EnableFeignClients`
```java
@SpringBootApplication
@EnableFeignClients(basePackages = "org.esguerra.james.proxy")
public class Application { }
```

4. Create the controller that injects the proxy to call on the service. It's important to note that you don't return a `ResponseEntity` in this case, just the data type itself. We pass on the request ID request header to the proxy.
```java
@RestController
public class PaymentsController {
	private final PaymentsProxy paymentsProxy;

	public PaymentsController(PaymentsProxy paymentsProxy) {
		this.paymentsProxy = paymentsProxy;
	}

	@PostMapping("/payment")
	public Payment createPayment(@RequestBody Payment payment) {
		var requestId = UUID.randomUUID().toString();
		return paymentsProxy.createPayment(requestId, payment);
	}
}
```


## RestTemplate
a well-known tool used since Spring 3; OpenFeign is better

## WebClient
an alternative to RestTemplate that uses reactive programming




