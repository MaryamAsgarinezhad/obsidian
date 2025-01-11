
Concurrency is having two tasks run in parallel on separate threads. However, asynchronous methods run in parallel but on the same 1 thread. How is this achieved? Also, what about parallelism?

- Concurrent and parallel are effectively the same principle as you correctly surmise, both are related to tasks being executed simultaneously although I would say that parallel tasks should be truly multitasking, executed "at the same time" whereas concurrent could mean that the tasks are sharing the execution thread while still appearing to be executing in parallel.
  
- Asynchronous methods aren't directly related to the previous two concepts, asynchrony is used to present the impression of concurrent or parallel tasking but effectively an asynchronous method call is normally used for a process that needs to do work away from the current application and we don't want to wait and block our application awaiting the response.

- For example, getting data from a database could take time but we don't want to block our UI waiting for the data. ==The async call takes a call-back reference and returns execution back to your code as soon as the request has been placed with the remote system==. Your UI can continue to respond to the user while the remote system does whatever processing is required, once it returns the data to your call-back method then that method can update the UI (or handoff that update) as appropriate.

From the User perspective, it appears like multitasking but it may not be.
It's probably worth adding that in many implementations an asynchronous method call will cause a thread to be spun up but it's not essential, it really depends on the operation being executed and how the response can be notified back to the system.

--------------

 - `boto3` is **synchronous**, meaning it blocks the execution of the program while performing network operations (like uploading to S3). This blocking behavior is not ideal when you want to perform non-blocking I/O operations (such as uploading data to S3 asynchronously) without slowing down the main processing flow of the application (like serving inference requests in Triton).
   
   In contrast, **`aiobotocore`** is an asynchronous version of `boto3`, designed specifically for **non-blocking** I/O operations. By using `aiobotocore`, you can handle the S3 upload in the background while allowing the rest of your code to continue executing (e.g., processing inference requests, generating speech).

-------
