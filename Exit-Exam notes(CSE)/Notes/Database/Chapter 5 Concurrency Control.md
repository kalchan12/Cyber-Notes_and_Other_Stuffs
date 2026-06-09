
## 5.1 The Transaction Processor Architecture

The Transaction Processor is divided into two main components, each responsible for a different ACID property:

|Component|Responsibility|ACID Property|
|---|---|---|
|**Concurrency-Control Manager (Scheduler)**|Ensures that interleaved transactions produce a net effect identical to serial execution|Isolation|
|**Logging and Recovery Manager**|Ensures changes survive failures and can be undone if necessary|Durability|

### Why Concurrency Control is Necessary

- Enforces **mutual exclusion** among conflicting transactions
- Preserves database **consistency** (e.g., money can't disappear or duplicate during simultaneous bank transfers)
- Resolves **read-write** and **write-write** conflicts safely

---

## 5.2 Overview of Concurrency Control Techniques

All concurrency control techniques fall into two philosophical categories:

|Category|Philosophy|Examples|How It Works|
|---|---|---|---|
|**Conservative (Pessimistic)**|Assumes conflicts WILL happen — delays transactions proactively|Locking, Timestamping|Prevents conflicts before they occur by blocking or ordering transactions|
|**Optimistic**|Assumes conflicts are RARE — lets transactions run freely and only checks at commit time|Optimistic/Validation CC|Detects and resolves conflicts after they occur|

---

## 5.3 Locking Techniques

A **lock** is a variable (flag) associated with each data item that controls which operations are permitted on it. Before accessing any data item, a transaction must first acquire the appropriate lock.

### Lock Modes

|Lock Type|Also Called|Who Can Hold It|Who Is Blocked|
|---|---|---|---|
|**Shared Lock**|Read Lock (S)|Multiple transactions simultaneously|Anyone wanting an Exclusive lock|
|**Exclusive Lock**|Write Lock (X)|Only ONE transaction at a time|ALL other transactions (both read and write)|

### Lock Compatibility Matrix

||Shared (S) Held|Exclusive (X) Held|
|---|---|---|
|**Request Shared (S)**|✅ Compatible|❌ Must Wait|
|**Request Exclusive (X)**|❌ Must Wait|❌ Must Wait|

> The key insight: **multiple readers can coexist** — shared locks are compatible with each other. But **a writer needs exclusive access** — no other transaction can hold any lock when an exclusive lock is required.

### Lock Conversion

|Conversion|Direction|Condition|
|---|---|---|
|**Upgrade**|Shared → Exclusive|Must wait if any other transaction holds a shared lock on the same item|
|**Downgrade**|Exclusive → Shared|Safe to do at any time — allows other readers in|

### The Lock Manager

The **Lock Manager** maintains a **Lock Table** (typically implemented as a hash table or linked list) that tracks:

- Which data item is locked
- What type of lock is held
- Which transaction holds the lock
- Which transactions are waiting for the lock

### Well-Formed Transaction Rules

A transaction must follow these locking rules:

- It must **lock an item before accessing it**
- It must **not lock an item it already holds a lock on**
- It must **not unlock an item it does not currently hold**

---

## 5.4 Two-Phase Locking (2PL) Protocol

Locking alone does not guarantee serializability — the **timing** of when locks are acquired and released matters critically. The **Two-Phase Locking (2PL) Protocol** solves this.

### The 2PL Rule

> **All locking operations must precede the FIRST unlock operation.**

In other words: once a transaction releases even one lock, it can never acquire any new locks.

### The Two Phases

```
    Locks held
         │     ┌─── Peak (lock point)
         │    /│\
         │   / │ \
         │  /  │  \
         │ /   │   \
         │/    │    \─────────────
         └─────┼──────────────────► Time
          GROWING   SHRINKING
          (acquire)  (release)
```

|Phase|Also Called|What Happens|
|---|---|---|
|**Phase 1**|Growing / Locking Phase|Transaction **acquires** locks as needed. It **cannot release** any locks during this phase.|
|**Phase 2**|Shrinking / Unlocking Phase|Transaction **releases** locks. It **cannot acquire** any new locks during this phase.|

The point where a transaction holds its maximum number of locks (before any are released) is called the **lock point**.

### Variants of 2PL

|Variant|Additional Rule|Benefit|
|---|---|---|
|**Basic 2PL**|Standard two-phase approach|Guarantees serializability|
|**Conservative 2PL**|All locks acquired before the transaction begins (pre-declare all needed items)|Deadlock-free, but requires knowing all data items in advance|
|**Strict 2PL**|Exclusive locks held until commit/abort|Simplest recovery; prevents cascading aborts|
|**Rigorous 2PL**|ALL locks (shared + exclusive) held until commit/abort|Strictest form|

### 2PL Trade-offs

- ✅ **Guarantees serializability** — the fundamental correctness guarantee
- ❌ **Restricts concurrency** — transactions may have to wait longer than necessary
- ❌ **Subject to deadlocks** — two transactions can each wait for the other's lock forever

---

## 5.5 Deadlock and Starvation

### What is a Deadlock?

A **deadlock** occurs when two or more transactions are each waiting for a lock held by the other, creating an endless circular wait where none of them can proceed.

```
T1 holds lock on X, waiting for lock on Y
T2 holds lock on Y, waiting for lock on X
→ Neither can proceed — DEADLOCK
```

### Approach 1: Deadlock Prevention (Timestamp-Based)

Prevention assigns each transaction a **timestamp** when it starts (older transactions have smaller timestamps = higher priority). Two policies exist:

|Policy|Rule for Older → Younger|Rule for Younger → Older|Characteristic|
|---|---|---|---|
|**Wait-Die**|Older waits for younger|Younger **dies** (aborts)|Non-preemptive — older transactions are never killed|
|**Wound-Wait**|Older **wounds** (kills) younger|Younger waits for older|Preemptive — older transactions can force younger ones to abort|

> 📌 **Note:** In both policies, aborted transactions are restarted with their **original timestamp** (not a new one) to prevent starvation — they keep their seniority.

### Approach 2: Deadlock Detection (Wait-for Graph)

Instead of preventing deadlocks, the system detects them after they form:

1. The scheduler maintains a **Wait-for Graph** — nodes are transactions, directed edges represent "is waiting for"
2. Periodically, the system checks the graph for **cycles**
3. **A cycle = a deadlock exists**
4. The system selects a **victim** transaction (typically the one that has done the least work or has been aborted the fewest times) and **aborts** it to break the cycle

### Starvation

**Starvation** occurs when a transaction is repeatedly selected as a deadlock victim or kept waiting indefinitely, never getting to complete.

**Solutions:**

- Use **FIFO (First-In-First-Out) queues** for lock requests — older requests are served first
- **Boost the priority** of transactions that have been waiting a long time or have been aborted multiple times

---

## 5.6 Timestamp Ordering (TO)

**Timestamp Ordering** is an alternative to locking that orders conflicting operations based on **when transactions entered the system** rather than using locks.

### How It Works

- Every transaction Ti receives a **unique timestamp TS(Ti)** when it begins
- Every data item X stores two timestamp values:
    - `read_TS(X)` — the timestamp of the most recent transaction that successfully **read** X
    - `write_TS(X)` — the timestamp of the most recent transaction that successfully **wrote** X
- If a transaction tries to access an item but a **younger** transaction has already accessed it (item's TS > transaction's TS), the older transaction is **aborted and restarted** with a new, larger timestamp

### Advantages and Disadvantages

|Aspect|Detail|
|---|---|
|✅ **No deadlocks**|No locks means no circular waiting — deadlocks are impossible|
|✅ **Serializable**|Transactions are effectively ordered by timestamp|
|❌ **Cyclic restarts**|A transaction may be repeatedly aborted and restarted if it keeps conflicting with newer transactions (starvation risk)|
|❌ **No locking overhead**|But requires maintaining timestamps on every data item|

---

## 5.7 Multiversion Concurrency Control (MVCC)

**Multiversion Concurrency Control** takes a fundamentally different approach: instead of blocking reads or overwriting data, the system **keeps multiple historical versions** of each data item.

### How It Works

- When Transaction Ti **writes** item X, it creates a **new version** of X (rather than overwriting the existing value)
- When Transaction Tj **reads** item X, it is directed to the appropriate **historical version** that was current at Tj's timestamp
- This means **reads are never blocked or rejected** — they simply access an older version

### Multiversion 2PL with Certify Locks

An advanced variant that uses **three lock modes**:

|Lock Mode|Meaning|
|---|---|
|**Read lock**|Shared access for reading|
|**Write lock**|Creates a new version — concurrent reads of the old version still allowed|
|**Certify lock**|Required at commit time to officially replace the old version with the new one|

This allows reads and writes to happen **concurrently** on the same item (reader sees old version, writer creates new version), dramatically improving concurrency.

### Trade-offs

|Advantage|Disadvantage|
|---|---|
|✅ Read operations are never rejected or delayed|❌ Requires significantly more storage (RAM and disk) for multiple versions|
|✅ Very high concurrency for read-heavy workloads|❌ Requires a **garbage collection process** to periodically clean up obsolete old versions|

> 📌 **Note:** MVCC is used by PostgreSQL, Oracle, and MySQL (InnoDB) in production — it is the dominant concurrency approach in modern database systems.

---

## 5.8 Optimistic (Validation) Concurrency Control

Optimistic concurrency control is based on the assumption that **conflicts between transactions are rare**. Rather than blocking transactions preemptively, it lets them execute freely and only checks for conflicts when a transaction is ready to commit.

### Three Phases

|Phase|What Happens|Locking?|
|---|---|---|
|**Read Phase**|Transaction reads data items and makes changes to **local, private copies** (cached data in memory). No database is modified yet.|❌ None|
|**Validation Phase**|When ready to commit, the DBMS checks whether this transaction's reads and writes conflict with any other concurrently executing transactions.|❌ None|
|**Write Phase**|If validation **passes** → changes are permanently written to the database. If validation **fails** → transaction is aborted and restarted.|❌ None|

### When to Use Optimistic CC

- ✅ **Best for:** Read-heavy workloads where actual write conflicts are genuinely rare
- ❌ **Poor for:** Write-heavy workloads — high abort rates waste significant processing time when validation frequently fails

---

## 5.9 Multiple Granularity Locking

Real databases have a natural **hierarchy of data sizes** — from the entire database down to individual field values:

```
Database
  └── File (Table)
        └── Page (Block)
              └── Record (Row)
                    └── Field (Column value)
```

### The Granularity Trade-off

|Lock Granularity|Concurrency|Lock Management Overhead|Best For|
|---|---|---|---|
|**Coarse** (lock entire table)|Low|Low — few locks to manage|Batch operations on the whole table|
|**Fine** (lock individual records)|High|High — many locks to track|High-concurrency OLTP with many small transactions|

### Intention Locks

**Intention locks** allow the system to efficiently handle the lock hierarchy without checking every descendant node. They signal the system's _intention_ to lock something at a lower level:

|Intention Lock|Symbol|Meaning|
|---|---|---|
|**Intention-Shared**|IS|"I intend to place shared (read) locks on some descendant nodes"|
|**Intention-Exclusive**|IX|"I intend to place exclusive (write) locks on some descendant nodes"|
|**Shared-Intention-Exclusive**|SIX|"This node is shared-locked now AND I intend to exclusively lock some descendants"|

### Locking Rules for the Hierarchy

- **Top-down locking:** To lock a node, you must first acquire IS or IX locks on all its **ancestors** (from root downward)
- **Bottom-up unlocking:** When releasing locks, always release **children before parents**

> ⭐ **Exam Tip:** Intention locks exist to avoid the expensive process of checking all descendants. If a transaction holds IX on a table, the system knows some records are exclusively locked without scanning every record.


# Chapter 5 — Practice Questions

### Concurrency Control

#### 25 Multiple Choice Questions · Correct answers marked with ✅

---

**1.** The Concurrency-Control Manager (Scheduler) is primarily responsible for which ACID property?

- A) Atomicity — ensuring all operations succeed or none do
- B) Consistency — ensuring business rules are enforced
- C) **Isolation — ensuring interleaved transactions produce results identical to serial execution** ✅
- D) Durability — ensuring committed changes survive failures

> **Answer: C** — The Concurrency-Control Manager enforces Isolation. It ensures that even though transactions are physically interleaved on the CPU, their combined effect is as if they ran one at a time (serially). The Logging and Recovery Manager handles Durability.

---

**2.** What is the fundamental philosophical difference between Conservative and Optimistic concurrency control approaches?

- A) Conservative approaches use locks; optimistic approaches use timestamps only
- B) Conservative approaches work on single-user systems; optimistic approaches on multiuser systems
- C) **Conservative approaches assume conflicts WILL happen and delay transactions proactively; optimistic approaches assume conflicts are RARE and only check at commit time** ✅
- D) Conservative approaches are used for read-only transactions; optimistic for write transactions

> **Answer: C** — Conservative (pessimistic) approaches like locking prevent conflicts before they occur by blocking transactions. Optimistic approaches let transactions run freely and validate for conflicts only when they try to commit — rolling back if a conflict is found.

---

**3.** Multiple transactions can simultaneously hold a Shared (Read) lock on the same data item. However, if any transaction holds an Exclusive (Write) lock, what happens to all other lock requests on that item?

- A) Other transactions can still acquire Shared locks but not Exclusive locks
- B) Other transactions can read the item but cannot write to it
- C) Other transactions are immediately aborted to free the locked item
- D) **All other lock requests (both Shared and Exclusive) must wait until the Exclusive lock is released** ✅

> **Answer: D** — An Exclusive lock provides sole ownership. While it is held, NO other transaction can acquire ANY type of lock on that item — not even a shared (read) lock. This ensures the writer has completely uncontested access.

---

**4.** What does "upgrading" a lock mean in the context of concurrency control?

- A) Moving a lock from a child node to a parent node in the data hierarchy
- B) Increasing the priority of a lock request in the lock manager's queue
- C) **Converting a Shared (Read) lock to an Exclusive (Write) lock because the transaction now needs to write** ✅
- D) Transferring a lock from one transaction to another transaction

> **Answer: C** — Lock upgrade means a transaction that currently holds a Shared lock needs to modify the data and must convert its lock to an Exclusive lock. It must wait if any other transaction also holds a Shared lock on the same item.

---

**5.** The Two-Phase Locking (2PL) Protocol's core rule states:

- A) A transaction must acquire all needed locks before accessing any data item
- B) A transaction must release all locks immediately after each operation completes
- C) Locks must be held for exactly two time units before being released
- D) **All locking operations must precede the first unlock operation — once a lock is released, no new locks can be acquired** ✅

> **Answer: D** — The 2PL rule creates two strict phases: a Growing phase (acquire locks only, no releasing) and a Shrinking phase (release locks only, no acquiring). This ordering of locks and unlocks is what guarantees serializability.

---

**6.** During which phase of the Two-Phase Locking protocol does a transaction reach its "lock point"?

- A) At the very beginning of the Growing phase when the first lock is acquired
- B) At the very end of the Shrinking phase when the last lock is released
- C) **At the boundary between the Growing and Shrinking phases — when the maximum number of locks is held** ✅
- D) When the transaction issues its COMMIT statement

> **Answer: C** — The lock point is the moment when a transaction holds its maximum number of locks — right before it starts releasing any. It marks the transition from the Growing phase to the Shrinking phase.

---

**7.** Which variant of 2PL holds Exclusive locks until the transaction COMMITS or ABORTS, making recovery simpler?

- A) Basic 2PL
- B) Conservative 2PL
- C) Rigorous 2PL
- D) **Strict 2PL** ✅

> **Answer: D** — Strict 2PL holds all Exclusive (write) locks until the transaction commits or aborts. This prevents other transactions from reading dirty (uncommitted) data, which simplifies recovery and prevents cascading aborts.

---

**8.** A major DISADVANTAGE of the basic Two-Phase Locking protocol is:

- A) It does not guarantee serializability of transaction schedules
- B) It requires all data items to be locked before the transaction begins
- C) It only works with Shared locks and cannot handle Exclusive locks
- D) **It is subject to deadlocks and restricts concurrency by making transactions wait longer than necessary** ✅

> **Answer: D** — While 2PL guarantees serializability, it has two key drawbacks: (1) transactions may deadlock by circularly waiting for each other's locks, and (2) the growing-only / shrinking-only rule sometimes forces transactions to hold locks longer than strictly necessary, reducing concurrency.

---

**9.** A deadlock occurs when:

- A) A transaction holds too many locks and the lock table overflows
- B) A transaction takes too long to commit and the system times it out
- C) Two transactions try to write to the same data item at exactly the same millisecond
- D) **Two or more transactions are each waiting for a lock held by the other, creating a circular wait with no possibility of progress** ✅

> **Answer: D** — Deadlock is a circular dependency of lock waits. T1 holds lock on X and wants Y; T2 holds lock on Y and wants X. Neither can proceed — each is permanently blocked waiting for the other. This is an infinite cycle.

---

**10.** In the Wait-Die deadlock prevention policy, what happens when a YOUNGER transaction requests a lock held by an OLDER transaction?

- A) The younger transaction waits for the older one to release the lock
- B) The older transaction is aborted (wounded) to give the lock to the younger one
- C) Both transactions are aborted and restarted simultaneously
- D) **The younger transaction DIES (is aborted and restarted) — it cannot wait for an older transaction** ✅

> **Answer: D** — In Wait-Die: Older waits for Younger (non-preemptive). Younger DIES if it requests a lock held by Older. The older transaction always has priority and is never killed. Remember: "young die, old wait."

---

**11.** In the Wound-Wait deadlock prevention policy, what happens when an OLDER transaction requests a lock held by a YOUNGER transaction?

- A) The older transaction waits for the younger one to finish
- B) The older transaction is aborted since newer transactions have priority
- C) Both transactions abort and restart with new timestamps
- D) **The older transaction WOUNDS (aborts/preempts) the younger one to take the lock** ✅

> **Answer: D** — In Wound-Wait: Older WOUNDS Younger (preemptive — kills the younger to take its lock). Younger waits for Older. Contrasting with Wait-Die: in Wound-Wait, the older transaction is aggressive and kills the younger one rather than waiting.

---

**12.** When an aborted transaction is restarted after being selected as a deadlock victim, it is restarted with:

- A) A brand new, larger timestamp (newer priority)
- B) No timestamp — it goes to the end of the queue
- C) A timestamp equal to the current system time
- D) **Its ORIGINAL timestamp (to maintain its seniority and prevent starvation)** ✅

> **Answer: D** — Restarting with the original timestamp is critical to prevent starvation. If a transaction kept getting a newer timestamp every time it was aborted, it would always appear "young" and keep getting killed. The original timestamp preserves its seniority.

---

**13.** The Wait-for Graph (used for deadlock detection) detects a deadlock when:

- A) The graph has more nodes than edges
- B) A transaction node has no outgoing edges
- C) The graph becomes too large to fit in memory
- D) **The graph contains a CYCLE** ✅

> **Answer: D** — In the Wait-for Graph, nodes = transactions and directed edges = "is waiting for." A cycle means T1 waits for T2 which waits for T3 which waits for T1 — a circular dependency with no resolution. The system must abort one transaction (the victim) to break the cycle.

---

**14.** What is "starvation" in the context of concurrency control?

- A) The database runs out of disk space due to excessive transaction logging
- B) A transaction consumes all available memory, starving other transactions of resources
- C) **A transaction is repeatedly selected as a victim or kept waiting indefinitely, never getting to complete** ✅
- D) The lock manager runs out of entries in the lock table

> **Answer: C** — Starvation means a transaction never gets to commit because it keeps being aborted (chosen as deadlock victim) or perpetually blocked by other transactions. Solutions include FIFO lock queues and priority boosting for long-waiting transactions.

---

**15.** A key ADVANTAGE of Timestamp Ordering over Locking-based protocols is:

- A) Timestamp ordering provides stronger serializability guarantees than 2PL
- B) Timestamp ordering requires less memory than maintaining a lock table
- C) Timestamp ordering is faster for write-heavy workloads
- D) **Timestamp ordering is completely DEADLOCK-FREE because it uses no locks** ✅

> **Answer: D** — Since Timestamp Ordering does not use locks, circular lock-waiting (deadlock) is impossible. Conflicts are resolved by aborting and restarting the older transaction rather than making it wait. The trade-off is potential starvation from cyclic restarts.

---

**16.** In Timestamp Ordering, every data item X maintains two values: `read_TS(X)` and `write_TS(X)`. What do these values represent?

- A) The time when item X was first created and last deleted
- B) The time when item X was last backed up and restored
- C) The size and location of item X on the physical disk
- D) **The timestamps of the most recent transactions that successfully READ and WROTE item X, respectively** ✅

> **Answer: D** — `read_TS(X)` = timestamp of the youngest transaction that successfully read X. `write_TS(X)` = timestamp of the youngest transaction that successfully wrote X. These are used to determine if a transaction's access would violate the timestamp ordering.

---

**17.** In Multiversion Concurrency Control (MVCC), when a transaction writes to a data item X, what happens?

- A) X is immediately overwritten with the new value, and the old value is discarded
- B) X is locked exclusively and all readers must wait until the write commits
- C) X is copied to a backup file before being overwritten with the new value
- D) **A NEW VERSION of X is created; the old version remains accessible to transactions that need it** ✅

> **Answer: D** — MVCC is the defining feature of multiversion control: writes create new versions rather than overwriting. Concurrent readers are directed to the appropriate historical version based on their timestamp, so reads are never blocked by writes.

---

**18.** What is the PRIMARY advantage of Multiversion Concurrency Control over standard locking?

- A) MVCC uses no storage space because old versions are immediately garbage collected
- B) MVCC eliminates all forms of transaction conflicts including write-write conflicts
- C) MVCC guarantees that no transaction ever needs to be aborted
- D) **Read operations are NEVER blocked or rejected — they can always access an appropriate version of the data** ✅

> **Answer: D** — The greatest advantage of MVCC is that reads never wait. Even if a transaction is currently writing item X, readers access an older committed version of X without blocking. This dramatically improves concurrency for read-heavy workloads.

---

**19.** A significant DISADVANTAGE of Multiversion Concurrency Control is:

- A) It cannot handle write transactions — only reads are supported
- B) It requires all transactions to use the same timestamp
- C) It is incompatible with the SQL standard and cannot be used in standard DBMS
- D) **It requires significantly more storage for maintaining multiple versions AND needs a garbage collection process to remove obsolete versions** ✅

> **Answer: D** — MVCC's cost is storage overhead. Keeping multiple historical versions of every modified data item consumes substantial RAM and disk space. A background garbage collection process must periodically clean up old versions that are no longer needed by any active transaction.

---

**20.** In the Optimistic (Validation) Concurrency Control approach, during which phase does the transaction make changes to LOCAL COPIES of data rather than the actual database?

- A) Validation Phase
- B) Write Phase
- C) Commit Phase
- D) **Read Phase** ✅

> **Answer: D** — During the Read Phase, transactions read data from the database but make any modifications only to local, private copies (cache) in memory. The actual database is not changed until the Write Phase — and only if the Validation Phase passes successfully.

---

**21.** In Optimistic Concurrency Control, if the VALIDATION PHASE fails, what happens?

- A) The transaction's local changes are automatically merged with the database
- B) The conflicting transaction is notified and asked to retry after a delay
- C) The system rolls back all other transactions that were running concurrently
- D) **The current transaction is ABORTED and restarted from the beginning** ✅

> **Answer: D** — If validation detects that the transaction's local changes conflict with changes made by another concurrently executing transaction, the current transaction is completely aborted (its local copies are discarded) and restarted. Its work is lost.

---

**22.** Multiple Granularity Locking addresses what fundamental trade-off in concurrency control?

- A) The trade-off between using locks versus using timestamps
- B) The trade-off between deadlock prevention and deadlock detection
- C) The trade-off between conservative and optimistic approaches
- D) **The trade-off between COARSE-grained locks (low overhead, low concurrency) and FINE-grained locks (high overhead, high concurrency)** ✅

> **Answer: D** — Locking an entire table (coarse) is easy to manage but prevents all other transactions from accessing any part of it. Locking individual records (fine) allows high concurrency but requires tracking thousands of individual locks. Multiple Granularity Locking lets the system choose the appropriate level.

---

**23.** An Intention-Shared (IS) lock placed on a TABLE node means:

- A) The entire table is locked with a shared (read) lock
- B) The transaction intends to lock the entire table exclusively in the near future
- C) The table is currently being scanned but no locks have been acquired yet
- D) **The transaction intends to place Shared (read) locks on one or more RECORDS within this table** ✅

> **Answer: D** — IS (Intention-Shared) is placed on ancestor nodes (like a table or file) to signal that a shared lock will be requested somewhere further down the hierarchy (on a specific record or field). It warns other transactions without locking the entire table.

---

**24.** In Multiple Granularity Locking, which order must locks be acquired and released?

- A) Acquired bottom-up (records first, then table); released top-down (table first, then records)
- B) Both acquired and released in any order — there is no required sequence
- C) Acquired and released in the same order — always top-to-bottom
- D) **Acquired top-down (lock ancestors before descendants); released bottom-up (unlock children before parents)** ✅

> **Answer: D** — The hierarchy must be respected: to lock a record, you must first acquire IS or IX on its parent table (top-down). When unlocking, children (records) must be released before parents (tables) to avoid leaving orphaned intention locks (bottom-up).

---

**25.** Which of the following correctly describes the SIX (Shared-Intention-Exclusive) lock?

- A) A lock that is simultaneously shared by six different transactions
- B) A sequential lock mode where shared access is granted for exactly six seconds
- C) A lock that grants exclusive access to six specific records in a table
- D) **The current node is locked with a Shared lock AND the transaction intends to place Exclusive locks on some of its descendants** ✅

> **Answer: D** — SIX = Shared (current node is read-locked for full scan) + Intention-Exclusive (some specific descendant records will be exclusively locked for writing). It combines a table-level read with record-level writes in the most efficient way.

---

_End of Chapter 5 Practice Questions_