
- High performance **system programming** language that reduces memory related errors.
- Grabage collection not necessary
- needs much less memory
- concurrent programming support
- robust compiler that detects errors not found by other compilers
- toml file is to define dependencies (libraries that you install and import from them)

---------------------------

```rust
#![allow(unused)]
```

It is to void raising warnings when we have unused variables.

-------------------------------------
Macros in Rust are a way of defining reusable chunks of code. They are similar to functions, but instead of generating a value, they generate code. This means that they are evaluated at compile time, not at runtime, which can lead to more efficient code.

```rust
macro_rules! say_hello {
    () => (
        println!("Hello, world!");
    );
}
```

In the above example, we've defined a macro called "say_hello" that prints "Hello, world!" when invoked.

In Rust, a **macro attribute** is a special type of attribute that ==applies a macro to the item it decorates==. These attributes are usually written as `#[macro_name]` and are placed above structs, enums, functions, or other items. They enable **metaprogramming**, allowing you to generate or modify code **at compile time**.

### Common Macro Attributes:

#### 1. `#[derive(...)]`

The `derive` attribute automatically generates implementations for certain traits for a given struct or enum. For example:

- **`#[derive(Debug)]`**: Automatically generates an implementation of the `Debug` trait, which allows the struct to be formatted using the `{:?}` formatter.
- **`#[derive(Deserialize)]`**: Automatically generates an implementation of the `Deserialize` trait, which allows the struct to be deserialized from a format like JSON.

#### 2. `#[cfg(...)]`

The `cfg` attribute allows conditional compilation based on the provided configuration options. For example:

- **`#[cfg(test)]`**: Only includes the item when compiling tests.
- **`#[cfg(target_os = "linux")]`**: Only includes the item when compiling for a Linux target.

**`#[tokio::main]`**: This attribute macro from the `tokio` crate allows the main function to be asynchronous. It initializes the Tokio runtime, which is necessary for running async functions.

--------------------------------------------

In Rust, a `struct` (short for structure) is a custom data type that allows you to group together related data under one name. Structs are used to create complex data types that can encapsulate multiple pieces of data, each with its own name and type. Structs in Rust are similar to classes in object-oriented programming languages but are simpler as they do not support inheritance.

-----------------------------------------

**When to Make a Reference:**

In Rust, you make a reference to a variable when you want to borrow the value rather than taking ownership of it. This is useful when you need to read or modify the value **temporarily**. This decision is driven by considerations of memory safety, ownership, borrowing rules, and the specific requirements of the operation you are performing.

```rust
fn calculate_length(s: &str) -> usize {
    s.len()
}

fn main() {
    let s = String::from("hello");
    let length = calculate_length(&s); // Borrowing the string
    println!("The length of '{}' is {}.", s, length); // Ownership is not taken
}
```

if you need to modify a value within a function but don’t want to take ownership, use a mutable reference (`&mut`). The function's signature should also have an ampersand with the type of argument that receives the reference.

```rust
fn add_exclamation(s: &mut String) {
    s.push_str("!");
}

fn main() {
    let mut my_string = String::from("Hello");
    add_exclamation(&mut my_string); // Borrowing the string mutably
    println!("Modified string: {}", my_string); // Ownership is not taken
}
```

When multiple parts of your code need access to the same data without ownership, references allow safe sharing.

```rust
fn main() {
    let s = String::from("hello");
    let r1 = &s;
    let r2 = &s;
    println!("r1: {}, r2: {}", r1, r2); // Multiple references
}

```

- `&str` is a reference to a **string slice**. The `&` indicates that it is a reference, and `str` is the type of the data being referenced.
- String slices are typically ==used to refer to parts of a string without taking ownership of the entire string==. This allows for efficient string handling and manipulation without unnecessary copying.

**When Not to Make a Reference:**

If a function or operation needs to take ownership of a variable (e.g., for transformation or exclusive access), do not use a reference.

```rust
fn take_ownership(s: String) {
    println!("Taking ownership of: {}", s);
}

fn main() {
    let s = String::from("hello");
    take_ownership (s); // Ownership is moved here
    // println!("{}", s); // Error: s is no longer valid here
}
```

When creating a new instance or value, you typically don’t use a reference.

```rust
fn main() {
    let s = String::from("hello"); // No reference needed
    println!("String: {}", s);
}
```

-----------------------------------------

Concurrency:

Executing different blocks of code independently (parallel programming).

- There are no guarantees on when threads will execute and if they even complete execution.
![[Pasted image 20240804134910.png]]

- To make sure they complete, we use join:

```rust
tread1.join().unwrap();
```

-------------------------
Semaphore:

A semaphore is used to limit the number of concurrent tasks. Here, we create an `Arc<Semaphore>` with a permit count of 64. This ensures that no more than 64 tasks are running concurrently.

Initialization in `main` Function

```rust
let semaphore = Arc::new(Semaphore::new(64));
```

![[Pasted image 20240807171129.png]]

--------------------------------------------

### 1. **Raw Pointers**

Raw pointers are similar to pointers in languages like C or C++. They provide ==direct access to memory== but do not come with the safety guarantees that Rust usually enforces.

- **`*const T`**:
    
    - A raw pointer to an immutable value of type `T`.
    - You cannot modify the value through this pointer.
    - Example: `let ptr: *const i32 = &value as *const i32;`
- **`*mut T`**:
    
    - A raw pointer to a mutable value of type `T`.
    - You can modify the value through this pointer.
    - Example: `let ptr: *mut i32 = &mut value as *mut i32;`

#### Characteristics of Raw Pointers:

- **Unsafe**: Raw pointers are not bound by Rust's strict ownership, borrowing, and lifetime rules. They can lead to undefined behavior if used incorrectly (e.g., dangling pointers, double-free errors).
- **Manual Management**: You must manually ensure that raw pointers do not point to invalid memory.
- **Use Case**: They are mainly used in unsafe code, FFI (Foreign Function Interface) to interact with other programming languages, or in scenarios requiring fine-grained control over memory.


### 2. **Smart Pointers**

Smart pointers in Rust are types that not only act like pointers but also manage the memory or resources they point to. They come with various features such as automatic memory management, reference counting, and thread safety.

- **`Box<T>`**:
    
    - A smart pointer for heap allocation.
    - Owns the data it points to, and automatically deallocates the memory when it goes out of scope.
    - Example: `let boxed_value = Box::new(5);`
    - Use Case: When you need heap allocation and strict ownership of the data.
      
- **`Rc<T>`** (Reference Counted):
    
    - A single-threaded reference-counting smart pointer.
    - Allows multiple ownership of the same data by keeping track of how many references exist.
    - Example: `let rc_value = Rc::new(5);`
    - Use Case: Shared ownership in single-threaded scenarios, like when you want multiple parts of your program to read the same data.
      
- **`Arc<T>`** (Atomic Reference Counted):
    
    - A thread-safe reference-counting smart pointer.
    - Allows multiple ownership across threads with atomic operations to manage the reference count.
    - Example: `let arc_value = Arc::new(5);`
    - Use Case: Shared ownership in multi-threaded scenarios, useful when multiple threads need access to the same data.
      
- **Versatility**: ==Smart pointers like `Box<T>` allow dynamic data structures, while `Rc<T>` and `Arc<T>` provide shared ownership without requiring manual reference counting.==

-----------------------------------------------------------

- **Cloning in Smart Pointers (`Rc<T>`, `Arc<T>`)**: Creates a new pointer to the same data, increasing the reference count.
- **Cloning Other Types (`Box<T>`, `Vec<T>`, etc.)**: Creates a deep copy of the data, resulting in a new, independent instance.

-------------------------------------------------------------



