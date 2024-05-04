
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