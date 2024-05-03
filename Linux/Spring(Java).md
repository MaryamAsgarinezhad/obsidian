
- @PostMapping("/secureLink"):

    This annotation indicates that the method annotated with @PostMapping will handle HTTP POST requests to the /secureLink endpoint.


- PreAuthorize("hasAuthority('USER') or hasAuthority('ADMIN')"):

    This annotation is part of Spring Security and is used for method-level security.
    It specifies that access to the method is restricted based on the expression provided within the annotation.
    Due to this issue, we have to login to the Admin or User users to be able to call that specific method
----------------------------------------

![[Pasted image 20240503195151.png]]TokenSingleUrlModel is the object that stores the input arguments we define in our request's body. (Ctrl + Enter)

![[Pasted image 20240503195213.png]]Here is all the arguments you need to define in your http request's body as the function input  arguments