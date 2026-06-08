
# 3.1 Background and Address Binding

Before a program can execute, it must be loaded from secondary storage (usually a hard disk or SSD) into main memory (RAM). The CPU can only execute instructions that are present in main memory. Therefore, every program must go through a process of loading and memory allocation before execution begins.

One of the fundamental responsibilities of the operating system is memory management. The operating system must determine where a process will reside in memory, ensure that processes do not interfere with one another, and provide a mechanism for translating program-generated addresses into actual memory locations.

## Memory Protection

In a multiprogramming environment, multiple processes may reside in memory simultaneously. Without protection mechanisms, one process could accidentally or intentionally modify another process's memory space, causing data corruption, crashes, or security vulnerabilities.

To prevent this, operating systems use hardware-supported memory protection.

Two important registers are used:

### Base Register

The base register contains the smallest legal physical memory address that a process is allowed to access.

For example:

```text
Base Register = 3000
```

This means the process cannot legally access addresses below 3000.

### Limit Register

The limit register specifies the size of the memory region allocated to the process.

For example:

```text
Base = 3000
Limit = 1000
```

Valid addresses become:

```text
3000 → 3999
```

If the process attempts to access address 4000 or higher, the access is considered illegal.

### Addressing Error

Whenever a process attempts to access memory outside its allocated region, the hardware immediately detects the violation and generates a trap to the operating system.

The operating system can then terminate the process or take corrective action.

This mechanism is one of the most important security features of modern operating systems.

### Exam Notes

```text
Base Register  = Smallest legal address
Limit Register = Size of allocated memory region
```

A common MCQ asks:

> Which register stores the smallest legal memory address?

Answer:

```text
Base Register
```

---

## Address Binding

A program contains instructions and data, but before execution these instructions must be associated with actual memory addresses.

The process of mapping program instructions and data to memory locations is called Address Binding.

Address binding can occur at three different stages.

### Compile-Time Binding

If the compiler already knows where the program will reside in memory, it generates absolute addresses directly.

Example:

```text
Instruction stored at address 5000
Variable stored at address 5200
```

Since the addresses are fixed, moving the program to another location requires recompilation.

Advantages:

- Simple
    
- Fast execution
    

Disadvantages:

- Inflexible
    
- Requires recompilation if memory location changes
    

---

### Load-Time Binding

In many systems, the compiler does not know the final memory location of the program.

Instead, it generates relocatable code.

The actual addresses are assigned when the loader places the program into memory.

Advantages:

- More flexible than compile-time binding
    
- No recompilation required when location changes
    

---

### Execution-Time Binding

Modern operating systems usually perform address binding during execution.

The CPU generates logical addresses during runtime and hardware translates them into physical addresses.

Advantages:

- Maximum flexibility
    
- Supports virtual memory
    
- Allows processes to move in memory during execution
    

Disadvantages:

- Requires hardware support
    

Examples:

```text
MMU
Base Register
Limit Register
```

### Exam Notes

```text
Compile Time → Absolute Code

Load Time → Relocatable Code

Execution Time → Runtime Address Translation
```

These questions focus on the concepts covered in:

- Address Binding
    
- Base & Limit Registers
    
- Logical vs Physical Address
    
- MMU
    
- Dynamic Loading
    
- Dynamic Linking
    
- Swapping
    
- Contiguous Allocation
    
- Fragmentation
    
- Paging
    
- Segmentation
    
- TLB
    
- Virtual Memory
    
- Demand Paging
    
- Page Faults
    
- Page Replacement
    
- Thrashing
    

These are exactly the areas that tend to appear in OS exit exams.

---

# Chapter 3: Memory Management & Virtual Memory Practice Questions

### 1. What is the primary purpose of memory protection?

A. Increase CPU speed

B. Prevent processes from accessing unauthorized memory locations

C. Reduce page faults

D. Increase disk capacity

**Answer: B**

**Explanation:** Memory protection prevents one process from interfering with another process's memory.

---

### 2. Which register contains the smallest legal physical address a process can access?

A. Program Counter

B. Limit Register

C. Base Register

D. Stack Pointer

**Answer: C**

**Explanation:** The Base Register stores the starting address of the allocated memory region.

---

### 3. The Limit Register is used to:

A. Store page numbers

B. Store CPU instructions

C. Specify the size of the process memory region

D. Store physical addresses

**Answer: C**

**Explanation:** The Limit Register defines the range of valid memory addresses.

---

### 4. What happens when a process attempts to access memory outside its allocated range?

A. Memory is automatically expanded

B. A trap is generated

C. The process gains kernel privileges

D. Nothing happens

**Answer: B**

**Explanation:** Hardware detects the illegal access and generates a trap to the OS.

---

### 5. Address binding refers to:

A. Linking processes together

B. Mapping instructions and data to memory addresses

C. Connecting CPUs to memory

D. Allocating CPU time

**Answer: B**

**Explanation:** Address binding determines where instructions and data reside in memory.

---

### 6. Which type of binding generates absolute code?

A. Load-Time Binding

B. Execution-Time Binding

C. Compile-Time Binding

D. Dynamic Binding

**Answer: C**

**Explanation:** Compile-time binding produces fixed addresses known during compilation.

---

### 7. Which type of address binding provides the greatest flexibility?

A. Compile-Time Binding

B. Load-Time Binding

C. Execution-Time Binding

D. Static Binding

**Answer: C**

**Explanation:** Addresses are translated at runtime, allowing processes to move in memory.

---

### 8. A logical address is:

A. The actual RAM address

B. The address generated by the CPU

C. The address stored on disk

D. The address used by hardware only

**Answer: B**

**Explanation:** The CPU generates logical (virtual) addresses during execution.

---

### 9. Which component translates logical addresses into physical addresses?

A. PCB

B. Cache

C. MMU

D. TLB

**Answer: C**

**Explanation:** The Memory Management Unit (MMU) performs address translation.

---

### 10. Which statement is TRUE regarding physical addresses?

A. They are generated by the CPU

B. They are generated by the compiler

C. They are the actual addresses in main memory

D. They are stored in the PCB

**Answer: C**

**Explanation:** Physical addresses correspond to real locations in RAM.

---

### 11. Dynamic Loading improves memory utilization because:

A. Entire programs are always loaded

B. Routines are loaded only when needed

C. Memory allocation is eliminated

D. Swapping is avoided

**Answer: B**

**Explanation:** Unused routines remain on disk until called.

---

### 12. Dynamic Linking delays linking until:

A. Compilation

B. Program design

C. Execution Time

D. System shutdown

**Answer: C**

**Explanation:** Libraries are connected when the program runs.

---

### 13. Swapping is the process of:

A. Exchanging CPU registers

B. Moving a process between RAM and backing store

C. Translating logical addresses

D. Linking shared libraries

**Answer: B**

**Explanation:** Processes may be temporarily moved to disk and later restored.

---

### 14. Which memory allocation strategy is generally considered the fastest?

A. Best-Fit

B. Worst-Fit

C. First-Fit

D. Paging

**Answer: C**

**Explanation:** First-Fit allocates the first hole large enough to satisfy the request.

---

### 15. External Fragmentation occurs when:

A. Free memory is scattered into small non-contiguous holes

B. Memory blocks are too large

C. Pages are missing

D. The page table is full

**Answer: A**

**Explanation:** Enough total memory exists, but it is not contiguous.

---

### 16. Which technique eliminates external fragmentation?

A. Segmentation

B. Paging

C. Swapping

D. Dynamic Linking

**Answer: B**

**Explanation:** Paging allows pages to occupy non-contiguous frames.

---

### 17. Internal Fragmentation occurs when:

A. Memory is scattered

B. Allocated memory exceeds requested memory

C. A page fault occurs

D. A process terminates

**Answer: B**

**Explanation:** Wasted space exists inside an allocated block.

---

### 18. In paging, logical memory is divided into:

A. Frames

B. Segments

C. Pages

D. Clusters

**Answer: C**

**Explanation:** Logical memory is divided into pages, while physical memory is divided into frames.

---

### 19. What is the primary purpose of the TLB?

A. Store process states

B. Store page tables permanently

C. Speed up address translation

D. Manage CPU scheduling

**Answer: C**

**Explanation:** The Translation Lookaside Buffer caches recent page table entries.

---

### 20. A page fault occurs when:

A. The CPU crashes

B. A requested page is not currently in memory

C. A page table is deleted

D. A process enters the ready state

**Answer: B**

**Explanation:** The operating system must load the missing page into memory before execution continues.

---

# Bonus High-Frequency Exit Exam Questions

These concepts are extremely likely to appear:

### Which page replacement algorithm suffers from Belady's Anomaly?

A. FIFO

B. LRU

C. Optimal

D. Clock

**Answer: A**

---

### Which page replacement algorithm is used as the theoretical benchmark?

A. FIFO

B. LRU

C. Optimal

D. Second Chance

**Answer: C**

---

### Which page replacement algorithm replaces the page that has not been used for the longest time?

A. FIFO

B. LRU

C. Optimal

D. FCFS

**Answer: B**

---

### Thrashing occurs when:

A. CPU utilization becomes too high

B. Too many interrupts occur

C. A process spends more time paging than executing

D. Memory protection fails

**Answer: C**

---

### Which memory management scheme uses variable-sized segments?

A. Paging

B. Segmentation

C. FIFO

D. TLB

**Answer: B**

---

## Exit Exam Must-Know Concepts from This Chapter

If you had only one day before the exam, prioritize:

```text
1. Logical vs Physical Address
2. MMU
3. Address Binding
4. Internal vs External Fragmentation
5. Paging
6. Segmentation
7. TLB
8. Virtual Memory
9. Demand Paging
10. Page Fault Handling Steps
11. FIFO
12. LRU
13. Optimal Page Replacement
14. Belady's Anomaly
15. Thrashing
```

These are the concepts that show up again and again in Operating Systems exams.

