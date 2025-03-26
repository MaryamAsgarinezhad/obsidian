
![[Pasted image 20241223194101.png]]

-------------

![[Pasted image 20241223194341.png]]

![[Pasted image 20241223194444.png]]
![[Pasted image 20241223194557.png]]

PC: program counter

#### **Roles of Memory in an OS:**

- **Temporary Data Storage:**
    
    - Holds the code and data of active processes.
    - Allows quick access to instructions and data compared to storage like hard drives or SSDs.
- **Execution of Processes:**
    
    - The OS loads processes into memory to execute them. Without sufficient memory, processes cannot run efficiently
- ==**Multitasking==:**
    - The OS uses memory to allow multiple processes to run concurrently by allocating portions of memory to each process.

#### **Roles of Registers in an OS:**

- **Execution Efficiency:**
    
    - Registers allow the CPU to access data and instructions much faster than accessing them from memory.
- **Instruction Execution:**
    
    - The CPU uses registers to fetch, decode, and execute instructions.
- **==Process Context Switching==:**
    
    - During multitasking, the OS saves the state of a process (including register values) before switching to another process. This state is restored when the process resumes.
- **Types of Registers and Their Uses:**
    
    - **Program Counter (PC):** Tracks the address of the next instruction to be executed.
    - **Accumulator:** Temporarily holds data being processed.
    - **Instruction Register (IR):** Holds the instruction currently being executed.
    - **General-Purpose Registers:** Store intermediate results or operands.
    - **Status Registers (Flags):** Store conditions (e.g., zero, carry, or overflow) that affect program flow.


### **Relationship Between Memory and Registers:**

- Registers act as the immediate working space for the CPU, while memory serves as a larger but slower workspace.
- The OS facilitates the movement of data between registers and memory as part of executing programs and managing resources.
- Efficient use of registers and memory is critical for the OS to optimize system performance.

![[Pasted image 20241223194932.png]]
**Instructions are located in memory**.This image represents the **Instruction Cycle** in a CPU, illustrating the steps involved in fetching, decoding, and executing instructions from memory.

### **1. Instruction Fetch:**

- **Program Counter (PC):**
    - Contains the address of the next instruction to be executed.
    - The CPU uses this address to retrieve the instruction from memory.
- **Memory:**
    - The instruction is fetched from memory (highlighted in yellow) into the processor for further processing.

---

### **2. Decode:**

- The **decode unit** interprets the fetched instruction to determine what action the CPU needs to perform.
- This process involves understanding the operation code (opcode) and operands.

---

### **3. Execute:**

- **Registers:**
    - Temporary storage locations within the CPU that hold data or intermediate results.
- **Arithmetic Logic Unit (ALU):**
    - Performs arithmetic or logical operations as required by the instruction.
- ==The CPU processes the data (fetched from memory or registers) and stores the result back into memory or a register.==

------------

So how does a thread does the above?
![[Pasted image 20241223201911.png]]

- CPU core executes only one thread at time.
- All is about process state, program counter register pointer, intermediate values (actual values or pointer to values)

![[Pasted image 20241224192216.png]]

- Memory consists of temporary data and instructions.

---------------

Very important pic:

![[Pasted image 20241224193234.png]]

- A thread consists of a pc and stack pointer along with its intermediate values stored in its registers.

- It is located on **core** when running, and saved in **memory TCB** when pending.

When it is said that **threads have their own Program Counter (PC), stack pointer, and registers**, it refers to the fact that threads are treated as independent units of execution within a process. Here's what each component means in the context of threads:

### **1. Program Counter (PC):**

- The **Program Counter** holds the memory address of the next instruction to be executed by the thread.
- **Why Threads Need Their Own PC:**
    - Each thread executes its own sequence of instructions.
    - Having its own PC allows a thread to keep track of where it is in its execution independently of other threads in the same process.


### **2. Stack Pointer:**

- The **Stack Pointer** points to the top of the thread's **call stack**, which is used for:
    
    - Function calls and returns.
    - Local variables.
    - Parameters passed to functions.
- **Why Threads Need Their Own Stack Pointer:**
    
    - Each thread has its own stack, which is used to manage its function calls and execution context.
    - This separation prevents interference between threads when they call functions or allocate local variables.


### **3. Registers:**

- **Registers** are small, high-speed storage locations in the CPU that temporarily hold data and instructions during execution.
- **Why Threads Need Their Own Registers:**
    - Threads need independent register sets because each thread could be executing its own code and managing its own variables.
    - During a context switch (when the CPU switches from executing one thread to another), the CPU saves the current thread's register values and loads the new thread's register values.

----------

What happens between each context switch?
![[Pasted image 20241228183530.png]]


What triggers this switch
![[Pasted image 20241228183723.png]]


--------------

**ADDRESS SPACE:**


![[Pasted image 20241228184327.png]]

Page **fault**: when you try to read sth from between heap and stack where there is no memory assigned.

![[Pasted image 20241228184555.png]]


- Code segment contains instructions
- Static data segment contain static, global variables (explicitly declared rather than allocated with malloc) and when the program is first loaded 
- Stack segment contains local variables
- Heap segment stores variables allocated with **malloc like linked lists

------------

Reliability and fairness of OS:

![[Pasted image 20241228185758.png]]
![[Pasted image 20241228185843.png]]
![[Pasted image 20241228190053.png]]

We define a specific range of address for each thread : 
- Addresses are translated when program loaded (requires relocation loader)

![[Pasted image 20241228190233.png]]

![[Pasted image 20241228190722.png]]

![[Pasted image 20241228190935.png]]

--------

**PROCESS:**

![[Pasted image 20241228191143.png]]

important pic:

- Threads have their own stack
- concurrency vs parallelsim
- ![[Pasted image 20241228191407.png]]

-----------

![[Pasted image 20241228191553.png]]
 dual mode mechanism (address translation is not enough):
![[Pasted image 20241228191728.png]]

![[Pasted image 20241228191744.png]]
![[Pasted image 20241228191829.png]]

how os loads a process (put it all together):
![[Pasted image 20241228191957.png]]
![[Pasted image 20241228192036.png]]

--------------

How does the kernell switch between proceses (important)?

![[Pasted image 20241228192308.png]]

