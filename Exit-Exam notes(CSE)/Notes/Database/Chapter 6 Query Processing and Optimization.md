
## 6.1 What is Query Processing?

SQL is a **declarative language** — when you write a query, you specify **what data you want**, not **how to retrieve it**. This is a powerful advantage for the programmer, but it means the DBMS must figure out the best retrieval strategy on its own.

**Query Processing** is the complete pipeline of steps the DBMS takes to transform a high-level SQL query into an actual execution strategy and return results.

**Query Optimization** is the specific step within that pipeline where the DBMS selects the single most efficient execution plan from potentially hundreds of mathematically equivalent alternatives.

### Why Optimization Matters: A Dramatic Example

The notes present a query finding "Managers who work in a London branch":

|Approach|Strategy|I/O Operations Required|Efficiency|
|---|---|---|---|
|**Query 1 (Bad)**|Cartesian Product first, then Select|101,050|Baseline|
|**Query 2 (Better)**|Join first, then Select|3,050|~33× faster|
|**Query 3 (Best)**|Select first (reduce data), then Join|1,160|~87× faster|

This example powerfully illustrates that **the order in which operations are applied** makes an enormous difference — 87 times the difference in this case. The optimizer's job is to find Query 3's approach automatically.

### Goals of Query Optimization

The optimizer aims to minimize resource consumption:

|Resource|What It Means|
|---|---|
|**Disk I/O**|The most expensive operation — reads and writes to/from physical storage|
|**CPU Time**|Computation time for sorting, hashing, comparing values in memory|
|**Memory**|RAM required to hold intermediate query results|
|**Network**|Data transfer cost in distributed database environments|

> ⭐ **Exam Tip:** Disk I/O (secondary storage access) is consistently the **most important and most costly** factor in query optimization. Minimizing disk reads/writes is always the primary objective.

---

## 6.2 Steps in Query Processing

When an SQL query is submitted to the DBMS, it passes through a sequential pipeline of steps:

```
SQL Query Submitted
        ↓
   1. SCANNING
   Tokenizes the query — identifies keywords (SELECT, FROM, WHERE),
   attribute names, and relation names
        ↓
   2. PARSING
   Checks the query's syntax and grammar rules
   (e.g., are parentheses matched? Is the SQL syntax correct?)
        ↓
   3. VALIDATION
   Checks semantic correctness — do the table names and column names
   actually exist in the database schema?
        ↓
   4. TRANSLATION
   Converts the validated SQL query into an initial
   Relational Algebra expression
        ↓
   5. QUERY OPTIMIZER
   Analyzes the relational algebra expression, estimates
   execution costs of multiple equivalent plans, and selects
   the best Query Execution Plan (QEP)
        ↓
   6. CODE GENERATION & EXECUTION
   Generates executable code for the QEP and runs it
   against the actual data, returning results (or an error)
```

|Step|What Can Go Wrong / What It Catches|
|---|---|
|Scanning|Unrecognized tokens, invalid characters|
|Parsing|Syntax errors — missing commas, unclosed parentheses|
|Validation|Semantic errors — referencing a column that doesn't exist|
|Translation|Multiple equivalent RA expressions are generated|
|Optimization|Wrong plan chosen = dramatically poor performance|

---

## 6.3 Relational Algebra — The Language of Optimization

The query optimizer internally represents and manipulates queries using **Relational Algebra** — a formal mathematical language for data operations. Understanding these operations is essential because the optimizer's transformations are all expressed in relational algebra.

### Unary Operations (Operate on a Single Table)

#### SELECT — σ (sigma)

Filters **rows** — returns only the tuples that satisfy a given condition.

```
σ (condition) (Relation)
Example: σ (salary > 50000) (Employee)
→ Returns all employees with salary greater than 50,000
```

- Uses Boolean logic: AND (∧), OR (∨), NOT (¬)
- Does NOT reduce the number of columns — only filters rows

#### PROJECT — π (pi)

Filters **columns** — returns only the specified attributes and discards the rest.

```
π (attribute_list) (Relation)
Example: π (name, salary) (Employee)
→ Returns only the name and salary columns of all employees
```

- Automatically **eliminates duplicate rows** (equivalent to SQL's `SELECT DISTINCT`)
- Does NOT filter rows — only selects which columns to keep

### Set Theory Operations (Require Union-Compatible Tables)

> For Union, Intersection, and Set Difference, both relations must be **union-compatible**: same number of columns AND corresponding columns have the same domains.

|Operation|Symbol|Description|
|---|---|---|
|**Union**|∪|Combines all tuples from both tables; **duplicates removed**|
|**Intersection**|∩|Returns only tuples that appear in **both** tables|
|**Set Difference**|−|Returns tuples in the **first** table that are **NOT** in the second|

### Binary Operations (Operate on Two Tables)

#### Cartesian Product — ×

Combines **every tuple from Table A with every tuple from Table B**, regardless of any meaningful relationship.

- If Table A has m rows and Table B has n rows → result has **m × n rows**
- Very expensive — almost always leads to huge intermediate results
- Rarely useful on its own; typically followed by a Select to filter meaningful combinations

#### JOIN — ⋈

Combines related tuples from two tables based on a **matching condition**. Essentially a Cartesian Product followed by a Select — but implemented far more efficiently.

```
Employee ⋈ (Employee.DeptID = Department.DeptID) Department
→ Returns only pairs of employees and departments that are actually related
```

- **Theta Join (θ-join):** Uses any comparison operator (=, <, >, ≤, ≥, ≠)
- **Equijoin:** A Theta Join using only equality (=) — most common type
- **Natural Join:** An Equijoin that automatically matches columns with the same name and removes duplicate columns

---

## 6.4 Why Query Optimization Matters — The I/O Example Revisited

The three-query comparison from §6.1 can be explained in relational algebra terms:

|Query|Relational Algebra Order|Problem|
|---|---|---|
|**Query 1 (Bad)**|`Employee × Branch → Select managers in London`|Cartesian product of all employees and all branches first — generates a massive intermediate table before any filtering|
|**Query 2 (Better)**|`Employee ⋈ Branch → Select managers in London`|Join is smarter than Cartesian Product — only produces matching pairs|
|**Query 3 (Best)**|`Select(managers) → Select(London branches) → Join`|Reduces both tables to only relevant rows FIRST, then joins the tiny results — the smallest possible intermediate tables|

The fundamental insight: **always reduce the data size as early as possible**.

---

## 6.5 Approach 1: Heuristic (Logical) Optimization

**Heuristic optimization** improves query performance by applying a set of transformation rules to **logically restructure** the relational algebra expression — without yet considering physical storage details.

### The Query Tree

A **query tree** is a graphical (tree-based) representation of a relational algebra expression:

|Element|Position|Represents|
|---|---|---|
|**Leaves**|Bottom of tree|Base relations — the actual starting tables|
|**Internal Nodes**|Middle of tree|Intermediate relational algebra operations|
|**Root**|Top of tree|The final result of the entire query|
|**Execution order**|Bottom → Top|Always executed from leaves upward to the root|

A **query block** is one complete `SELECT-FROM-WHERE` expression (including `GROUP BY` and `HAVING`). Nested SQL queries are processed as separate, independent query blocks.

### The Core Heuristic Rules — "The Golden Rules of Optimization"

These two rules capture the most important transformations:

#### Rule 1: Apply SELECT and PROJECT as Early as Possible

Move SELECT (σ) and PROJECT (π) operations as **far down the query tree as possible** — as close to the leaf (base table) level as possible. This reduces the number of rows and columns in intermediate results early in the execution, making all subsequent operations faster and cheaper.

```
❌ Bad Order:
   (Employee × Branch) → σ(branch='London') → σ(job='Manager')

✅ Good Order:
   σ(job='Manager')(Employee) → σ(branch='London')(Branch) → JOIN
```

#### Rule 2: Execute the Most Restrictive Operations First

When multiple Select or Join operations must be performed, execute the **most restrictive one first** — the one that eliminates the most rows. This ensures the dataset shrinks as quickly as possible before more expensive operations are applied.

> 📌 **Note:** "Restrictive" means an operation that produces the fewest output rows relative to its input. A select on a rare condition (e.g., `salary > 500,000`) is more restrictive than one on a common condition (e.g., `age > 18`).

---

## 6.6 Approach 2: Cost Estimation (Physical) Optimization

While heuristic optimization uses logical rules, **cost-based optimization** uses **actual statistics** about the database to mathematically estimate and compare the real cost of different execution plans.

The optimizer retrieves these statistics from the **System Catalog** (the database's internal metadata store) — things like the number of rows in each table, the number of distinct values in each column, and the distribution of data values.

### The Cost Function

The total cost of executing a query plan is estimated as a combination:

```
Total Cost = I/O Cost + CPU Cost + Memory/Storage Cost + Communication Cost
```

### Detailed Cost Components

|Cost Component|What It Measures|Key Factors|
|---|---|---|
|**Secondary Storage (I/O) Cost**|Cost of reading data blocks from disk and writing results back|File organization (sequential vs. scattered), number of disk blocks accessed, use of indexes|
|**Storage Cost**|Main memory (RAM) required for intermediate results|Size of intermediate tables — larger results consume more limited RAM|
|**Computation Cost**|CPU power needed for in-memory operations|Sorting, hashing, comparing, arithmetic on data values|
|**Communication Cost**|Network bandwidth for distributed queries|Volume of data transferred between database server and client terminal|

### Why I/O Cost Dominates

Disk access is **orders of magnitude slower** than main memory access:

- RAM access: ~nanoseconds (10⁻⁹ seconds)
- SSD disk access: ~microseconds (10⁻⁶ seconds)
- HDD disk access: ~milliseconds (10⁻³ seconds)

This means a query that requires one extra disk read can cost more than thousands of in-memory operations. Minimizing disk I/O is always the optimizer's primary objective.

### Heuristic vs. Cost-Based Optimization

|Feature|Heuristic (Logical)|Cost-Based (Physical)|
|---|---|---|
|**Basis**|Transformation rules and logical reasoning|Statistical estimates from the system catalog|
|**Speed**|Fast — rule application is quick|Slower — must estimate and compare many plans|
|**Accuracy**|Good general improvements but not always optimal|More accurate — considers actual data distribution|
|**Knowledge needed**|No statistics required|Requires up-to-date statistics in the catalog|
|**Typical use**|First pass — quick structural improvements|Second pass — fine-grained physical plan selection|

> ⭐ **Exam Tip:** In practice, modern query optimizers use **both approaches together** — heuristic rules first to prune the search space, then cost estimation to select the best physical plan from the remaining candidates.


# Chapter 6 — Practice Questions

### Query Processing and Optimization

#### 25 Multiple Choice Questions · Correct answers marked with ✅

---

**1.** SQL is described as a "declarative" language. What does this mean in the context of query processing?

- A) SQL queries must be declared at the beginning of a program before being used
- B) SQL queries are compiled into machine code before being executed
- C) SQL queries use special DECLARE keywords to define variables
- D) **The user specifies WHAT data is needed, not HOW to retrieve it — the DBMS determines the best execution strategy** ✅

> **Answer: D** — Declarative means you describe the desired result (SELECT name FROM Employee WHERE salary > 50000) without specifying how to find it (which index to use, which join algorithm to apply). The Query Optimizer figures out the best "how."

---

**2.** What is the PRIMARY goal of Query Optimization?

- A) To ensure that all SQL queries follow correct syntax and grammar rules
- B) To translate SQL into relational algebra before execution
- C) To verify that all table names and column names in the query actually exist
- D) **To select the most efficient execution plan from multiple equivalent alternatives, minimizing resource usage (especially disk I/O)** ✅

> **Answer: D** — Query Optimization chooses the single best Query Execution Plan (QEP) from potentially hundreds of mathematically equivalent plans. The primary optimization target is minimizing disk I/O, as disk access is orders of magnitude slower than memory operations.

---

**3.** In the optimization example comparing three query strategies for "Managers in London branches," Query 3 (SELECT first, then JOIN) required only 1,160 I/O operations compared to Query 1's 101,050. This improvement was achieved by:

- A) Using a faster disk with better I/O performance
- B) Removing the WHERE clause conditions to simplify the query
- C) Caching all table data in RAM before executing the query
- D) **Reducing the size of both tables first by filtering rows BEFORE performing the expensive JOIN operation** ✅

> **Answer: D** — The fundamental insight of query optimization: apply SELECT (filter rows) as early as possible to shrink the data before joining. Query 3 joined tiny filtered sets instead of entire tables, resulting in an 87× performance improvement.

---

**4.** During which step of query processing does the DBMS verify that all table names and column names in the query actually EXIST in the database schema?

- A) Scanning
- B) Parsing
- C) Translation
- D) **Validation** ✅

> **Answer: D** — Validation checks semantic correctness — it ensures that the query references real, existing tables and columns. Scanning tokenizes the query; Parsing checks syntax/grammar; Translation converts to relational algebra.

---

**5.** What is the output of the TRANSLATION step in the query processing pipeline?

- A) The final result set returned to the user
- B) An optimized physical execution plan ready for the code generator
- C) A list of syntax errors and semantic violations found in the query
- D) **An initial internal representation of the query in Relational Algebra** ✅

> **Answer: D** — Translation converts the validated SQL query into a relational algebra expression. This intermediate representation is then handed to the Query Optimizer, which transforms it into an efficient execution plan.

---

**6.** Which query processing step identifies SQL keywords, attribute names, and relation names as individual tokens?

- A) Parsing
- B) Validation
- C) Translation
- D) **Scanning** ✅

> **Answer: D** — Scanning (also called lexical analysis) is the first step. It reads the raw SQL text and breaks it into meaningful tokens: keywords (SELECT, FROM, WHERE), identifiers (table names, column names), operators, and literals.

---

**7.** The Relational Algebra SELECT operation (σ) differs from the SQL SELECT statement. The RA SELECT operation:

- A) Chooses specific columns to display, discarding the rest
- B) Creates a new table by combining rows from two existing tables
- C) Removes duplicate rows from the result of a query
- D) **Filters ROWS — returns only the tuples that satisfy a specified condition** ✅

> **Answer: D** — In Relational Algebra, SELECT (σ) is a row filter — it returns a subset of tuples that satisfy a condition. It does NOT select columns (that is PROJECT). Confusingly, SQL's SELECT statement combines both row filtering (WHERE) and column selection.

---

**8.** The Relational Algebra PROJECT operation (π) does which of the following?

- A) Filters rows based on a Boolean condition
- B) Combines every tuple from one table with every tuple from another
- C) Returns rows that appear in both of two union-compatible tables
- D) **Selects specific COLUMNS and discards the rest, automatically eliminating duplicate rows** ✅

> **Answer: D** — PROJECT (π) is a column filter. It selects which attributes to keep and removes the others. It also automatically removes duplicate tuples from the result (like SQL's SELECT DISTINCT). It does not filter rows.

---

**9.** For the UNION, INTERSECTION, and SET DIFFERENCE operations in Relational Algebra, both participating tables must be:

- A) Located in the same database file on disk
- B) Owned by the same database user with identical access permissions
- C) Have the same primary key attribute with matching data types
- D) **Union-compatible — the same number of columns AND corresponding columns must have the same domains** ✅

> **Answer: D** — Union-compatible means the two tables have identical structure for set operations to make sense: same degree (number of columns) and matching domain types for each corresponding column pair.

---

**10.** The Relational Algebra INTERSECTION (∩) of tables A and B returns:

- A) All tuples from A plus all tuples from B, with duplicates removed
- B) All tuples in A that do NOT appear in B
- C) All possible combinations of tuples from A and tuples from B
- D) **Only the tuples that appear in BOTH table A AND table B** ✅

> **Answer: D** — INTERSECTION returns the overlap — rows present in both tables. UNION returns all rows from both (no duplicates). SET DIFFERENCE (A − B) returns rows in A but not in B. Cartesian Product combines all row pairs.

---

**11.** The Cartesian Product (×) of Table A (100 rows) and Table B (50 rows) produces how many rows?

- A) 150 rows (100 + 50)
- B) 50 rows (the smaller table size)
- C) 100 rows (the larger table size)
- D) **5,000 rows (100 × 50)** ✅

> **Answer: D** — Cartesian Product combines EVERY tuple from A with EVERY tuple from B. With m rows in A and n rows in B, the result has m × n rows. This is why Cartesian Products are so expensive and should be avoided in favor of JOINs.

---

**12.** A Theta Join (⋈) in Relational Algebra is essentially:

- A) A Union operation followed by a Select on matching keys
- B) A Project operation that combines columns from two different tables
- C) An Intersection that finds matching rows between two tables
- D) **A Cartesian Product followed by a SELECT that filters only the rows satisfying a join condition** ✅

> **Answer: D** — A JOIN is conceptually a Cartesian Product + Select, but implemented far more efficiently. The Theta Join uses any comparison operator (=, <, >, ≤, ≥, ≠) to define which row combinations are meaningful.

---

**13.** In a Query Tree, execution always proceeds in which direction?

- A) From the root downward to the leaves (top-down)
- B) In alphabetical order of the operation names
- C) In the order in which operations appear in the original SQL query
- D) **From the leaves upward to the root (bottom-up)** ✅

> **Answer: D** — Query trees are executed bottom-up. The leaves are the base relations (actual tables). Operations at the bottom execute first, producing intermediate results that feed upward into higher-level operations, until the root produces the final query result.

---

**14.** In a Query Tree, what do the LEAVES of the tree represent?

- A) The final result of the entire query
- B) The most expensive operations (like Cartesian Products)
- C) Intermediate results passed between operations
- D) **The BASE RELATIONS — the actual starting tables the query reads from** ✅

> **Answer: D** — Leaves in a query tree are always the base relations (actual database tables). They are the starting point from which all data flows upward through the tree of operations until the root produces the final answer.

---

**15.** A "Query Block" in heuristic optimization refers to:

- A) A physical block of disk space reserved for query execution
- B) A cache of recently executed queries stored in memory
- C) A collection of related tables that must always be queried together
- D) **A single SELECT-FROM-WHERE expression (including GROUP BY/HAVING); nested queries become separate query blocks** ✅

> **Answer: D** — A query block is one complete SELECT statement. When SQL queries are nested (subqueries), each level of nesting becomes a separate query block that the optimizer processes independently.

---

**16.** The FIRST core heuristic rule of query optimization states that SELECT (σ) and PROJECT (π) operations should be:

- A) Applied last — after all joins and products are complete
- B) Applied in alphabetical order of the column names they reference
- C) Combined into a single operation to reduce the number of tree nodes
- D) **Applied as EARLY as possible — moved as far DOWN the query tree as possible toward the leaves** ✅

> **Answer: D** — Applying SELECT and PROJECT early means filtering rows and removing unnecessary columns before expensive operations like JOINs. This minimizes the size of intermediate results, making all subsequent operations faster and cheaper.

---

**17.** The SECOND core heuristic rule states that when multiple SELECT or JOIN operations must be performed, you should:

- A) Perform them in the order they appear in the original SQL query
- B) Perform the LEAST restrictive operations first to build up larger intermediate tables
- C) Always perform JOINs before SELECTs regardless of their selectivity
- D) **Perform the MOST RESTRICTIVE operations first — the ones that eliminate the most rows** ✅

> **Answer: D** — "Most restrictive first" means execute the operation that reduces the dataset the most before moving on to the next. If one SELECT reduces a 1,000,000-row table to 10 rows, doing that first makes every subsequent operation work on only 10 rows instead of 1,000,000.

---

**18.** Heuristic optimization is described as "logical" optimization. This means it:

- A) Uses formal mathematical proofs to guarantee the optimal execution plan
- B) Relies on statistics from the system catalog to estimate physical costs
- C) Applies machine learning algorithms to learn from past query executions
- D) **Applies transformation rules to restructure the query algebraically WITHOUT considering physical storage details** ✅

> **Answer: D** — Heuristic (logical) optimization rearranges the relational algebra expression using known transformation rules (like pushing selections down) without looking at actual table sizes or index structures. Cost-based (physical) optimization handles those physical details.

---

**19.** Cost-based (physical) optimization differs from heuristic optimization primarily because it:

- A) Only works on simple single-table queries without joins
- B) Applies logical transformation rules to the query tree
- C) Does not consider disk I/O when estimating execution costs
- D) **Uses STATISTICS from the system catalog to mathematically estimate and compare the real physical cost of different execution plans** ✅

> **Answer: D** — Cost-based optimization uses stored statistics (row counts, distinct value counts, data distribution) to numerically estimate the actual cost (I/O, CPU, memory) of different plans and selects the cheapest one. It handles physical realities that heuristic rules cannot.

---

**20.** In the Cost Function for query optimization, which cost component is consistently the MOST IMPORTANT to minimize?

- A) Communication Cost — network data transfer
- B) Computation Cost — CPU usage for sorting and hashing
- C) Storage Cost — RAM used for intermediate results
- D) **Secondary Storage (I/O) Cost — disk read and write operations** ✅

> **Answer: D** — Disk I/O is the dominant cost factor. HDD access takes milliseconds while RAM access takes nanoseconds — a difference of ~6 orders of magnitude. A single extra disk read can cost more than millions of memory operations, making I/O minimization the optimizer's top priority.

---

**21.** The System Catalog is important for cost-based optimization because it stores:

- A) The source code of all user-written stored procedures and triggers
- B) The physical location (disk address) of every row in every table
- C) The complete transaction log of all past database operations
- D) **Statistics about tables (row counts, distinct values, data distribution) used to estimate query execution costs** ✅

> **Answer: D** — The System Catalog (data dictionary) is the DBMS's internal metadata store. For optimization, it provides statistics like the number of rows in each table, the number of distinct values per column, and histogram data about value distributions — all needed to estimate costs accurately.

---

**22.** Which of the following correctly describes a "Natural Join" in Relational Algebra?

- A) A join that uses any comparison operator including <, >, and ≠
- B) A join that combines ALL possible row combinations from two tables
- C) A join that returns only rows where all column values are identical
- D) **An Equijoin that automatically matches columns with the SAME NAME and removes the duplicate matched columns from the result** ✅

> **Answer: D** — A Natural Join is a special Equijoin: it automatically finds columns with matching names in both tables, joins on them using equality, and eliminates the duplicate columns from the output. No join condition needs to be explicitly specified.

---

**23.** The Scanning step in query processing would catch which of the following errors?

- A) A reference to a column name that does not exist in the specified table
- B) A missing comma between column names in the SELECT list
- C) Using a table that the current user does not have permission to access
- D) **An illegal character or completely unrecognized token in the query text** ✅

> **Answer: D** — Scanning (lexical analysis) identifies and classifies tokens. Truly unrecognized input (illegal characters, garbled text) is caught here. Missing commas and syntax errors are caught by Parsing; non-existent columns are caught by Validation.

---

**24.** Modern query optimizers in production DBMS systems typically use:

- A) Only heuristic (logical) optimization for all queries
- B) Only cost-based (physical) optimization for all queries
- C) Neither approach — they rely on the programmer to manually choose execution plans
- D) **BOTH approaches together — heuristic rules first to reduce the search space, then cost estimation to select the best physical plan** ✅

> **Answer: D** — In practice, modern optimizers combine both: heuristic transformations quickly eliminate obviously poor plans (reducing the search space from millions to thousands of candidates), then cost-based estimation identifies the single best plan among the remaining candidates.

---

**25.** The PROJECT operation (π) in Relational Algebra is said to "automatically eliminate duplicates." In SQL, which keyword achieves the equivalent effect?

- A) UNIQUE
- B) GROUP BY
- C) HAVING
- D) **DISTINCT** ✅

> **Answer: D** — SQL's SELECT DISTINCT is equivalent to the Relational Algebra PROJECT operation in its duplicate-elimination behavior. A plain SQL SELECT (without DISTINCT) can return duplicate rows, but RA's PROJECT by definition produces a set (no duplicates).

---

_End of Chapter 6 Practice Questions_