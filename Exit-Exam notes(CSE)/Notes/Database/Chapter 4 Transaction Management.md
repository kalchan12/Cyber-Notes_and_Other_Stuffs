
## 4.1 Introduction to Transaction Processing

Before diving into transactions themselves, it is important to understand the environment in which they operate.

### Single-User vs. Multiuser Systems

|System Type|Description|Examples|
|---|---|---|
|**Single-User**|Only one user can access the DBMS at a time|Personal desktop applications|
|**Multiuser**|Many users access the DBMS simultaneously and concurrently|Banking systems, airline reservations, e-commerce platforms|

The vast majority of real-world database systems are **multiuser** systems. This introduces a fundamental challenge: how do you ensure that multiple users accessing and modifying the same data simultaneously do not corrupt each other's work?

### How Concurrency is Achieved

**Concurrency** is the appearance of simultaneous execution of multiple processes. It is achieved through two mechanisms:

#### Interleaved Processing (Single CPU)

The operating system rapidly switches between multiple processes on a **single CPU**, giving the illusion of parallelism. While one process is waiting for a slow I/O operation (reading from disk), the CPU is assigned to another process instead of sitting idle.

- ✅ Keeps the CPU constantly busy
- ✅ Increases overall system throughput
- ✅ Minimizes unpredictable delays for individual users
- Common scheduling approach: **round-robin algorithm**

#### Parallel Processing (Multiple CPUs)

Processes are **genuinely executed simultaneously** across multiple CPUs or processor cores.

- ✅ True simultaneous execution
- Higher hardware cost
- Used in high-performance database servers

> 📌 **Note:** Interleaved processing on a single CPU is the more common scenario tested in exams. Understand that it is the CPU _switching between_ processes very quickly, not truly running them at the same moment.

---

## 4.2 Transaction Concepts and Operations

### What is a Transaction?

A **transaction** is a logical, atomic unit of database work. It is a sequence of one or more database operations (reads, writes, inserts, updates, deletes) that together accomplish a single meaningful business task.

**Key idea:** A transaction is treated as a single indivisible unit — either ALL of its operations succeed and their results are permanently saved, or NONE of them take effect. There is no in-between.

**Classic example — Bank Transfer:**

```
BEGIN TRANSACTION
  1. READ account A balance         → $1,000
  2. SUBTRACT $200 from account A  → $800
  3. WRITE new balance to account A
  4. READ account B balance         → $500
  5. ADD $200 to account B          → $700
  6. WRITE new balance to account B
END TRANSACTION
```

If the system crashes after step 3 but before step 6, account A has lost $200 but account B never received it. Without transaction management, this leaves the database in an inconsistent, corrupted state. Transactions prevent this.

### Transaction Boundaries

Transactions are defined by **Begin** and **End** boundary statements in the application program. Everything between these boundaries is treated as one atomic unit.

### Database Operations at the Low Level

|Operation|What It Does|
|---|---|
|`read_item(X)`|Locates the disk block containing item X, copies it into a main memory buffer, and assigns the value to a program variable|
|`write_item(X)`|Copies the value from a program variable back to the main memory buffer, which is eventually written back to disk|

**Granularity** refers to the size of the data item being processed — it can range from a single field value, to a full record (row), to an entire disk block. Finer granularity allows more concurrency but requires managing more locks.

---

## 4.3 The Need for Concurrency Control — Three Classic Problems

When multiple transactions execute concurrently without any control, three serious problems can arise. Understanding these problems is essential because concurrency control exists specifically to prevent them.

### Problem 1: The Lost Update Problem

**What happens:** Two transactions read the same data item, both modify it based on the value they read, and then both write it back. The second write overwrites the first, permanently losing the first transaction's change.

```
T1: Read(X) → X = 100
T2: Read(X) → X = 100        ← both read the same original value
T1: X = X - 10 → X = 90
T2: X = X + 50 → X = 150
T1: Write(X) → X = 90
T2: Write(X) → X = 150       ← T1's update is completely lost!
```

The correct final value should be: 100 - 10 + 50 = 140. But T1's deduction was overwritten and lost.

### Problem 2: The Temporary Update (Dirty Read) Problem

**What happens:** Transaction T1 modifies a data item and then fails (aborts) before committing. But Transaction T2 already read the modified, uncommitted value. When T1 rolls back, T2 is now working with data that never officially existed.

```
T1: Write(X) → X = 200       ← T1 updates X
T2: Read(X)  → X = 200       ← T2 reads the updated value
T1: ABORTS   → X rolls back to 100   ← T1 reverses its change
T2 continues working with 200  ← T2 is using "dirty" (invalid) data!
```

This is also called a **Dirty Read** — T2 read a value that was never actually committed.

### Problem 3: The Incorrect Summary Problem

**What happens:** Transaction T1 is computing an aggregate (like a SUM or COUNT) over a set of records. Simultaneously, Transaction T2 is updating some of those same records. T1's summary includes some old values and some new values — resulting in an inaccurate, meaningless aggregate.

```
T1: Computing SUM of all account balances
T2: Transferring $500 from Account A to Account B
T1 reads Account A AFTER T2 deducted (gets lower value)
T1 reads Account B BEFORE T2 added (gets old value)
→ T1's SUM is $500 short — neither the old total nor the new total
```

> ⭐ **Exam Tip:** These three problems — Lost Update, Dirty Read, and Incorrect Summary — are among the most frequently tested concepts in database transaction chapters. Be able to identify which problem a given scenario describes.

---

## 4.4 Transaction Failures and Recovery

A well-designed DBMS must guarantee that the database is never left in an inconsistent state, regardless of what type of failure occurs. The DBMS ensures that either **all operations of a transaction succeed**, or the transaction has **absolutely no effect** on the database.

### Types of Transaction Failures

|Failure Type|Description|Example|
|---|---|---|
|**System Crash**|Hardware or software failure causing loss of main memory contents|Power outage, OS crash|
|**Transaction/System Error**|Logical or arithmetic error during execution|Integer overflow, division by zero, infinite loop|
|**Exception Condition**|A programmatic condition the application treats as an error|Insufficient funds for withdrawal, record not found|
|**Concurrency Control Enforcement**|The system deliberately aborts a transaction|Deadlock prevention, serializability violation|
|**Disk Failure**|Physical damage to storage media|Disk head crash, corrupted sectors|
|**Physical Catastrophe**|Environmental disaster|Fire, flood, power surge, theft|

---

## 4.5 Transaction States and the System Log

### Transaction State Diagram

```
              ┌─────────┐
    Begin ──► │ ACTIVE  │ ── partial commit ──► PARTIALLY COMMITTED
              └────┬────┘                              │
                   │ failure                           │ commit
                   ▼                                   ▼
               FAILED ◄──────────── ──────────── COMMITTED
                   │
                   ▼
              TERMINATED
```

|State|Meaning|
|---|---|
|**Active**|The transaction is currently executing — reading and writing data|
|**Partially Committed**|All read/write operations have finished, but changes are not yet permanently guaranteed|
|**Committed**|All changes are permanently saved to the database — the transaction is complete and successful|
|**Failed**|A problem occurred; the transaction must be rolled back (aborted)|
|**Terminated**|The transaction has left the system (either after committing or after aborting)|

### The System Log

To support recovery, the DBMS maintains a **System Log** — a sequential record written to stable storage that tracks every significant transaction event:

- `[start_transaction, T]` — transaction T has begun
- `[write_item, T, X, old_value, new_value]` — T wrote item X, changing it from old to new
- `[read_item, T, X]` — T read item X
- `[commit, T]` — T has committed successfully
- `[abort, T]` — T has been aborted

The log is the foundation of all database recovery. By replaying or reversing log entries, the DBMS can **redo** committed transactions that were lost in a crash, or **undo** the effects of failed transactions.

---

## 4.6 The ACID Properties

The **ACID properties** are the four fundamental guarantees that every transaction must provide to ensure database correctness and reliability. They are the gold standard of transaction management.

|Property|Full Meaning|Description|Managed By|
|---|---|---|---|
|**A** — Atomicity|All or nothing|Every operation in the transaction executes completely, or none of them do. No partial transactions.|Recovery Subsystem|
|**C** — Consistency|Valid state to valid state|A transaction takes the database from one valid, consistent state to another valid state. It never violates integrity constraints.|Application Programmer|
|**I** — Isolation|Hidden until committed|A transaction's intermediate, uncommitted changes are invisible to all other concurrent transactions. Each transaction appears to execute in isolation.|Concurrency Control Subsystem|
|**D** — Durability|Permanent once committed|Once a transaction commits, its changes are permanently written to the database and will survive any subsequent failure (crash, power loss, etc.).|Recovery Subsystem|

> ⭐ **Exam Tip:** The ACID acronym and what each letter stands for is one of the most commonly tested facts in database exams. Also know WHICH subsystem is responsible for each property.

### ACID in the Bank Transfer Example

- **Atomicity:** Either both the debit AND the credit happen, or neither does
- **Consistency:** The total money in the system is the same before and after the transfer
- **Isolation:** No other transaction sees the balance between the debit and the credit steps
- **Durability:** Once the transfer commits, it is permanently recorded — even if the server crashes 1 second later

---

## 4.7 Characterizing Schedules: Serializability

### What is a Schedule?

A **schedule** (also called a **history**) is the specific order in which operations from multiple concurrent transactions are interleaved and executed. Managing schedules correctly is the core job of the concurrency control system.

### Serial vs. Non-Serial Schedules

|Schedule Type|Description|Performance|Safety|
|---|---|---|---|
|**Serial**|Transactions execute one completely at a time — no interleaving|Slow — poor CPU utilization|Always correct|
|**Non-Serial (Interleaved)**|Operations from multiple transactions are interleaved|Fast — good CPU utilization|May be unsafe|
|**Conflict Serializable**|A non-serial schedule equivalent in effect to some serial schedule|Fast|Safe — equivalent to serial|

The goal of concurrency control is to allow non-serial schedules (for performance) while only permitting those that are **conflict serializable** (for correctness).

### Conflicting Operations

Two operations **conflict** if ALL three of the following are true:

1. They belong to **different transactions**
2. They access the **same data item**
3. **At least one** of them is a **write** operation

|Operation Pair|Same item?|Conflict?|
|---|---|---|
|Read(T1, X) and Read(T2, X)|✅|❌ — two reads never conflict|
|Read(T1, X) and Write(T2, X)|✅|✅ — read-write conflict|
|Write(T1, X) and Read(T2, X)|✅|✅ — write-read conflict|
|Write(T1, X) and Write(T2, X)|✅|✅ — write-write conflict|

### Testing Serializability: The Precedence Graph

The **Precedence Graph** (also called a Serialization Graph) is the standard tool for testing whether a schedule is conflict serializable:

1. Create one **node** for each transaction
2. Draw a **directed edge** from Ti → Tj whenever an operation in Ti conflicts with a later operation in Tj
3. **If the graph has NO cycles → the schedule IS conflict serializable (safe)**
4. **If the graph has a cycle → the schedule is NOT serializable (unsafe)**

> ⭐ **Exam Tip:** A serial schedule is ALWAYS correct (no cycles possible). A non-serial schedule is safe ONLY if its precedence graph is acyclic (no cycles).

---

## 4.8 Characterizing Schedules: Recoverability

Beyond serializability, schedules must also be **recoverable** — meaning if a transaction fails, the database can be correctly restored.

### Three Levels of Schedule Recoverability (from weakest to strongest)

|Schedule Type|Rule|Protection Level|
|---|---|---|
|**Recoverable**|A transaction T2 must not commit until all transactions whose data T2 read have first committed|Basic recovery is possible|
|**Cascadeless**|A transaction may only read data written by already-committed transactions — no reading of uncommitted data|Prevents cascade aborts (one abort causing many others)|
|**Strict**|A transaction may neither read NOR write a data item until the last transaction that wrote it has committed or aborted|Simplest to implement and recover from; most restrictive|

> 📌 **Note:** Every strict schedule is cascadeless, and every cascadeless schedule is recoverable — they form a hierarchy of increasingly strict safety guarantees.

---

## 4.9 Transaction Support in SQL

### Access Modes

- `READ ONLY` — the transaction will only read data (no modifications)
- `READ WRITE` — the transaction can both read and modify data (default)

### SQL Isolation Levels

SQL provides four **isolation levels** that trade safety for performance. Higher isolation = safer but slower; lower isolation = faster but more anomalies possible.

|Isolation Level|Dirty Read|Non-Repeatable Read|Phantom Read|Performance|
|---|---|---|---|---|
|`READ UNCOMMITTED`|✅ Possible|✅ Possible|✅ Possible|Fastest|
|`READ COMMITTED`|❌ Prevented|✅ Possible|✅ Possible|Fast|
|`REPEATABLE READ`|❌ Prevented|❌ Prevented|✅ Possible|Moderate|
|`SERIALIZABLE`|❌ Prevented|❌ Prevented|❌ Prevented|Slowest (Safest)|

### Types of Read Anomalies

|Anomaly|Conflict Type|Description|
|---|---|---|
|**Dirty Read**|WR|Transaction T2 reads data that T1 has written but not yet committed. If T1 aborts, T2 has read invalid data.|
|**Non-Repeatable Read**|RW|T1 reads a value, T2 modifies and commits it, T1 reads it again and gets a different value.|
|**Phantom Read**|—|T1 queries a set of rows, T2 inserts a new row that matches the query condition, T1 re-runs the query and finds a "phantom" new row.|
|**Overwriting Uncommitted Data**|WW|Two transactions write to the same item simultaneously without control.|

> ⭐ **Exam Tip:** `SERIALIZABLE` is the default and safest isolation level. `READ UNCOMMITTED` is the most dangerous — it allows all anomalies including dirty reads.

# Chapter 4 — Practice Questions

### Transaction Management

#### 25 Multiple Choice Questions · Correct answers marked with ✅

---

**1.** What is the PRIMARY difference between interleaved processing and parallel processing?

- A) Interleaved processing uses multiple CPUs; parallel processing uses one CPU
- B) **Interleaved processing rapidly switches between processes on a single CPU; parallel processing genuinely executes processes simultaneously on multiple CPUs** ✅
- C) Interleaved processing is only used in single-user systems; parallel processing is for multiuser systems
- D) There is no meaningful difference — both terms describe the same concept

> **Answer: B** — Interleaved processing gives the _illusion_ of simultaneity on one CPU by rapidly switching between processes during I/O wait times. Parallel processing achieves _true_ simultaneity using multiple CPUs running at the same time.

---

**2.** Which of the following BEST defines a database transaction?

- A) A single SQL SELECT statement that retrieves data from one table
- B) The process of creating a new table and inserting the first record into it
- C) **A logical, atomic unit of database work consisting of one or more operations that must either all succeed or have no effect** ✅
- D) Any database operation that modifies data, excluding read operations

> **Answer: C** — A transaction is atomic — it is treated as a single indivisible unit. Either ALL operations within it succeed and are committed, or NONE of them take effect (they are rolled back).

---

**3.** In the bank transfer example (debit Account A, credit Account B), the system crashes after debiting A but before crediting B. Which ACID property ensures the debit is reversed?

- A) Consistency
- B) Isolation
- C) Durability
- D) **Atomicity** ✅

> **Answer: D** — Atomicity guarantees the "all or nothing" rule. Since the transaction did not complete fully, the partial debit must be rolled back entirely. The database is left as if the transaction never began.

---

**4.** The `read_item(X)` database operation does which of the following?

- A) Writes the value of variable X from memory directly to the disk
- B) Creates a new record X in the database file
- C) Deletes item X from the disk and removes it from memory
- D) **Locates the disk block containing X, copies it to a memory buffer, and assigns the value to a program variable** ✅

> **Answer: D** — `read_item(X)` is a three-step process: find X on disk → copy to main memory buffer → assign to program variable. `write_item(X)` is the reverse: copy from variable → buffer → eventually write back to disk.

---

**5.** Two bank transactions both read an account balance of $1,000 simultaneously. T1 subtracts $200 and writes $800. T2 adds $300 and writes $1,300. The final balance is $1,300 — but T1's deduction was lost. This is an example of:

- A) The Dirty Read (Temporary Update) Problem
- B) The Incorrect Summary Problem
- C) **The Lost Update Problem** ✅
- D) A deadlock between two transactions

> **Answer: C** — The Lost Update Problem occurs when two transactions read the same value, both modify it, and the second write overwrites the first. T1's $200 deduction is completely lost. The correct final balance should be $1,100.

---

**6.** Transaction T1 updates a record and then ABORTS (rolls back). Before T1 aborts, Transaction T2 had already READ the updated value. T2 is now working with data that officially never existed. This is called:

- A) The Lost Update Problem
- B) The Incorrect Summary Problem
- C) A phantom read
- D) **The Dirty Read (Temporary Update) Problem** ✅

> **Answer: D** — A Dirty Read occurs when a transaction reads data written by another transaction that has not yet committed (and may still abort). The read data is "dirty" — it may be rolled back, making T2's work based on invalid data.

---

**7.** A transaction is computing a SUM of all account balances while another transaction is simultaneously transferring money between accounts. The sum result is neither the old total nor the new total. This is:

- A) The Lost Update Problem
- B) The Dirty Read Problem
- C) **The Incorrect Summary Problem** ✅
- D) A non-repeatable read anomaly

> **Answer: C** — The Incorrect Summary Problem occurs when an aggregate calculation (SUM, COUNT, AVG) spans records that are being simultaneously updated by another transaction, producing an inaccurate result that reflects a mix of old and new values.

---

**8.** Which type of transaction failure occurs due to a disk head crash or physical power outage?

- A) Transaction/System Error
- B) Exception Condition
- C) Concurrency Control Enforcement
- D) **Disk Failure or Physical Catastrophe** ✅

> **Answer: D** — Disk Failures and Physical Catastrophes (power outages, fires, hardware damage) are the most severe failure type, destroying physical storage. This is distinct from System Crashes (software/memory failure) and Transaction Errors (logical programming mistakes).

---

**9.** A transaction that has completed all its read/write operations but whose changes have not yet been permanently guaranteed to the disk is in which state?

- A) Active State
- B) **Partially Committed State** ✅
- C) Committed State
- D) Failed State

> **Answer: B** — The Partially Committed State means all operations have finished executing, but the final commit (permanent write to disk) has not yet been confirmed. The transaction could still fail at this stage before reaching the Committed State.

---

**10.** Which ACID property is managed by the APPLICATION PROGRAMMER rather than the DBMS subsystem?

- A) Atomicity
- B) **Consistency** ✅
- C) Isolation
- D) Durability

> **Answer: B** — Consistency is the responsibility of the application programmer. They must write transactions that correctly enforce business rules and take the database from one valid state to another. Atomicity and Durability are managed by the Recovery subsystem; Isolation by the Concurrency Control subsystem.

---

**11.** Which ACID property guarantees that once a transaction commits, its changes survive even a subsequent system crash?

- A) Atomicity
- B) Consistency
- C) Isolation
- D) **Durability** ✅

> **Answer: D** — Durability guarantees that committed changes are permanent. Even if the server crashes 1 second after a commit, the changes must be recoverable (typically through the system log). Durability is managed by the Recovery subsystem.

---

**12.** The ACID property of ISOLATION means:

- A) Each transaction must run on its own dedicated CPU without sharing resources
- B) Transactions in different user sessions cannot access the same database tables
- C) **A transaction's intermediate, uncommitted changes must be invisible to all other concurrent transactions** ✅
- D) Each database table can only be modified by one transaction per day

> **Answer: C** — Isolation ensures that concurrent transactions do not see each other's uncommitted (intermediate) data. Each transaction appears to execute in isolation, even though they may be physically interleaved. This prevents dirty reads and other concurrency anomalies.

---

**13.** A schedule in which transactions execute completely one at a time with zero interleaving is called:

- A) A conflict serializable schedule
- B) A cascadeless schedule
- C) A recoverable schedule
- D) **A serial schedule** ✅

> **Answer: D** — A serial schedule executes transactions sequentially — T1 completes entirely before T2 begins. Serial schedules are ALWAYS correct but have poor performance because they underutilize the CPU during I/O wait times.

---

**14.** For two operations to be considered "conflicting," which THREE conditions must ALL be true?

- A) Same transaction, same data item, at least one is a read
- B) Different transactions, different data items, both are writes
- C) Same transaction, different data items, at least one is a write
- D) **Different transactions, same data item, at least one is a write** ✅

> **Answer: D** — The three conditions for conflict: (1) operations belong to DIFFERENT transactions, (2) they access the SAME data item, and (3) at least ONE of them is a WRITE. Two reads on the same item by different transactions do NOT conflict.

---

**15.** In a Precedence Graph used to test serializability, what does the presence of a CYCLE indicate?

- A) The schedule is conflict serializable — it is safe to execute
- B) The schedule is serial and requires no concurrency control
- C) **The schedule is NOT conflict serializable — it is unsafe** ✅
- D) A deadlock has occurred between the transactions in the cycle

> **Answer: C** — In a Precedence (Serialization) Graph, nodes = transactions and directed edges = conflicts. A cycle means the schedule cannot be reordered into any equivalent serial schedule — it is NOT conflict serializable and may produce incorrect results.

---

**16.** Which schedule type ensures no transaction commits until all transactions whose data it READ have also committed first?

- A) Serial schedule
- B) Strict schedule
- C) Cascadeless schedule
- D) **Recoverable schedule** ✅

> **Answer: D** — A Recoverable Schedule requires that T2 does not commit before T1, if T2 read data written by T1. This ensures that if T1 aborts after T2 has already read its data, the situation can still be correctly recovered.

---

**17.** A Cascadeless Schedule prevents which specific problem?

- A) Lost updates caused by concurrent writes
- B) Incorrect summaries from aggregate functions
- C) Phantom rows appearing in repeated queries
- D) **A chain reaction of aborts where one transaction's failure forces many others to also abort** ✅

> **Answer: D** — Cascadeless schedules prevent "cascading aborts" by ensuring transactions only read data written by already-committed transactions. If T1 aborts, T2 (which never read T1's uncommitted data) is unaffected and does not need to abort.

---

**18.** Which SQL isolation level allows DIRTY READS — reading uncommitted data from other transactions?

- A) SERIALIZABLE
- B) REPEATABLE READ
- C) READ COMMITTED
- D) **READ UNCOMMITTED** ✅

> **Answer: D** — READ UNCOMMITTED is the lowest and most dangerous isolation level. It allows dirty reads (reading uncommitted data), non-repeatable reads, and phantom reads. It offers the highest performance but the lowest data consistency guarantees.

---

**19.** Transaction T1 reads a value, Transaction T2 then modifies and commits that value, and T1 reads the same value again and gets a DIFFERENT result. This anomaly is called:

- A) A dirty read
- B) A phantom read
- C) An overwrite of uncommitted data
- D) **A non-repeatable read** ✅

> **Answer: D** — A Non-Repeatable Read (RW conflict) occurs when a transaction reads the same data item twice and gets different values because another transaction modified and committed it between the two reads. REPEATABLE READ isolation level prevents this.

---

**20.** Which isolation level prevents ALL anomalies including dirty reads, non-repeatable reads, AND phantom reads?

- A) READ UNCOMMITTED
- B) READ COMMITTED
- C) REPEATABLE READ
- D) **SERIALIZABLE** ✅

> **Answer: D** — SERIALIZABLE is the highest and safest SQL isolation level. It prevents all concurrency anomalies including phantoms (new rows matching a query condition inserted by another transaction). It is the default in most DBMS implementations.

---

**21.** A "Phantom Read" occurs when:

- A) A transaction reads a value that was later rolled back by another transaction
- B) A transaction reads the same value twice and gets a different result
- C) Two transactions simultaneously write to the same data item
- D) **A transaction re-runs a query and finds new rows that were inserted by another committed transaction since the first execution** ✅

> **Answer: D** — Phantoms are rows that "appear" between two executions of the same query within one transaction. T1 runs SELECT WHERE salary > 50000, T2 inserts a new employee with salary 60000 and commits, T1 re-runs the query and finds the new "phantom" row.

---

**22.** The System Log maintained by the DBMS primarily serves which purpose?

- A) To store the SQL source code of all transactions for auditing purposes
- B) To track user login/logout times for security monitoring
- C) **To record all transaction operations (start, write, commit, abort) to enable recovery after failures** ✅
- D) To cache frequently accessed data items in main memory for faster access

> **Answer: C** — The System Log is a sequential record written to stable storage that tracks every significant transaction event. It is the foundation of database recovery — by replaying (REDO) or reversing (UNDO) log entries, the DBMS can restore the database to a consistent state after any failure.

---

**23.** Which of the following transaction states is FINAL — the transaction has left the system entirely?

- A) Committed
- B) Failed
- C) Partially Committed
- D) **Terminated** ✅

> **Answer: D** — The Terminated State is the final state. A transaction reaches Terminated after it has either successfully Committed or was Aborted from the Failed state. In both cases, the transaction is completely done and removed from the system.

---

**24.** Interleaved processing on a single CPU improves system performance primarily by:

- A) Running each transaction on a dedicated CPU core for maximum speed
- B) Splitting each transaction into smaller sub-transactions that run in parallel
- C) Eliminating the need for disk I/O by keeping all data in memory
- D) **Keeping the CPU busy by switching to another transaction while the current one waits for slow I/O operations** ✅

> **Answer: D** — The key benefit of interleaved processing is CPU utilization. When T1 is waiting for a disk read (slow I/O), the CPU would otherwise sit idle. Instead, the OS switches to T2, keeping the CPU productive. This increases overall system throughput.

---

**25.** Which of the following is TRUE about a SERIAL schedule?

- A) Serial schedules are always faster than interleaved schedules
- B) Serial schedules can sometimes produce incorrect results due to ordering issues
- C) Serial schedules require a Precedence Graph check to verify correctness
- D) **Serial schedules are always correct — they never produce the three concurrency problems (lost update, dirty read, incorrect summary)** ✅

> **Answer: D** — Serial schedules execute one transaction at a time with zero interleaving. Since no two transactions access data simultaneously, none of the three concurrency problems can occur. They are always correct — the trade-off is performance, not correctness.

---

_End of Chapter 4 Practice Questions_