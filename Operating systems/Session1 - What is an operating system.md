What does OS do?

![[Pasted image 20241217184518.png]]

![[Pasted image 20241217184648.png]]
![[Pasted image 20241217184924.png]]
Formal definition:
![[Pasted image 20241217185052.png]]
- ==Abstraction of hardware resources==
- Security authentication

There used to be...
![[Pasted image 20241217185228.png]]
Engineering mindset needed (system programming):
![[Pasted image 20241217185406.png]]
![[Pasted image 20241217185614.png]]

Operating system is an **illusionist**. 

----------------------

Abstraction (illusion) that OS gives:
![[Pasted image 20241217192036.png]]
==Process (=application's machine) definition, so important. A process uses threads, sockets, files, and is restricted to an address space.== Processes are isolated from each other.
![[Pasted image 20241217192332.png]]
![[Pasted image 20241217192803.png]]
- system libs differ from other libs. They are included into your program, run by a **compiler**, and turn your program into bits to be run on the process.
  
#### 1. **Memory (Physical Memory)**

- **Definition**: Refers to the actual hardware (e.g., RAM) in a computer where data and instructions are stored *for quick access by the processor*.
- **Capacity**: Limited by the size of the physical RAM installed in the system.
- **Access**: Directly manipulated by the processor and managed by the operating system.
- **Characteristics**:
    - Stores the running program's code, data, and stack.
    - ==Volatile==, meaning its contents are lost when the system powers off.

#### 2. **Address Space**

- **Definition**: A logical construct that provides **a range of addresses that a program can use to reference memory.**
- **Virtualization**: Created by the operating system using virtual memory techniques. Programs think they have access to a large, continuous block of memory.
- **Capacity**: Depends on the system architecture:
    - **32-bit system**: Can address up to 4 GB of memory (2³² addresses).
    - **64-bit system**: Can address up to 16 exabytes (2⁶⁴ addresses), though practical limits are much lower.
- **Characteristics**:
    - ==Isolated== for each process, ensuring one process cannot access another's memory.
    - Maps logical addresses to physical memory using a **Memory Management Unit (MMU)**.


### **Sockets vs Network**

#### 1. **Sockets**

- **Definition**: Sockets are endpoints for communication between two devices (or ==processes==) *over* a network. A socket can be thought of as **an endpoint in a two-way ==communication channel**==. Socket routines create the communication channel, and the channel is used to send data between application programs either locally or over networks.
- **Purpose**: Provide a programming interface for applications to send and receive data over the network.
- **Scope**:
    - Operate at the **transport layer** (e.g., TCP, UDP).
    - Abstract the complexity of communication, offering a simpler API.
- **Types**:
    - **Stream Sockets (TCP)**: Reliable, connection-oriented communication.
    - **Datagram Sockets (UDP)**: Unreliable, connectionless communication.
- **Examples**:
    - Sending HTTP requests (client socket) and receiving responses (server socket).
    - Real-time chat applications using socket communication.

#### 2. **Network**

- **Definition**: A network is a collection of interconnected devices (computers, servers, routers, etc.) that communicate using specific protocols.
- **Purpose**: Enables the transfer of data between devices globally (internet) or locally (LAN, WAN).
- **Scope**:
    - Includes all layers of the **OSI model** (from physical cables to application protocols).
    - Covers infrastructure like routers, switches, DNS servers, etc.
- **Types**:
    - **LAN**: Local Area Network (e.g., within a home or office).
    - **WAN**: Wide Area Network (e.g., the internet).
    - **PAN**: Personal Area Network (e.g., Bluetooth devices).


|Feature|Port|Socket|
|---|---|---|
|**Definition**|A numerical identifier for services|Combination of IP address and port number|
|**Purpose**|Identifies specific processes or services|Establishes communication endpoints|
|**Scope**|Exists independently within a system|Tied to a specific connection or endpoint|
|**Protocol**|Part of TCP/UDP layer|Includes transport and application layers|
|**Example**|Port 80 for HTTP service|`192.168.1.5:80` (a socket for HTTP)|

In essence, **ports** are like "doors" to a system, while **sockets** are the actual "connections" established through those doors for communication.

---

![[Pasted image 20241217193901.png]]

- These services are often linked with ==libraries== to be used in a program.
- Os provides common services in the form of I/O (mouse, monitor, keybooard)

![[Pasted image 20241217193210.png]]
Process switch shown above.

- Application becomes a process when it's binary is loaded on the memory and puted into a scheduler queue and so on. 
-----------------

![[Pasted image 20241217194941.png]]

![[Pasted image 20241217195037.png]]

---------------




