### What is Spring?
Spring is an **application framework**. Application frameworks are things that provide tools to implement common software functionalities for building an application. The programmer needs to decide what software capabilities they'll use to build an app.

Frameworks were invented after people started noticing that different applications had a lot of similar necessities, such as logging, error handling, authorization etc. So instead of reinventing the wheel for these functionalities every time, you can leverage what other people have created and maintained already.

Software capabilities include:
- logging
- transactions
- protection against common vulnerabilities
- communication
- caching

These capabilities are like the part of an iceberg that's hidden. What a user usually sees and interacts with is the **business logic**--code that implements what user expects from an application.

#### Spring Ecosystem
Spring is actually an **ecosystem** of frameworks.

1. **Spring Core**
- Spring context, aspects, configuration (foundational capabilities)
- enables Spring to manage instances of your app

2. **Spring MVC**
- web server to serve HTTP requests

3. **Spring Data Access**
- connection to DBs

4. **Spring Testing**

##### Spring Core
Spring works using **Inversion of Control (IoC)**. In this manner, you let Spring create dependencies instead of your application code. Spring context is like a DI container in C#.

##### Extending Spring Core
1. **Spring Data**
- less lines of boilerplate code for setting up a database connection
- more functionality for connecting to different SQL and NoSQL data sources

2. **Spring Boot**
- convention > configuration

next: [[2 - Defining Beans]]

