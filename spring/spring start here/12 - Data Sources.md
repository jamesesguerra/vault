> a **data source** is a component that manages connections to the server handling the database (the DBMS)

A data source uses a **JDBC driver** to get the connections it manages. It improves the efficiency of the app by reusing database connections and requesting for new ones only when needed. It also makes sure to close the connection when it releases them. The most commonly used data source today is HikariCP.

![[jdbc-architecture.png]]

### JDBC
**Java Database Connectivity** offers you a way to connect to a DBMS to work with a database. However, the JDK doesn't give you a specific implementation for working with a particular DBMS technology ie Oracle, SQL Server. What it does provide you are abstractions for working with a relational database. In other words, they're just interfaces.

To add these implementations for these abstractions, you add a runtime dependency called the **JDBC driver.** These enable the app to connect to specific technologies, and therefore these would come from other vendors. So if you needed your app's data source to create and manage connections to a MySQL DB, you need a MySQL JDBC driver.

### Using `JdbcTemplate`
While the data source can manage connections efficiently, using JDBC classes provided by the JDK isn't a comfortable way to work with persisted data.

`JdbcTemplate` is the go-to tool to use to implement a persistence layer when you don't want your app to have any other dependency.

1. Add the dependencies. In this case, we're adding the Spring Boot JDBC starter and the H2 dependency for an in-memory database and an H2 JDBC driver. Spring Boot also automatically adds a configured data source and a `JdbcTemplate` instance
```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-jdbc</artifactId>  
</dependency>  
  
<dependency>  
    <groupId>com.h2database</groupId>  
    <artifactId>h2</artifactId>  
    <scope>runtime</scope>  
</dependency>
```

 Also, add a `schema.sql` file under the resources folder of the Maven project. The queries in this file will get run when the app starts.

2. Code the repository logic. `JdbcTemplate` exposes an `update` method that you can use to execute any query for data mutation: `INSERT`, `UPDATE`, or `DELETE`
```java
@Repository  
public class PurchaseRepository {  
    private final JdbcTemplate jdbc;  
  
    public PurchaseRepository(JdbcTemplate jdbc) {  
        this.jdbc = jdbc;  
    }  
  
    public void storePurchase(Purchase purchase) {  
        var sql = "INSERT INTO purchase VALUES (NULL, ?, ?)";  
  
        jdbc.update(sql, purchase.getProduct(), purchase.getPrice());  
    }  
}
```

3. Call the repository methods that implement the REST endpoints' actions
