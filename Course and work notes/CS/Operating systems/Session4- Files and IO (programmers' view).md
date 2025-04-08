
![[Pasted image 20250111233609.png]]
Semaphore is a type of lock.

![[Pasted image 20250112000058.png]]
![[Pasted image 20250112000150.png]]

------

![[Pasted image 20250112165515.png]]

![[Pasted image 20250112165528.png]]


-----------------

![[Pasted image 20250112165609.png]]
![[Pasted image 20250112165704.png]]

![[Pasted image 20250112165807.png]]

---------

![[Pasted image 20250112165949.png]]![[Pasted image 20250112170100.png]]


![[Pasted image 20250112170505.png]]

--------

About files:

![[Pasted image 20250112170709.png]]

![[Pasted image 20250112170841.png]]


==Connecting all together:==
![[Pasted image 20250112171000.png]]

----

Focus of ==today's lecture==:
![[Pasted image 20250112171129.png]]

![[Pasted image 20250112171303.png]]


==stdout and stdin example:==
![[Pasted image 20250112171540.png]]
![[Pasted image 20250112171623.png]]
![[Pasted image 20250112172110.png]]

Pointer management:
![[Pasted image 20250112172341.png]]

-----

I/O design concepts:

![[Pasted image 20250112172502.png]]

---

Low-level I/O:

![[Pasted image 20250112172822.png]]
![[Pasted image 20250112180937.png]]

---

Why do we have high level file I/O?
![[Pasted image 20250112183504.png]]

Low level operations (syscalls) are applied directly to the kernel, but high level ones store files in user buffer, and otherwise applied to the kernel. Thus, high level operations are much faster. 

![[Pasted image 20250112183804.png]]


---------


![[Pasted image 20250112184614.png]]
![[Pasted image 20250112184721.png]]
