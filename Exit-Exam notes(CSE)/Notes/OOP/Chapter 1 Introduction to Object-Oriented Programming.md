
## **1.1 Computer Systems and Programs**

A Computer System is made up of two inseparable components that work together:

**Hardware:** The physical components of a computer — CPU, motherboard, RAM, hard disk, keyboard, mouse, etc.

**Software:** A collection of programs that control and direct hardware operations. Examples include operating systems, applications, drivers, and utilities.

A Computer Program is a precise sequence of instructions that directs the computer to complete a task. Programs are written using a Programming Language — a formal language with its own syntax and grammar rules. The programmer writes source code in a text editor or IDE (Integrated Development Environment). The choice of programming language depends on:

Maintainability — how easy it is to update or fix the code

Scalability — how well the code handles growth

Performance — how efficiently the code runs

Security — how well the code is protected from vulnerabilities

## **1.2 Classifications of Programming Languages**

Programming languages can be classified in three ways:

|   |   |
|---|---|
|**Classification**|**Explanation**|
|**By Generation**|Level of abstraction: Machine code → Assembly → High-level (e.g., Java, Python)|
|**By Purpose**|General-purpose (Java, C++) vs. Special-purpose (SQL for databases, HTML for web)|
|**By Paradigm**|The style and approach to writing code (see below)|

### **1.2.1 Programming Paradigms**

A Programming Paradigm defines the style and methodology used to structure and write code. The four major paradigms are:

|   |   |
|---|---|
|**Paradigm**|**Description**|
|**Procedural**|Top-down approach. Code is a series of procedures/functions. Examples: C, Pascal, Fortran.|
|**Object-Oriented (OOP)**|Code organized as classes and objects that combine data and behavior. Examples: Java, C++, Python.|
|**Functional**|Based on pure mathematical functions; no side effects. Output depends only on input. Examples: Haskell, Lisp, Scala.|
|**Logic**|Declarative style — the programmer specifies WHAT to achieve, not HOW. Based on formal logic. Example: Prolog.|

_⭐ Exam Tip: Exit exams frequently test distinguishing between paradigms. Key distinctions: Procedural = step-by-step functions; OOP = objects with state; Functional = no side effects; Logic = declarative._

## **1.3 The Four Pillars of OOP**

Object-Oriented Programming is built on four fundamental principles — often called the Four Pillars of OOP:

### **1. Encapsulation**

The bundling of data (fields/variables) and the methods that operate on that data into a single unit called an object. The internal state is hidden from the outside world — external code can only interact through defined interfaces (methods).

Real-world analogy: A car's engine — you press the accelerator (interface) without knowing how fuel injection works (hidden state).

In Java: achieved using private fields and public getter/setter methods.

_⭐ Exam Tip: Encapsulation is primarily about data hiding and access control using access modifiers (private, public, protected)._

### **2. Abstraction**

The process of hiding complex implementation details and exposing only the essential, relevant features to the user. It lets programmers focus on WHAT an object does, not HOW it does it.

In Java: achieved through abstract classes and interfaces.

Difference from Encapsulation: Encapsulation hides data; Abstraction hides implementation complexity.

### **3. Inheritance**

The mechanism by which a child class (subclass) acquires the properties and behaviors of a parent class (superclass). This enables code reuse and establishes an IS-A relationship.

**Example:** A Dog class inherits from Animal class — Dog IS-A Animal.

In Java: using the extends keyword for classes, implements for interfaces.

Java supports single inheritance for classes but multiple inheritance through interfaces.

_��� Note: Java does NOT support multiple class inheritance to avoid the 'diamond problem'. It resolves this via interfaces._

### **4. Polymorphism**

The ability of a single method, object, or interface to take on many forms. The same method name behaves differently depending on the object it is called on.

**Compile-time Polymorphism (Static):** Method Overloading — multiple methods with the same name but different parameters in the same class.

**Runtime Polymorphism (Dynamic):** Method Overriding — a subclass provides a specific implementation of a method already defined in its superclass. Resolved at runtime.

_⭐ Exam Tip: Overloading = same class, different parameters (compile-time). Overriding = parent/child classes, same signature (runtime). This is a very common exam question._

## **1.4 Introduction to Java**

Java is one of the most widely used programming languages in the world. Key historical facts:

Developed by James Gosling and his team at Sun Microsystems, starting in 1991.

Originally called Oak, then Green, before being renamed Java.

First publicly released in 1995. Uses a C/C++-style syntax.

In 2010, Oracle Corporation acquired Sun Microsystems and continues to maintain Java.

Key version milestones: Generics (SE 5), Lambda expressions (SE 8), Modules (SE 9).

### **1.4.1 Key Features of Java**

|   |   |
|---|---|
|**Feature**|**Description**|
|**Object-Oriented**|Everything in Java is organized around objects and classes.|
|**Platform-Independent**|Compiled to bytecode that runs on any system with a JVM (Write Once, Run Anywhere).|
|**Secure**|Bytecode verification, no explicit pointers, sandboxing, and security manager.|
|**Robust**|Strong memory management, exception handling, and type checking reduce runtime errors.|
|**Multithreaded**|Built-in support for concurrent execution of multiple threads.|
|**Extensible & Open Source**|Modular design; JDK source code is freely available under the GPL license.|

## **1.5 Java Architecture and Execution**

Java uses a unique hybrid compilation strategy that gives it both portability and performance:

### **1.5.1 Compilation and Execution Flow**

|   |   |
|---|---|
|**Step**|**Action**|
|**Step 1**|Developer writes Java source code in .java files.|
|**Step 2**|The javac compiler compiles source code into platform-neutral bytecode (.class files).|
|**Step 3**|The JVM (Java Virtual Machine) interprets the bytecode on the specific host OS.|
|**Step 4**|The JIT compiler converts frequently-used bytecode into native machine code at runtime for speed.|

### **1.5.2 JVM, JRE, and JDK**

These three components form a hierarchy — each includes the one below it:

|   |   |
|---|---|
|**Component**|**Description**|
|**JDK (Java Development Kit)**|Full toolkit for developers. Includes: JRE + compilers (javac) + debuggers + documentation tools.|
|**JRE (Java Runtime Environment)**|What end users need to run Java programs. Includes: JVM + Java class libraries.|
|**JVM (Java Virtual Machine)**|The engine that executes bytecode. Contains the JIT compiler. Manages memory (heap, stack, garbage collection).|

 Note: JIT (Just-In-Time) Compiler is part of the JVM. It improves performance by converting hot (frequently executed) bytecode into native machine code at runtime, avoiding repeated interpretation overhead._

## **1.6 Java Editions**

|   |   |
|---|---|
|**Edition**|**Purpose & Target**|
|**Java SE (Standard Edition)**|Core language + APIs for general-purpose development (I/O, networking, graphics, databases). Foundation for all other editions.|
|**Java EE (Enterprise Edition)**|Extends SE with APIs for large-scale enterprise apps: web services, messaging (JMS), security, transactions.|
|**Java ME (Micro Edition)**|Lightweight subset for mobile and embedded devices with limited resources.|
|**JavaFX**|Modern framework for building rich, interactive desktop/web/mobile GUIs. Successor to Java Swing.|

# **CHAPTER 1 — PRACTICE QUESTIONS**

_Introduction to Object-Oriented Programming_

_25 Multiple Choice Questions  •  Correct answers highlighted in green_

**1.** **Which of the following best defines a Computer Program?**

A.  A physical device that processes data

**B.  A set of instructions that tells a computer how to perform a task**

C.  The operating system that manages hardware resources

D.  A type of hardware component used to store data

**✔ Answer: B**   _A computer program is a precise sequence of instructions written in a programming language that directs the computer to carry out a specific task._

**2.** **Which of the following is considered HARDWARE in a computer system?**

A.  An operating system

B.  A web browser application

C.  A device driver

**D.  A CPU (Central Processing Unit)**

**✔ Answer: D**   _Hardware refers to physical components. The CPU is a physical chip. Operating systems, browsers, and drivers are all software._

**3.** **A programmer needs to choose a programming language for a safety-critical medical system. Which factor should be the MOST important consideration?**

A.  Scalability

B.  Maintainability

**C.  Security and robustness**

D.  Performance

**✔ Answer: C**   _For a safety-critical system, security and robustness (reliability, fault tolerance) are the top priorities to prevent life-threatening failures._

**4.** **Which programming language classification is based on the STYLE of writing and structuring code?**

A.  Classification by generation

B.  Classification by purpose

**C.  Classification by paradigm**

D.  Classification by platform

**✔ Answer: C**   _Paradigm-based classification groups languages by their programming style (e.g., procedural, OOP, functional). Generation is about abstraction level; purpose is about the task._

**5.** **The Procedural Programming paradigm is best described as:**

A.  Organizing code around objects that combine data and behavior

**B.  Writing code as a series of functions using a top-down approach**

C.  Specifying the desired result without describing how to achieve it

D.  Using pure mathematical functions with no side effects

**✔ Answer: B**   _Procedural programming (e.g., C, Pascal) organizes code into procedures/functions and follows a top-down execution flow. It is the oldest and most basic paradigm._

**6.** **In Functional Programming, a 'pure function' means:**

A.  A function that only contains arithmetic operations

B.  A function declared with the 'pure' keyword

**C.  A function whose output depends ONLY on its inputs and causes no side effects**

D.  A function that can only be called once during program execution

**✔ Answer: C**   _A pure function always produces the same output for the same input and does not modify any external state (no side effects). This is the core principle of functional programming._

**7.** **Which programming paradigm allows the programmer to declare WHAT result is needed without specifying HOW to compute it, using formal rules and logic?**

A.  Procedural Programming

B.  Object-Oriented Programming

C.  Functional Programming

**D.  Logic Programming**

**✔ Answer: D**   _Logic Programming (e.g., Prolog) is declarative — the programmer defines facts and rules; the runtime engine determines how to satisfy queries._

**8.** **Which OOP principle describes the bundling of data and methods into a single unit, while restricting direct access to the internal state?**

A.  Abstraction

B.  Polymorphism

**C.  Encapsulation**

D.  Inheritance

**✔ Answer: C**   _Encapsulation bundles fields and methods into an object and uses access modifiers (private, public) to control access to the internal state._

**9.** **What is the key difference between Abstraction and Encapsulation?**

A.  Abstraction is for classes; encapsulation is for objects

**B.  Abstraction hides implementation complexity; encapsulation hides data (internal state)**

C.  Abstraction uses private fields; encapsulation uses interfaces

D.  There is no meaningful difference between the two

**✔ Answer: B**   _Encapsulation hides data using private fields + getters/setters. Abstraction hides HOW something is implemented (e.g., via abstract classes and interfaces)._

**10.** **A 'Dog' class that inherits properties and behaviors from an 'Animal' class is an example of which OOP principle?**

A.  Encapsulation

B.  Abstraction

C.  Polymorphism

**D.  Inheritance**

**✔ Answer: D**   _Inheritance establishes an IS-A relationship (Dog IS-A Animal), allowing the Dog class to reuse and extend the Animal class's fields and methods._

**11.** **Java does NOT support multiple class inheritance primarily to avoid:**

A.  Memory overflow errors

**B.  The diamond problem — ambiguity when two parents define the same method**

C.  Slow compilation times

D.  Circular dependency between packages

**✔ Answer: B**   _The diamond problem occurs when a class inherits from two classes that both have the same method, creating ambiguity. Java avoids this by limiting classes to single inheritance but allows multiple interface implementation._

**12.** **A method named 'makeSound()' behaves differently when called on a Dog object versus a Cat object. This is an example of:**

A.  Encapsulation

B.  Abstraction

C.  Inheritance

**D.  Polymorphism**

**✔ Answer: D**   _Polymorphism allows the same method name to have different behaviors depending on the object it is invoked on. This is runtime polymorphism (method overriding)._

**13.** **Method Overloading is classified as which type of polymorphism?**

A.  Runtime polymorphism — resolved during program execution

**B.  Compile-time polymorphism — resolved by the compiler based on method signatures**

C.  Dynamic polymorphism — determined by the JVM at runtime

D.  Structural polymorphism — based on object structure

**✔ Answer: B**   _Method overloading is resolved at compile time because the compiler determines which method to call based on the number and type of arguments. It is also called static binding._

**14.** **Java was originally developed at which company?**

A.  Oracle Corporation

B.  Microsoft

**C.  Sun Microsystems**

D.  IBM

**✔ Answer: C**   _Java was created by James Gosling and his team at Sun Microsystems in 1991. Oracle later acquired Sun in 2010 and now maintains Java._

**15.** **Which Java version introduced Lambda Expressions, enabling functional programming features?**

A.  Java SE 5

**B.  Java SE 8**

C.  Java SE 9

D.  Java SE 1.0

**✔ Answer: B**   _Java SE 8 (released 2014) introduced lambda expressions and the Stream API. SE 5 introduced Generics, and SE 9 introduced the module system._

**16.** **Java achieves platform independence through which mechanism?**

A.  Compiling source code directly into platform-specific machine code

**B.  Compiling source code into bytecode that runs on any system with a JVM**

C.  Interpreting source code line by line at runtime on each platform

D.  Using platform-specific compilers installed on each operating system

**✔ Answer: B**   _Java's 'Write Once, Run Anywhere' principle works because the compiler produces bytecode (.class files) — a neutral intermediate format — which the JVM on each platform then executes._

**17.** **Which of the following Java features prevents common memory errors by eliminating explicit pointer manipulation?**

A.  Platform independence via bytecode

B.  The JIT compiler

**C.  Automatic memory management and no explicit pointers**

D.  The Java package system

**✔ Answer: C**   _Java is Robust partly because it has no explicit pointer arithmetic (unlike C/C++). Memory is managed automatically by the JVM's garbage collector, preventing many common memory bugs._

**18.** **In Java's execution process, what is a .class file?**

A.  The original Java source code written by the developer

B.  Platform-specific native machine code ready to run on an OS

**C.  Bytecode produced by the Java compiler, executable by the JVM**

D.  A compressed archive containing the entire application

**✔ Answer: C**   _The javac compiler converts .java source files into .class files containing platform-neutral bytecode. The JVM then interprets (and JIT-compiles) this bytecode on the target platform._

**19.** **What is the correct hierarchical relationship between JDK, JRE, and JVM?**

A.  JVM ⊃ JRE ⊃ JDK  (JVM is the largest)

**B.  JDK ⊃ JRE ⊃ JVM  (JDK is the largest)**

C.  JRE ⊃ JDK ⊃ JVM  (JRE is the largest)

D.  They are all equal and independent components

**✔ Answer: B**   _JDK (developer toolkit) contains the JRE + compiler + tools. JRE (runtime environment) contains the JVM + class libraries. JVM is the engine that runs bytecode. JDK is the most complete package._

**20.** **A developer wants to run a Java application but does NOT need to write or compile Java code. What is the MINIMUM they need to install?**

A.  JDK

**B.  JRE**

C.  JIT compiler only

D.  javac compiler only

**✔ Answer: B**   _The JRE (Java Runtime Environment) is sufficient to run compiled Java applications. The JDK is needed for development (writing and compiling). javac and JIT alone are not enough._

**21.** **The JIT (Just-In-Time) compiler improves Java performance by:**

A.  Compiling .java files to .class files faster than javac

B.  Reducing the size of bytecode files stored on disk

**C.  Converting frequently executed bytecode into native machine code at runtime**

D.  Pre-compiling all bytecode into machine code before the program starts

**✔ Answer: C**   _The JIT compiler monitors which bytecode segments are executed most ('hot spots') and compiles those into native machine code at runtime, eliminating the overhead of interpreting them repeatedly._

**22.** **Which Java edition provides APIs specifically designed for mobile phones and embedded systems with limited memory?**

A.  Java SE

B.  Java EE

**C.  Java ME**

D.  JavaFX

**✔ Answer: C**   _Java ME (Micro Edition) is a subset of Java designed for resource-constrained devices like feature phones and embedded systems. Java SE is for general use; Java EE for enterprise; JavaFX for rich GUIs._

**23.** **A company is building a large web application requiring security, messaging, and transaction management across multiple servers. Which Java edition is most appropriate?**

A.  Java ME

B.  Java SE

C.  JavaFX

**D.  Java EE**

**✔ Answer: D**   _Java EE (Enterprise Edition) extends SE with enterprise-grade APIs including web services, JMS (messaging), transaction management (JTA), and distributed security — all needed here._

**24.** **Which of the following correctly distinguishes a Compiler from an Interpreter?**

A.  A compiler translates code line-by-line; an interpreter translates the whole program at once

**B.  A compiler translates the entire program before execution; an interpreter translates and executes line-by-line**

C.  Both compiler and interpreter produce the same type of output

D.  An interpreter is faster than a compiler because it skips the translation step

**✔ Answer: B**   _A compiler processes the entire source program and produces an executable. An interpreter translates and executes one statement at a time. Java uses a hybrid: compiler → bytecode, then JVM interpreter/JIT._

**25.** **Which statement about the Java programming language is FALSE?**

A.  Java source code is compiled into bytecode before execution

B.  Java supports multiple inheritance through interfaces

C.  Java was first released publicly in 1995

**D.  Java supports explicit pointer manipulation like C and C++**

**✔ Answer: D**   _Java deliberately does NOT support explicit pointer manipulation (unlike C/C++). This makes Java more secure and robust by preventing common pointer-related errors like buffer overflows._