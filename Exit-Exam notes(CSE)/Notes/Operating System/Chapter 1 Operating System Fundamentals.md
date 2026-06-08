
## 1.1 What is an Operating System? (The Manager of a Hotel)

An Operating System (OS) is software that sits between users, applications, and computer hardware.

When you open Chrome, VS Code, or Spotify, those applications do not directly communicate with the CPU, memory, or disk. Instead, they ask the operating system to perform those tasks.

Think of a hotel.

Guests do not walk into storage rooms, assign themselves rooms, or manage hotel employees. Instead, they communicate with the hotel manager, who coordinates everything.

The operating system plays the same role in a computer. It manages resources and provides services to applications.

### Main Goals of an Operating System

#### Execute User Programs

The operating system loads, runs, and terminates programs.

Examples:

- Chrome
    
- Firefox
    
- VS Code
    
- Games
    

#### Make the Computer Convenient to Use

Without an operating system, users would need to communicate directly with hardware.

The OS provides interfaces such as:

- Graphical User Interface (GUI)
    
- Command Line Interface (CLI)
    

making computers much easier to use.

#### Use Hardware Efficiently

Resources are limited.

The operating system must efficiently manage:

- CPU
    
- Memory
    
- Storage
    
- Input/Output Devices
    

to maximize performance.

### Exam Focus

A very common question asks:

**What are the primary goals of an operating system?**

Answer:

1. Execute user programs.
    
2. Make the system convenient to use.
    
3. Utilize hardware efficiently.
    

---

## 1.2 Resource Allocator (The Traffic Police of a Busy Intersection)

Multiple programs compete for limited resources.

For example:

- Chrome wants CPU time.
    
- Spotify wants CPU time.
    
- VS Code wants CPU time.
    

The operating system decides who gets access and for how long.

Think of a traffic officer controlling vehicles at a crowded intersection.

Without coordination, everything would become chaotic.

The resource allocator ensures fair and efficient resource usage.

### Responsibilities

- CPU allocation
    
- Memory allocation
    
- Disk allocation
    
- Device allocation
    

### Key Idea

The resource allocator's job is fairness and efficiency.

---

## 1.3 Control Program (The Security Guard of the Computer)

Besides managing resources, the operating system also protects the system.

A control program monitors program execution and prevents improper behavior.

Examples:

- Preventing unauthorized memory access
    
- Preventing illegal instructions
    
- Preventing one process from corrupting another
    

Without this protection, one faulty application could crash the entire system.

### Key Idea

Control Program = Protection + Supervision

---

## 1.4 Kernel (The Heart of the Operating System)

The kernel is the most important component of an operating system.

It remains loaded in memory and runs throughout the entire lifetime of the system.

Every major operating system service eventually passes through the kernel.

### Responsibilities of the Kernel

- Process Management
    
- Memory Management
    
- Device Management
    
- File Management
    
- System Call Handling
    

Think of a company.

Employees perform daily work, but major decisions pass through management.

Applications are the employees.

The kernel is the management.

These are the kinds of questions I would expect from an Ethiopian CSE Exit Exam. I've mixed conceptual questions, definition questions, and exam-trap questions.

# Chapter 1 Practice Questions

### 1. Which of the following best describes an Operating System?

A. A hardware component that stores data permanently

B. A program that acts as an intermediary between users and hardware

C. A device used to connect computers to networks

D. A programming language used to develop applications

**Answer: B**

**Explanation:**  
The operating system sits between users/applications and the hardware, managing resources and providing services.

---

### 2. What is the primary goal of multiprogramming?

A. Increase memory size

B. Improve graphics performance

C. Increase CPU utilization

D. Reduce storage requirements

**Answer: C**

**Explanation:**  
Multiprogramming keeps multiple jobs in memory so the CPU always has something to execute instead of remaining idle.

---

### 3. Which component of the operating system is always running?

A. Shell

B. Compiler

C. Kernel

D. Device Driver

**Answer: C**

**Explanation:**  
The kernel is the core part of the operating system and remains active throughout system operation.

---

### 4. Which of the following is NOT a primary goal of an Operating System?

A. Execute user programs

B. Make the system convenient to use

C. Utilize hardware efficiently

D. Replace application software

**Answer: D**

**Explanation:**  
The OS provides an environment for applications; it does not replace them.

---

### 5. The bootstrap program is responsible for:

A. Scheduling CPU processes

B. Managing files

C. Loading the operating system kernel into memory

D. Allocating virtual memory

**Answer: C**

**Explanation:**  
When the computer starts, the bootstrap program initializes hardware and loads the OS kernel.

---

### 6. The bootstrap program is typically stored in:

A. RAM

B. Cache Memory

C. ROM or EEPROM

D. Hard Disk

**Answer: C**

**Explanation:**  
The bootstrap must exist before the OS loads, so it is stored in non-volatile memory.

---

### 7. An interrupt is:

A. A permanent CPU failure

B. A signal that causes the CPU to temporarily stop its current activity

C. A type of secondary storage

D. A memory allocation technique

**Answer: B**

**Explanation:**  
Interrupts notify the CPU that an event requires immediate attention.

---

### 8. Which of the following is a software-generated interrupt?

A. Keyboard Input

B. Mouse Click

C. Disk Completion Signal

D. Trap

**Answer: D**

**Explanation:**  
A trap originates from software, often due to errors or system calls.

**Common Exam Trap:**  
Interrupt → Usually hardware generated  
Trap → Software generated

---

### 9. Which execution mode has unrestricted access to hardware resources?

A. User Mode

B. Application Mode

C. Protected Mode

D. Kernel Mode

**Answer: D**

**Explanation:**  
Kernel Mode allows direct access to hardware and privileged instructions.

---

### 10. In dual-mode operation, the mode bit value 1 indicates:

A. Kernel Mode

B. Monitor Mode

C. User Mode

D. Supervisor Mode

**Answer: C**

**Explanation:**  
Mode bit = 1 → User Mode  
Mode bit = 0 → Kernel Mode

This appears frequently in OS exams.

---

### 11. System calls are primarily used to:

A. Increase processor speed

B. Request services from the operating system

C. Create hardware devices

D. Replace interrupts

**Answer: B**

**Explanation:**  
Applications use system calls whenever they need OS services such as file access or process creation.

---

### 12. What is the primary purpose of a timer in an operating system?

A. Increase RAM size

B. Prevent infinite execution of user programs

C. Improve disk performance

D. Manage network communication

**Answer: B**

**Explanation:**  
Timers ensure that the operating system periodically regains control of the CPU.

---

### 13. Time-sharing systems are primarily designed to:

A. Allow multiple users to interact with the computer simultaneously

B. Increase hard disk capacity

C. Eliminate interrupts

D. Remove virtual memory

**Answer: A**

**Explanation:**  
Time-sharing rapidly switches the CPU among processes, creating the illusion of simultaneous execution.

---

### 14. Virtual memory allows:

A. Programs larger than physical memory to execute

B. Faster CPU execution

C. Permanent storage replacement

D. Elimination of disk usage

**Answer: A**

**Explanation:**  
Virtual memory uses disk space to extend available memory beyond physical RAM.

---

### 15. Which of the following best describes a Peer-to-Peer (P2P) system?

A. One central server provides all services

B. Every node can act as both client and server

C. Only one computer can access resources

D. Users communicate only through a web browser

**Answer: B**

**Explanation:**  
In a P2P system, there is no dedicated server. Every participant can both request and provide services.

---

# High-Frequency Exit Exam Concepts from This Chapter

Based on the exams you've shown so far, these deserve special attention:

1. Multiprogramming
    
2. Time Sharing (Multitasking)
    
3. Virtual Memory
    
4. User Mode vs Kernel Mode
    
5. System Calls
    
6. Interrupt vs Trap
    
7. Kernel
    
8. Bootstrap Program
    
9. Client-Server vs P2P
    
10. Process Management
    

These topics are much more likely to appear than things like Handheld Systems or Multimedia Systems.

