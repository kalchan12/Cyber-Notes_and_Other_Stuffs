
## 7.1 Database Recovery — Purpose and Failure Types

### What is Database Recovery?

No matter how well-designed a system is, failures are inevitable. **Database Recovery** is the process of restoring the database to its last known **consistent, correct state** after a failure has occurred. Recovery is the practical implementation of two ACID properties:

- **Atomicity** — uncommitted transactions must be completely undone (rolled back), as if they never happened
- **Durability** — committed transactions must survive any failure; their changes must be permanently preserved

Without a recovery mechanism, a single power outage or software crash could leave the database in a partially updated, corrupted, and permanently inconsistent state.

---

### Types of Database Failures

|Failure Type|Cause|Examples|
|---|---|---|
|**Transaction Failure**|Logical errors within a transaction or scheduling decisions|Incorrect input data, division by zero, deadlock resolution (one transaction deliberately aborted)|
|**System Failure**|Loss of main memory (RAM) contents — disk data is unaffected|OS crash, power outage causing memory loss, addressing errors, application faults|
|**Media Failure**|Physical damage to persistent storage — disk contents may be lost|Disk head crash, corrupted storage sectors, catastrophic power surge|

### Recovery Strategies Based on Failure Type

|Strategy|When Used|How It Works|
|---|---|---|
|**Catastrophic Recovery**|After extensive damage (media failure)|Restore the entire database from a previous **backup copy**, then **replay the transaction log** to reconstruct all operations that occurred since the backup|
|**Non-Catastrophic Recovery**|When database is physically intact but logically inconsistent|Use **Undo** (roll back uncommitted changes) and **Redo** (re-apply committed changes) operations directly from the log|

> ⭐ **Exam Tip:** Non-catastrophic recovery is far more common than catastrophic. The system log is the key tool for both types. For catastrophic recovery, both a backup AND a log are required.

---

## 7.2 Transaction Architecture and The Log

### The Transaction Processor Components

The Transaction Processor consists of multiple coordinated components that together ensure data integrity:

|Component|Responsibility|
|---|---|
|**Log Manager**|Writes log entries into memory buffers and coordinates with the Buffer Manager to flush them to stable disk storage|
|**Recovery Manager**|After a crash, examines the log to determine which transactions need to be redone or undone, then restores the database accordingly|
|**Cache Manager**|Manages the database cache (buffer pool) in RAM — holds recently accessed and modified data pages|
|**Buffer Manager**|Controls when and how memory buffers are flushed to disk|

### The Transaction Log

The **Transaction Log** is the single most important tool in database recovery. It is a sequential, append-only file stored on stable disk storage that records every significant change made to the database.

Every log entry records:

- The transaction identifier (which transaction made the change)
- The data item affected
- The **BFIM (Before Image)** — the value of the data item BEFORE modification
- The **AFIM (After Image)** — the new value of the data item AFTER modification
- The type of operation (write, commit, abort, checkpoint)

|Term|Full Name|Meaning|
|---|---|---|
|**BFIM**|Before Image|The old value of a data item before a transaction modified it — used for UNDO operations|
|**AFIM**|After Image|The new value of a data item after a transaction modified it — used for REDO operations|

> 📌 **Note:** The log must be stored on **stable storage** (separate from the main database) — otherwise a disk failure would destroy both the database AND the log needed to recover it.

---

## 7.3 Data Caching and Buffer Flushing Policies

Before a transaction's changes are written permanently to disk, they reside temporarily in the **database cache** (a buffer pool in RAM). The system must decide exactly when these buffers are flushed to disk. This decision directly determines what recovery operations are needed after a failure.

Two independent binary decisions create four possible combinations:

### Decision 1: STEAL vs. NO-STEAL

|Policy|Definition|Advantage|Recovery Implication|
|---|---|---|---|
|**STEAL**|A dirty (modified but uncommitted) buffer page CAN be written to disk before the transaction commits|Frees buffer space — avoids needing enormous amounts of RAM|If the transaction later aborts, its changes are already on disk and must be **UNDONE**|
|**NO-STEAL**|Dirty pages are NOT written to disk until the transaction commits|Simpler recovery — uncommitted changes never reach disk|More RAM required to hold all dirty pages until commit|

### Decision 2: FORCE vs. NO-FORCE

|Policy|Definition|Advantage|Recovery Implication|
|---|---|---|---|
|**FORCE**|ALL modified pages are immediately written to disk when a transaction commits|Durability guaranteed immediately at commit|Slower commits — high I/O cost if the same page is updated by multiple transactions|
|**NO-FORCE**|Modified pages are flushed to disk only when necessary — possibly after the commit|Better performance — multiple updates to the same page batched into one disk write|If system crashes after commit but before flush, committed changes must be **REDONE** from the log|

### The Four Recovery Combinations

|Combination|UNDO Needed?|REDO Needed?|Notes|
|---|---|---|---|
|**STEAL / No-Force**|✅ Yes|✅ Yes|Most flexible — used by ARIES|
|**STEAL / Force**|✅ Yes|❌ No|Uncommitted changes may be on disk; committed ones guaranteed|
|**No-Steal / No-Force**|❌ No|✅ Yes|Uncommitted changes never reach disk; committed ones may not be flushed yet|
|**No-Steal / Force**|❌ No|❌ No|Simplest recovery but worst performance|

> ⭐ **Exam Tip:** **STEAL/No-Force is the standard approach** used by ARIES and most production databases. It offers the best performance trade-off but requires both UNDO and REDO during recovery.

---

## 7.4 Write-Ahead Logging (WAL) and Checkpointing

### Write-Ahead Logging (WAL)

**WAL** is a strict protocol that governs the ordering of log writes relative to database writes. It is mandatory when using in-place updates (where the database page is directly overwritten).

The WAL protocol has two rules:

|Rule|For What|Requirement|
|---|---|---|
|**WAL Rule 1 (for UNDO)**|Undo capability|The **BFIM** (before image) must be written to the log on stable disk **BEFORE** the AFIM overwrites the data page on the database disk|
|**WAL Rule 2 (for REDO)**|Redo capability|All **AFIMs** (after images) must be written to the log **BEFORE** the transaction is allowed to commit|

**Why WAL is critical:** If the system crashes during a write, WAL ensures the log always has enough information to either undo or redo the change. Without WAL, a crash could corrupt both the data and leave the log without enough information to recover.

### Checkpointing

A **checkpoint** is a synchronization point where the database's memory state is deliberately aligned with its disk state. Checkpoints dramatically reduce the amount of log work needed during recovery — without them, recovery would need to process the entire log from the very beginning.

#### Steps in the Checkpointing Process

```
1. SUSPEND all new transaction executions temporarily
        ↓
2. FORCE WRITE all modified (dirty) buffer pages to the database disk
        ↓
3. WRITE a [checkpoint] record to the log
        ↓
4. RESUME normal transaction execution
```

#### How Checkpoints Aid Recovery

When a crash occurs and recovery begins, the system only needs to look back to the most recent checkpoint:

- **REDO** all transactions that had committed **since the last checkpoint** (their changes may not have been flushed to disk yet)
- **UNDO** all transactions that were **active (in-progress) at the time of the crash** (they never committed)

> 📌 **Note:** Everything before the last checkpoint is already safely on disk — no recovery work is needed for those transactions. This makes checkpoints extremely valuable for limiting recovery time.

---

## 7.5 Transaction Rollback (Undo) and Roll-Forward (Redo)

|Operation|Also Called|What It Does|When Used|
|---|---|---|---|
|**UNDO**|Rollback|Reads **BFIMs** from the log and restores the old values to disk, completely erasing the effects of the transaction|When a transaction fails or is aborted before committing|
|**REDO**|Roll-forward|Reads **AFIMs** from the log and re-applies them to disk|When a transaction committed but its changes weren't fully flushed to disk before a crash|

### Cascading Rollback

When Transaction T is rolled back (undone), any other transaction that **read data written by T** must also be rolled back. This chain reaction is called a **cascading rollback**.

```
T1 writes X → T2 reads X (dirty read) → T1 aborts
→ T2 must also abort because it used T1's uncommitted data
→ If T3 read data from T2, T3 must also abort
→ ... and so on (cascade)
```

> ⭐ **Exam Tip:** Cascading rollbacks are the reason **Cascadeless Schedules** (from Chapter 4) are preferred — they prevent this chain reaction by ensuring transactions only read committed data.

---

## 7.6 Data Update and Recovery Schemes

Three major approaches define how updates are applied and how recovery is performed:

### Scheme 1: Deferred Update (No-Undo / Redo)

**Core Idea:** Updates are recorded in a **local workspace and the log ONLY** — they are NOT written to the actual database disk until the transaction successfully commits.

```
Transaction executes → All changes written to LOG + local cache
                     → Changes NOT written to database disk yet
If commit: → NOW write AFIMs to database disk (Redo if crash occurs mid-write)
If abort:  → Simply discard the log entries and local cache (nothing to undo on disk)
```

|Aspect|Detail|
|---|---|
|UNDO needed?|❌ No — uncommitted changes never reach the disk|
|REDO needed?|✅ Yes — after a crash post-commit, must redo from log|
|Idempotent|Yes — redoing an operation multiple times gives the same result as doing it once|
|Drawback|Transactions must hold their entire change set in memory until commit — memory intensive|

### Scheme 2: Immediate Update (Undo / No-Redo)

**Core Idea:** Changes are written to the database disk **immediately** as they occur (under WAL protocol), even before the transaction commits.

```
Transaction executes → AFIMs written to database disk immediately (WAL ensures BFIM logged first)
If commit: → Changes already on disk — done (no redo needed)
If abort/crash: → Must UNDO by reading BFIMs from log and restoring old values to disk
```

|Aspect|Detail|
|---|---|
|UNDO needed?|✅ Yes — uncommitted changes may already be on disk|
|REDO needed?|❌ No — committed changes are already on disk by definition|
|Advantage|Recovery is simpler when it comes to committed transactions|
|Drawback|Aborts require undoing changes that were already written to disk|

### Scheme 3: Shadow Paging

**Core Idea:** Maintains **two separate page directories** — a Current directory and a Shadow directory. The database is organized into fixed-size pages.

```
Transaction starts → Current directory = exact copy of Shadow directory
During transaction → Current directory records updates to NEW disk locations
                  → Shadow directory remains completely UNCHANGED
                  → Old data is never overwritten — writes go to new locations
On COMMIT → Current directory becomes the new Shadow directory (atomic pointer swap)
On ABORT  → Simply discard the Current directory → Shadow directory remains valid
```

|Aspect|Detail|
|---|---|
|Log needed?|❌ No log required for recovery|
|UNDO needed?|❌ No — just discard the current directory|
|REDO needed?|❌ No — commit is an atomic pointer swap|
|Drawback|Fragments the database over time (data no longer in contiguous pages); garbage collection of old pages is needed|

### Scheme Comparison Summary

|Scheme|UNDO|REDO|Log Required|Disk Writes Before Commit|
|---|---|---|---|---|
|Deferred Update|❌|✅|✅ Yes|❌ No|
|Immediate Update|✅|❌|✅ Yes|✅ Yes|
|Shadow Paging|❌|❌|❌ No|❌ No (new locations)|

---

## 7.7 The ARIES Recovery Algorithm

**ARIES (Algorithm for Recovery and Isolation Exploiting Semantics)** is the gold-standard, industrial-strength recovery algorithm used by virtually all major modern DBMS systems (including IBM DB2, SQL Server, and PostgreSQL-based systems). It is designed to work with the **Steal / No-Force** buffer policy.

### Three Core Principles of ARIES

|Principle|Description|
|---|---|
|**1. Write-Ahead Logging (WAL)**|All log records must be saved to stable disk before the corresponding database pages are modified — exactly as defined in §7.4|
|**2. Repeating History During Redo**|During recovery, ARIES replays ALL operations (even those of transactions that ultimately aborted) to reconstruct the exact database state at the moment of the crash — then it undoes the aborted ones|
|**3. Logging Changes During Undo**|Every UNDO operation is itself logged (as a Compensation Log Record / CLR). This prevents ARIES from repeating already-completed undo work if another crash occurs during the recovery process itself|

> ⭐ **Exam Tip:** Principle 2 ("Repeating History") is what makes ARIES unique and powerful — most students find it counterintuitive. ARIES first replays EVERYTHING (including aborted transactions' changes), then precisely undoes only what needs to be undone. This allows very accurate, fine-grained recovery.

### Key ARIES Data Structures

|Data Structure|Purpose|
|---|---|
|**LSN (Log Sequence Number)**|A strictly increasing unique identifier assigned to every log record. Every database page also stores the LSN of its most recent update — used to determine what has/hasn't been written to disk|
|**Transaction Table**|Tracks all currently active transactions: their transaction ID, current status, and the LSN of their most recent log record|
|**Dirty Page Table**|Tracks all modified (dirty) pages currently in the buffer pool and the LSN of the earliest update to each page — determines where REDO must start from|

### The Three Phases of ARIES Recovery

```
CRASH OCCURS
      ↓
┌─────────────────────────────────────────┐
│  PHASE 1: ANALYSIS                      │
│  Start: Last checkpoint in the log      │
│  • Read Transaction Table and           │
│    Dirty Page Table from checkpoint     │
│  • Scan forward to end of log           │
│  • Identify: which transactions were    │
│    active at crash (need UNDO)          │
│  • Identify: which pages are dirty      │
│    (may need REDO)                      │
└─────────────┬───────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│  PHASE 2: REDO                          │
│  Start: Earliest LSN in Dirty Page Table│
│  • Scan FORWARD through the log         │
│  • Re-apply ALL changes (even aborted   │
│    transactions) that didn't make it    │
│    to disk → reconstruct crash state    │
└─────────────┬───────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│  PHASE 3: UNDO                          │
│  Start: End of the log                  │
│  • Scan BACKWARD through the log        │
│  • UNDO all changes of transactions     │
│    that were ACTIVE (uncommitted) at    │
│    the time of the crash                │
│  • Log each undo as a CLR               │
└─────────────────────────────────────────┘
      ↓
DATABASE RESTORED TO CONSISTENT STATE
```

|Phase|Direction|Starting Point|What It Does|
|---|---|---|---|
|**Analysis**|Forward (→)|Last checkpoint|Determines exactly what needs to be Redone and Undone|
|**Redo**|Forward (→)|Earliest dirty page LSN|Replays ALL history to reconstruct the exact crash state|
|**Undo**|Backward (←)|End of log|Reverses the effects of all uncommitted transactions|
# Chapter 7 — Practice Questions

### Database Recovery Techniques

#### 25 Multiple Choice Questions · Correct answers marked with ✅

---

**1.** What are the TWO ACID properties that database recovery is specifically designed to enforce?

- A) Consistency and Isolation
- B) Isolation and Durability
- C) Consistency and Durability
- D) **Atomicity and Durability** ✅

> **Answer: D** — Recovery enforces Atomicity (uncommitted transactions must be completely undone — "all or nothing") and Durability (committed transactions must survive any failure — their changes are permanent). Isolation is handled by concurrency control, and Consistency by the application programmer.

---

**2.** Which type of failure involves physical damage to persistent storage such as a disk head crash?

- A) Transaction Failure
- B) System Failure
- C) Logical Failure
- D) **Media Failure** ✅

> **Answer: D** — Media Failure involves physical damage to the storage medium itself (disk head crash, corrupted sectors, power surge that destroys the disk). System Failure causes loss of RAM content but leaves disk data intact. Transaction Failure is a logical/programming issue within a single transaction.

---

**3.** After a Media Failure (catastrophic damage), what TWO things are required to recover the database?

- A) A transaction log and a fresh empty database
- B) The Dirty Page Table and the Transaction Table
- C) A WAL record and a checkpoint
- D) **A backup copy of the database AND the transaction log** ✅

> **Answer: D** — Catastrophic recovery requires restoring the database from a prior backup, then replaying the transaction log to reconstruct all operations that occurred after the backup was taken. Without both, full recovery is impossible.

---

**4.** What does BFIM stand for, and when is it used during recovery?

- A) Buffer Frame In Memory — used to identify which pages are currently cached
- B) Basic Format for Internal Modification — the standard log entry format
- C) **Before Image — the old value of a data item BEFORE modification, used for UNDO operations** ✅
- D) Binary File Index Marker — used to locate records on disk

> **Answer: C** — BFIM (Before Image) is the data value that existed before a transaction modified it. During UNDO (rollback), the recovery manager reads BFIMs from the log and restores these old values to the database, reversing the transaction's effects.

---

**5.** What does AFIM stand for, and when is it used during recovery?

- A) After-File Index Map — locates data after a file is reorganized
- B) **After Image — the new value of a data item AFTER modification, used for REDO operations** ✅
- C) Atomic File Integrity Marker — confirms a file was saved atomically
- D) Archived File in Memory — refers to pages moved from cache to disk

> **Answer: B** — AFIM (After Image) is the data value after a transaction modified it. During REDO (roll-forward), the recovery manager reads AFIMs from the log and re-applies them to the database, re-creating the effects of a committed transaction that didn't fully reach disk.

---

**6.** The STEAL buffer policy means:

- A) The system can lock and exclusively own a data page preventing other access
- B) A transaction can take (steal) locks from lower-priority transactions
- C) **A dirty (uncommitted) buffer page CAN be written to disk before the transaction commits** ✅
- D) The system copies data from the database to a shadow directory

> **Answer: C** — STEAL allows the buffer manager to flush modified pages to disk even if the modifying transaction hasn't committed yet. This frees buffer space but means that if the transaction later aborts, its changes on disk must be UNDONE.

---

**7.** Which buffer policy combination requires BOTH Undo AND Redo during recovery and is used by the ARIES algorithm?

- A) No-Steal / Force
- B) No-Steal / No-Force
- C) Steal / Force
- D) **Steal / No-Force** ✅

> **Answer: D** — Steal/No-Force is the most flexible and common production approach (used by ARIES). STEAL means uncommitted pages may be on disk (requiring UNDO if the transaction aborts). NO-FORCE means committed pages may not yet be on disk (requiring REDO after a crash).

---

**8.** A FORCE buffer policy means that when a transaction commits:

- A) All lock acquisitions are forced to complete before any releases
- B) The transaction is forced to wait until all other active transactions also commit
- C) **ALL modified pages are immediately written to disk at commit time** ✅
- D) The log is forced to be deleted after the commit is recorded

> **Answer: C** — FORCE ensures all dirty pages are immediately flushed to disk when a transaction commits, guaranteeing durability right away. The disadvantage is high I/O cost — if the same page is updated many times, each commit forces a disk write.

---

**9.** Which buffer combination requires NO Undo and NO Redo, making recovery the simplest — but at the cost of the worst performance?

- A) Steal / No-Force
- B) Steal / Force
- C) No-Steal / No-Force
- D) **No-Steal / Force** ✅

> **Answer: D** — No-Steal/Force is the simplest for recovery: uncommitted pages never reach disk (no UNDO needed), and all committed pages are immediately forced to disk (no REDO needed). But forcing every commit to disk AND never stealing buffers is extremely expensive in both I/O and memory.

---

**10.** The Write-Ahead Logging (WAL) protocol's Rule 1 (for UNDO) requires:

- A) All AFIMs must be logged before the transaction commits
- B) The log must be written to disk after every transaction commits
- C) Checkpoints must be taken before any write operation begins
- D) **The BFIM must be written to the log on stable disk BEFORE the AFIM overwrites the data page on the database disk** ✅

> **Answer: D** — WAL Rule 1 ensures UNDO capability. If the system crashes while writing the AFIM to the database, the BFIM is already safely in the log — the recovery manager can use it to reverse the partial change. Without this rule, a crash could destroy both the old and new values.

---

**11.** The Write-Ahead Logging (WAL) protocol's Rule 2 (for REDO) requires:

- A) The BFIM must be logged before any database page is modified
- B) Checkpoints must be completed before a transaction can commit
- C) Exclusive locks must be released before the commit record is written
- D) **All AFIMs must be written to the log BEFORE the transaction is allowed to execute its COMMIT** ✅

> **Answer: D** — WAL Rule 2 ensures REDO capability. If the system crashes after commit but before the AFIMs are flushed from cache to the database disk, the log already has all the AFIMs. The recovery manager can re-apply them to reconstruct the committed state.

---

**12.** The PRIMARY purpose of database Checkpointing is to:

- A) Create a full backup copy of the database to separate storage
- B) Test the recovery procedures to ensure they work correctly
- C) Transfer all active transaction locks to a safe holding area
- D) **Synchronize the database and log by flushing buffers to disk, limiting how far back recovery must search the log** ✅

> **Answer: D** — Checkpoints minimize recovery time by creating a known synchronization point. After a crash, recovery only needs to process log entries from the last checkpoint forward — everything before it is already safely on disk.

---

**13.** During recovery after a crash, which transactions need to be REDONE?

- A) All transactions that were active (in-progress) at the time of the crash
- B) All transactions in the entire log, starting from the very beginning
- C) Only transactions that explicitly called the REDO procedure before the crash
- D) **Transactions that had COMMITTED since the last checkpoint but whose changes may not have been fully flushed to disk** ✅

> **Answer: D** — REDO is needed for committed transactions that used a No-Force policy — their changes are in the log but may not have reached the database disk before the crash. Transactions active at crash time need UNDO, not REDO.

---

**14.** What is a Cascading Rollback?

- A) A checkpoint that triggers all other checkpoints in the system to update simultaneously
- B) A recovery technique where the log is rolled back section by section
- C) When a transaction failure causes the entire database to be restored from backup
- D) **When Transaction T is rolled back, all other transactions that READ T's uncommitted data must also be rolled back in a chain reaction** ✅

> **Answer: D** — Cascading Rollback is a chain reaction of aborts. If T1 writes X (dirty), T2 reads X, T3 reads from T2, and T1 aborts — T2 and T3 must also abort because they built on T1's uncommitted (invalid) data. This is why Cascadeless Schedules are preferred.

---

**15.** In the Deferred Update recovery scheme, when are changes written to the actual DATABASE DISK?

- A) Immediately as each operation executes, under WAL protocol
- B) When the buffer becomes full and must be flushed
- C) When a checkpoint is triggered by the system
- D) **Only AFTER the transaction successfully commits** ✅

> **Answer: D** — Deferred Update (No-Undo/Redo) postpones all writes to the database disk until the commit point. Changes are recorded in the transaction's local workspace and the log, but the actual database pages are not modified until commit is guaranteed.

---

**16.** Why does the Deferred Update scheme NOT require UNDO operations?

- A) Because it keeps multiple versions of every data item
- B) Because it uses a shadow page directory instead of a log
- C) Because it forces all pages to disk immediately at commit
- D) **Because uncommitted changes are NEVER written to the database disk — if a transaction fails, there is nothing on disk to reverse** ✅

> **Answer: D** — Since Deferred Update never writes to the database disk before commit, an abort simply means discarding the log entries and local cache. The database disk was never touched, so no undo is needed.

---

**17.** The Deferred Update scheme is said to be "Idempotent." What does this mean?

- A) The recovery can only be performed once — a second recovery would corrupt the database
- B) The scheme produces identical results whether used on a single-user or multiuser system
- C) Updates can be deferred indefinitely without any impact on consistency
- D) **Redoing an operation multiple times produces the exact same result as doing it once — repeated REDO is safe** ✅

> **Answer: D** — Idempotency means: REDO(REDO(X)) = REDO(X). This is critical because during recovery, the system might need to redo an operation that was actually already applied to disk before the crash. Being idempotent means this is harmless — the result is the same.

---

**18.** In the Immediate Update recovery scheme, changes are written to the database disk BEFORE the transaction commits. What does this mean for recovery?

- A) No UNDO is needed because changes are always written before commit
- B) No REDO is needed because changes might not have reached disk before the crash
- C) Both UNDO and REDO are never needed because the WAL log covers all cases
- D) **UNDO IS required (uncommitted changes may be on disk) but REDO is NOT (committed changes are guaranteed to be on disk already)** ✅

> **Answer: D** — Immediate Update (Undo/No-Redo): since changes are forced to disk under WAL before commit, committed transactions are already on disk (no REDO). But since uncommitted changes may also be on disk (written before commit), aborted transactions need UNDO.

---

**19.** In Shadow Paging, what happens to the SHADOW DIRECTORY when a transaction commits?

- A) The shadow directory is deleted and replaced with an empty new one
- B) The shadow directory is backed up to a separate archive storage
- C) The shadow directory's pages are merged with the current directory's pages
- D) **The CURRENT directory (with the new updates) atomically becomes the new shadow directory** ✅

> **Answer: D** — On commit, the current directory pointer is atomically switched to replace the shadow directory. This single pointer change makes the transaction's changes permanent. The old shadow directory (with pre-transaction data) becomes obsolete.

---

**20.** What is a major DRAWBACK of the Shadow Paging scheme compared to logging-based approaches?

- A) Shadow paging requires both UNDO and REDO, making recovery slower
- B) Shadow paging cannot recover from media failures
- C) Shadow paging requires a transaction log to be maintained at all times
- D) **Shadow paging fragments the database over time because updates are written to new disk locations, and old pages must be garbage collected** ✅

> **Answer: D** — Shadow paging never overwrites old pages — updates always go to new disk locations. Over time, this fragments the database (data is no longer contiguous), degrading I/O performance. Periodic garbage collection of obsolete shadow pages is required.

---

**21.** ARIES uses the Steal/No-Force buffer policy. What does this combination specifically mean for ARIES's recovery process?

- A) ARIES only needs to REDO operations — no UNDO is ever needed
- B) ARIES only needs to UNDO operations — no REDO is ever needed
- C) ARIES requires neither UNDO nor REDO — shadow paging handles both
- D) **ARIES requires BOTH UNDO (because uncommitted pages may be on disk via STEAL) AND REDO (because committed pages may not be on disk via NO-FORCE)** ✅

> **Answer: D** — STEAL requires UNDO (dirty pages may have been flushed to disk before commit). NO-FORCE requires REDO (committed pages may not have been flushed to disk yet). This combination offers maximum flexibility but needs both recovery operations.

---

**22.** What is a Log Sequence Number (LSN) in ARIES, and what unique property must it have?

- A) A random ID assigned to identify which user initiated each transaction
- B) A timestamp showing the exact calendar date and time a log entry was created
- C) A hash value used to verify the integrity of each log record
- D) **A strictly INCREASING unique identifier assigned to every log record; each data page also stores the LSN of its most recent update** ✅

> **Answer: D** — The LSN is the core ordering mechanism in ARIES. Its strictly increasing property ensures every log record has a unique, ordered identifier. The LSN stored in each database page tells the recovery manager exactly what the last update applied to that page was, enabling precise Redo decisions.

---

**23.** What is the purpose of the DIRTY PAGE TABLE in ARIES?

- A) Tracks which users have modified which tables during the current session
- B) Lists all transactions that have been aborted and need to be undone
- C) Records the checkpoint history showing when each flush was performed
- D) **Tracks all modified (dirty) pages currently in the buffer pool and the LSN of the earliest update to each — used to determine where REDO must start** ✅

> **Answer: D** — The Dirty Page Table is essential for the Redo Phase. The earliest LSN across all dirty pages tells ARIES exactly where to start the Redo Phase — earlier log records don't need to be checked for Redo because those pages are already safely on disk.

---

**24.** In which ORDER do the three ARIES recovery phases execute, and what direction does each scan the log?

- A) Undo (backward) → Redo (forward) → Analysis (forward)
- B) Redo (forward) → Analysis (forward) → Undo (backward)
- C) Analysis (backward) → Redo (backward) → Undo (forward)
- D) **Analysis (forward from checkpoint) → Redo (forward from earliest dirty LSN) → Undo (backward from end of log)** ✅

> **Answer: D** — ARIES always follows this exact sequence: (1) Analysis scans forward from the last checkpoint to identify what needs REDO and UNDO. (2) Redo scans forward from the earliest dirty page LSN, replaying all history. (3) Undo scans backward from the end of log, reversing uncommitted transactions.

---

**25.** ARIES's second principle — "Repeating History During Redo" — states that ARIES replays ALL operations during the Redo phase, including operations from transactions that ultimately ABORTED. Why is this approach taken?

- A) It is a mistake — ARIES should skip aborted transactions during Redo to save time
- B) Because all transactions in ARIES are considered committed once they begin
- C) To satisfy the WAL protocol requirement that all log entries must be replayed
- D) **To precisely reconstruct the exact database state at the moment of the crash BEFORE selectively undoing only the uncommitted transactions in the Undo phase** ✅

> **Answer: D** — This counterintuitive principle is what makes ARIES so accurate. By first replaying everything (including aborted transactions) to reconstruct the crash state exactly, ARIES can then precisely undo only what needs to be undone. This allows very fine-grained, correct recovery — even for complex interleaved scenarios.

---

