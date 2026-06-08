
## 2.1 Process Concept

### Process (Recipe vs Chef)

A process is a program in execution.

Many students confuse a program with a process.

A program is simply a file containing instructions stored on disk. Nothing is happening yet.

A process is created when the operating system loads the program into memory and starts executing it.

Think about a recipe book.

A recipe sitting on a shelf is just instructions.

When a chef starts following the recipe and cooking, work is actually happening.

Similarly:

```text
Program  = Passive
Process  = Active
```

A process requires resources such as:

- CPU time
    
- Memory
    
- Files
    
- Input/Output devices
    

This distinction is asked surprisingly often in OS exams.

---

## 2.2 Process Memory Layout

Every process receives its own memory space.

Think of a company building where different floors serve different purposes.

### Text Section (The Instruction Floor)

Stores the executable machine code.

Contains the instructions the CPU executes.

Normally remains unchanged while the program runs.

---

### Data Section (The Permanent Records Room)

Stores:

- Global variables
    
- Static variables
    

Example:

```c
static int count = 0;
```

These variables exist throughout the program's lifetime.

---

### Heap (The Expandable Warehouse)

Stores memory allocated during execution.

Examples:

```c
malloc()
calloc()
new
```

The heap can grow and shrink dynamically.

Think of a warehouse that rents extra storage space whenever needed.

---

### Stack (The Work Desk)

Stores temporary information such as:

- Function parameters
    
- Local variables
    
- Return addresses
    

Whenever a function is called, information is pushed onto the stack.

When the function finishes, that information is removed.

Think of your study desk.

You place materials needed for the current task and remove them afterward.

Exam Favorite:

```text
Heap  → Dynamic Allocation
Stack → Temporary Function Data
```

Students mix these up all the time.

---

## 2.3 Process States

Every process moves through a lifecycle.

Think about a student taking an exit exam.

### New State

The student has just registered.

The process has just been created.

---

### Ready State

The student is seated and waiting for the exam to begin.

The process is waiting for CPU allocation.

Important:

```text
Ready ≠ Running
```

This appears in MCQs regularly.

---

### Running State

The student is actively writing answers.

The CPU is currently executing the process.

---

### Waiting / Blocked State

The student is waiting for clarification from the instructor.

The process is waiting for an external event.

Examples:

- Disk operation
    
- Keyboard input
    
- Network response
    

---

### Terminated State

The student submits the paper and leaves.

The process has finished execution.

---

## 2.4 Process Control Block (PCB)

### PCB (A Passport for a Process)

Imagine airport immigration.

Every traveler has a passport containing important information.

Similarly, every process has a PCB.

The Process Control Block contains information needed by the operating system to manage the process.

A PCB stores:

- Process ID (PID)
    
- Process State
    
- Program Counter
    
- CPU Registers
    
- Scheduling Information
    
- Memory Information
    
- I/O Status Information
    

Without a PCB, the operating system would lose track of the process.

Common Exit Question:

Which data structure contains information about a process?

Answer:

PCB.

---

## 2.5 Context Switching

### Context Switch (Teacher Switching Between Students)

Suppose a teacher helps Student A.

Before helping Student B, the teacher must remember where Student A stopped.

Later, the teacher can continue from that exact point.

The operating system does the same thing.

During a context switch:

1. Save current process state into PCB.
    
2. Load next process state from PCB.
    
3. Resume execution.
    

This operation is called a Context Switch.

Important:

Context switching is overhead.

The CPU is not doing useful work during the switch.

---

## 2.6 Process Creation

### Process Creation (A Family Tree)

Processes are often created by other processes.

The creating process is called the Parent Process.

The newly created process is called the Child Process.

This forms a Process Tree.

In UNIX:

```c
fork()
```

creates a child process.

After creation:

```c
exec()
```

can replace the child process with a completely new program.

Exam Favorite:

```text
fork() → Create Child Process

exec() → Replace Process Memory with New Program
```

Students confuse these two constantly.

---

## 2.7 Process Termination

A process can terminate:

### Normally

Using:

```c
exit()
```

The process finishes successfully.

---

### Abnormally

Using:

```c
abort()
```

The process terminates due to an error.

---

### Cascading Termination

If a parent process terminates, the operating system may automatically terminate all its child processes.

Think of closing a company branch and shutting down all departments within it.

---

### Chapter 2: Process Management - Practice Questions

#### 1. What is the main difference between a program and a process?

A. A program requires CPU resources while a process does not

B. A process is stored on disk while a program is stored in memory

C. A program is passive while a process is actively executing

D. There is no difference

**Answer: C**

**Explanation:**  
A program is just a set of instructions stored on disk. A process is a program that is currently executing and using system resources.

---

#### 2. Which memory section contains dynamically allocated memory?

A. Stack

B. Data Section

C. Text Section

D. Heap

**Answer: D**

**Explanation:**  
The heap stores memory allocated during runtime using functions such as `malloc()` and operators such as `new`.

---

#### 3. Which memory section stores local variables and function parameters?

A. Heap

B. Stack

C. Data Section

D. Text Section

**Answer: B**

**Explanation:**  
The stack stores temporary information needed during function execution.

---

#### 4. A process waiting for CPU allocation is in which state?

A. Running

B. Waiting

C. Ready

D. Terminated

**Answer: C**

**Explanation:**  
The Ready state means the process has everything it needs except CPU time.

**Exam Trap:**  
Ready ≠ Running

---

#### 5. Which process state indicates that instructions are currently being executed?

A. Ready

B. Waiting

C. New

D. Running

**Answer: D**

**Explanation:**  
The Running state means the CPU is actively executing the process.

---

#### 6. What does PCB stand for?

A. Process Communication Block

B. Program Control Buffer

C. Process Control Block

D. Program Communication Buffer

**Answer: C**

**Explanation:**  
A PCB stores all information needed by the operating system to manage a process.

---

#### 7. Which of the following information is typically stored in a PCB?

A. Process State

B. Program Counter

C. CPU Registers

D. All of the Above

**Answer: D**

**Explanation:**  
The PCB contains all information necessary to pause and resume a process.

---

#### 8. During a context switch, the operating system first:

A. Deletes the process

B. Saves the current process state

C. Terminates all processes

D. Clears memory

**Answer: B**

**Explanation:**  
The OS saves the current process information in its PCB before loading another process.

---

#### 9. Which statement about context switching is TRUE?

A. It increases disk capacity

B. It is useful work performed by the CPU

C. It is overhead because no user process is executing

D. It only occurs in single-process systems

**Answer: C**

**Explanation:**  
While switching processes, the CPU is busy saving and restoring states rather than executing useful work.

---

#### 10. In UNIX systems, which system call creates a child process?

A. exec()

B. create()

C. fork()

D. spawn()

**Answer: C**

**Explanation:**  
`fork()` creates a new child process that is initially identical to the parent process.

---

#### 11. What is the primary purpose of the exec() system call?

A. Create a PCB

B. Create a child process

C. Replace the current process image with a new program

D. Schedule a process

**Answer: C**

**Explanation:**  
After a fork, exec() is often used to load a completely different program into the child process.

---

#### 12. Which IPC mechanism is generally faster?

A. Message Passing

B. Shared Memory

C. Remote Procedure Call

D. Pipe

**Answer: B**

**Explanation:**  
Shared memory avoids copying messages between processes, making it very fast. However, synchronization is required.

---

#### 13. Why is synchronization needed in Shared Memory IPC?

A. To increase memory size

B. To prevent processes from interfering with each other's data

C. To create new processes

D. To terminate processes

**Answer: B**

**Explanation:**  
Multiple processes may access the same memory simultaneously, causing inconsistencies if synchronization is not used.

---

#### 14. What is a thread?

A. A storage device

B. A lightweight process and basic unit of CPU utilization

C. A scheduling algorithm

D. A memory allocation technique

**Answer: B**

**Explanation:**  
Threads share resources within a process while maintaining their own execution context.

---

#### 15. Which of the following is an advantage of multithreading?

A. Increased responsiveness

B. Better resource sharing

C. Improved scalability on multicore systems

D. All of the Above

**Answer: D**

**Explanation:**  
Multithreading improves responsiveness, allows resource sharing, reduces overhead, and takes advantage of multiple CPU cores.

---

### High-Frequency Exit Exam Concepts from This Chapter

Based on the exit exams you've shared, pay special attention to:

1. Program vs Process
2. Process States
3. PCB
4. Context Switching
5. fork() and exec()
6. Shared Memory vs Message Passing
7. Threads vs Processes
8. Advantages of Multithreading
9. User Threads vs Kernel Threads
10. Preemptive vs Non-Preemptive Scheduling
11. FCFS
12. Round Robin
13. SJF
14. Priority Scheduling
15. Starvation and Aging