
### **Formal Language**

A **formal language** is a set of strings formed from a finite alphabet according to specific rules. In the context of computer science, mathematics, and linguistics, a formal language is a fundamental concept used to describe computational problems, linguistic patterns, or symbolic systems.

![[Pasted image 20250207104926.png]]


----


![[Pasted image 20250207104952.png]]

- **Closure properties**: Regular languages are closed under union, concatenation, Kleene star, intersection, and complementation.

---

### **Pushdown Automata (PDA)**

A **pushdown automaton (PDA)** is an automaton that extends the capabilities of a finite automaton by including a **stack** for memory. It can process **context-free languages (CFLs)**, which allows it to handle languages that require **nested or recursive structures**, like balanced parentheses.

#### Components:

1. **Input Tape**: A tape of symbols from the input alphabet (Σ\SigmaΣ).
2. **Finite Control**: A finite set of states, similar to a DFA.
3. **Stack**: An unbounded stack used to store symbols, which provides memory to handle recursive patterns.
4. **Transition Function**: Depends on:
    - Current state.
    - Current input symbol.
    - Top symbol of the stack.
    - Defines the next state, what to pop from the stack, and what to push onto the stack.


---

### **Linear Bounded Automata (LBA)**

A **linear bounded automaton (LBA)** is a type of **Turing machine** with restricted memory. Unlike a standard Turing machine, an LBA's tape size is **bounded by the length of the input string**. It is more powerful than a PDA but less powerful than a general Turing machine.

#### Components:

1. **Tape**: A read/write tape, but the usable space is limited to a portion proportional to the input size.
2. **Finite Control**: A finite set of states.
3. **Tape Head**: Reads and writes on the tape and ==can move left or right==.

#### Characteristics:

- LBAs are useful for recognizing languages that require memory proportional to input size but don't need unlimited space.

---

### **Turing Machine (TM)**

A **Turing machine (TM)** is the most powerful type of automaton and serves as the foundation of **computability theory**. It can simulate any algorithmic computation.

#### Components:

4. **Tape**: An infinite tape divided into cells, which can hold symbols from a finite alphabet.
5. **Finite Control**: A finite set of states.
6. **Tape Head**:
    - Can move left or right.
    - Can read and write symbols on the tape.
7. **Transition Function**: Determines the machine's next state, what symbol to write on the tape, and the direction of the tape head's movement.

#### Languages Recognized:
- **Recursively Enumerable Languages (RELs)**, which include all computable languages.

#### Importance:

- A Turing machine is a theoretical model for defining what it means for a function to be "computable."
- It is used to formalize concepts like the **halting problem** and **P vs NP**.

----

### Note:

- A **context-free grammar (CFG)** is defined by having a single non-terminal on the LHS, and it cannot depend on the context of the surrounding symbols.
- A **context-sensitive grammar (CSG)** can depend on the surrounding context and requires that the RHS is at least as long as the LHS.
- To determine if a grammar is CFG or CSG, analyze the LHS of rules, check for context dependence, and ensure compliance with length conditions.

---


### **Other language types:** 

![[Pasted image 20250207113213.png]]

---

![[Pasted image 20250207124332.png]]
![[Pasted image 20250207124311.png]]

---

### **Definition of a Finite Automaton**

A finite automaton is defined by a **5-tuple**.
![[Pasted image 20250207125054.png]]
Where each symbol has a specific meaning:

- **QA-** The Set of States
- **Σ** - The Alphabet
- **δA** - The Transition Function
- **q0​** - The Start State
- **FA​** - The Set of Accepting (or Final) States

---

- Both regular and context-free languages can be text-independent or text dependent.

### **Grammer rules:**

![[Pasted image 20250207133232.png]]


### **PDA example**

![[Pasted image 20250207135039.png]]

---

### **Touring Machine**:

![[Pasted image 20250207140018.png]]

### **Key Components of the Turing Machine**

![[Pasted image 20250207140055.png]]
![[Pasted image 20250207140104.png]]

**==Example:==**
![[Pasted image 20250207140440.png]]

---

