![[Pasted image 20250101112736.png]]
Gist of previous sessions:

![[Pasted image 20250101114050.png]]
![[Pasted image 20250101114228.png]]
![[Pasted image 20250101114300.png]]


**Virtual Address space and translators**:
![[Pasted image 20250101114439.png]]


Translators are part of a **memory management unit**, but the operating system is responsible for configuring these things by configuring **page tables**.

So page tables are stored in the operating system and only accessible to that.

![[Pasted image 20250101115025.png]]

A process has its own threads, address space, sockets, and files; while a thread has its own SP, PC, and regs.

To make sure that the **user code cannot alter page tables** we have dual mode:
- Dual mode is a bit set in the process. (==Mode bit==)
- We need to be carefully control the transition between user mode and kernel mode. For example one thing that gets a process into kernel mode is **device drivers**:
![[Pasted image 20250101120733.png]]


----------------

This session:
![[Pasted image 20250101120911.png]]


Concurrency vs parallelism: overlapping, simultaneous

Thread **usecases**:
![[Pasted image 20250101121730.png]]


Some common terms:

- Multiprocessing: Multiple CPUs acting together on a same task (**parallelism**)
- Multiprogramming: Multiple jobs or processes, not necessarily running simultaneously (**concurrency**)
- Multithreading: Multiple thread in one or more processes (**scheduler runs threads in any order, maybe to completion or time slice**)

![[Pasted image 20250101124220.png]]

The moment we start thinking about threads and concurrent systems, ==we then have to start thinking about correctness, regardless of what the scheduler decides.== Because the scheduler does not design this itself.

- There is a possibility of overlap in parallelism.
- If you have more than one core in work, you can have parallelism on multiple processes and even thread. 

![[Pasted image 20250101141413.png]]

-------

Blocked threads (Async approach):

![[Pasted image 20250101141702.png]]
![[Pasted image 20250101141733.png]]

---------------------

![[Pasted image 20250101141958.png]]
**System call** (**through OS libraries**) is the bridge between user and OS

![[Pasted image 20250101142334.png]]

pthreads are functions wrapping in the system calls.

-----------

![[Pasted image 20250101143605.png]]
Threads have access to global information in memory, but only have access to their own metadata sotred in memory's TCB.

Important term: return PCs

![[Pasted image 20250101144250.png]]

![[Pasted image 20250101144731.png]]


- Since threads in a same process share memory, their stack space may overlap:

![[Pasted image 20250101145205.png]]
Benefit: 
- This architecture (same memory for threads) helps with sharing global information between stacks.

Loss:
- Overlapping space (maybe)

----------

Possible scheduler executions:

![[Pasted image 20250101150128.png]]
 - Proper **locking** discipline will save us here.
 - This scheduler behavior in no deal for independent threads (on different processes) but for cooperating threads (on a same process)

![[Pasted image 20250101150503.png]]

Correctness by design (in code) **motivation**:
- One reason is a **race condition**

![[Pasted image 20250101150627.png]]
Very important terms:
- **Blocking a thread is different from a lock**

![[Pasted image 20250101152910.png]]
![[Pasted image 20250101160632.png]]
![[Pasted image 20250101162206.png]]

- Here **lock** is the whole data structure of tree, and the **critical section** is insert function.
-------

Managing processes (just like what we did about threads)

![[Pasted image 20250101162608.png]]


**Init process:**

![[Pasted image 20250101162711.png]]
==Important:==

![[Pasted image 20250101162749.png]]

![[Pasted image 20250101163052.png]]

When a process calls **fork()**, the operating system creates a new process (called the child process) that is an almost exact copy of the calling process (called the parent process).

**Memory Addresses**:

- Although the virtual memory contents of the parent and child appear identical immediately after the fork, their virtual memory address spaces are distinct.
- **The same virtual address in the parent and child processes maps to different physical memory locations (or the same location until a modification is made).**
- The **physical memory** backing those virtual addresses will differ ==if modifications are made after the fork.==
- The child process gets a copy of the parent process's memory.

- After the fork, the parent and child processes execute independently, though they typically start execution at the same point, just after the `fork()` call.
- The parent and child processes may execute different code paths based on the return value of `fork()`.

![[Pasted image 20250101163516.png]]


### Typical Usage

The `fork()` system call is often used in combination with `exec()` system calls. A common pattern is:

1. A parent process forks a child process.
2. The child process ==replaces its memory space with a new program== using an `exec()` system call.
3. The parent process can continue its execution, often waiting for the child process to complete.

![[Pasted image 20250101170638.png]]