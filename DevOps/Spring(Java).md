Wrapping a function means creating another function that adds some additional functionality around the original function without modifying its core behavior. This concept is often used in programming to extend or customize the behavior of existing functions without changing their implementation.

- @PostMapping("/secureLink"):

    This annotation indicates that the method annotated with @PostMapping will handle HTTP POST requests to the /secureLink endpoint.


- PreAuthorize("hasAuthority('USER') or hasAuthority('ADMIN')"):

    This annotation is part of Spring Security and is used for method-level security.
    It specifies that access to the method is restricted based on the expression provided within the annotation.
    Due to this issue, we have to login to the Admin or User users to be able to call that specific method
----------------------------------------

![[Pasted image 20240503195151.png]]TokenSingleUrlModel is the object that stores the input arguments we define in our request's body. (Ctrl + Enter)

![[Pasted image 20240503195213.png]]Here is all the arguments you need to define in your http request's body as the function input  arguments

--------------------------
spring boot services
![[Pasted image 20240504125149.png]]
----------------------------

Spring RestController annotation is used to create RESTful web services using Spring MVC.

The command `mvn package -DskipTests` is used in a Maven-based Java project to package the project without running any tests.
- This command is useful when you want to quickly build the project without waiting for tests to run, which can save time during development or troubleshooting.
- However, skipping tests should be done with caution, as it bypasses a key part of the build process intended to verify the integrity and functionality of the code.
- - **`mvn package`**:
    - This phase compiles the source code, runs tests, and packages the code and resources into a JAR, WAR, or other package type.

-------------------------------

Autowired and Bean annotations:

### Definition of a Bean

A bean in Spring is essentially a Java object that is instantiated, assembled, and managed by the Spring container. Beans are the building blocks of a Spring application and represent the objects that form the backbone of your application.

### Declaring a Bean

Beans are typically declared in one of the following ways:

1. **Using Annotations**: The `@Component`, `@Service`, `@Repository`, and `@Controller` annotations can be used to declare a bean. These annotations are used on class-level to indicate that a class is a Spring-managed bean.

```java
import org.springframework.stereotype.Component;

@Component
public class MyBean {
    // bean implementation
}
```

2. **Using Java Configuration**: You can use `@Configuration` classes and `@Bean` methods to declare beans. This approach is more explicit and often preferred for complex configurations.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MyBean myBean() {
        return new MyBean();
    }
}
```

3. **Using XML Configuration**: Though less common in modern Spring applications, you can still declare beans in an XML configuration file.

```xml
<bean id="myBean" class="com.example.MyBean"/>
```


### Bean Scope

Spring beans can have different scopes, which define the lifecycle and visibility of the beans. Common scopes include:

- **Singleton**: A single shared instance per Spring IoC container (default scope).
- **Prototype**: A new instance each time a bean is requested.
- **Request**: A single instance per HTTP request (web applications only).
- **Session**: A single instance per HTTP session (web applications only).
- **Global Session**: A single instance per global HTTP session (portlet-based web applications).


The `@Autowired` annotation in Spring Framework is used for automatic dependency injection. This means that Spring will automatically provide the required dependencies for a bean by matching and injecting the appropriate bean from the Spring Application Context.

![[Pasted image 20240714150643.png]]

-------------------------

- **Externalized Configuration**: Using `@Value` helps externalize configuration, making the application easier to manage and configure without changing the source code.
- **Property Placeholders**: The `${...}` syntax is used to refer to property placeholders that Spring will resolve at runtime.

```java
import org.springframework.beans.factory.annotation.Value;

public class CacheConfig {

    @Value("${cacheNodes}")
    public String cacheNodes;
}
```

"cacheNodes" is defined in the application properties file.