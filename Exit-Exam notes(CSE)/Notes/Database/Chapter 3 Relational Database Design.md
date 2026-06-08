
## 3.1 Relational Database Fundamentals

A **relational database** organizes all data into a collection of **tables** (also called **relations**). This model, originally proposed by E.F. Codd in 1970, is based on mathematical set theory and is the foundation of virtually every modern database system (Oracle, MySQL, PostgreSQL, SQL Server, etc.).

Understanding the precise terminology is important because different terms are used interchangeably in different contexts:

|Formal Term|Common Synonym|Meaning|
|---|---|---|
|**Relation**|Table|A two-dimensional structure with rows and columns that stores data about one entity type|
|**Tuple**|Row / Record|A single data instance — one complete set of values for one entity instance|
|**Attribute**|Column / Field|A single property or characteristic of the entity|
|**Domain**|Data Type|The restricted set of all valid values for an attribute (e.g., Integer, VARCHAR(50), Date)|
|**Relation Schema**|Table Definition / Blueprint|The structure of the table: its name, column names, and their domains|
|**Relation Instance**|Table Contents / Data|The actual set of tuples (rows) stored in the table at a specific moment in time|
|**Degree / Arity**|Number of Columns|The total count of attributes (columns) in a relation|
|**Cardinality**|Number of Rows|The total count of tuples (rows) in a relation instance at a given moment|

### Important Properties of a Relation

- Every relation must have a **unique name**
- **No two tuples should be identical** — every row must be distinguishable
- **Row order does not matter** — the relational model treats a table as a mathematical set, not an ordered list
- **Column order does not matter** — columns are identified by name, not position
- All values in a column must come from the same domain (data type)

---

## 3.2 SQL — Data Definition and Manipulation

**SQL (Structured Query Language)** is the standard language for interacting with relational databases. It is divided into two major sub-languages:

- **DDL (Data Definition Language)** — defines and modifies the _structure_ of the database (tables, schemas)
- **DML (Data Manipulation Language)** — queries and modifies the _data_ inside the tables

### Core SQL Commands

|Command|Category|Purpose|Example|
|---|---|---|---|
|`CREATE TABLE`|DDL|Creates a new table with specified columns and data types|`CREATE TABLE Student (ID INT, Name VARCHAR(50));`|
|`ALTER TABLE`|DDL|Modifies an existing table — adds or removes columns|`ALTER TABLE Student ADD GPA DECIMAL(3,2);`|
|`DROP TABLE`|DDL|Permanently deletes both the table's data AND its schema|`DROP TABLE Student;`|
|`INSERT`|DML|Adds a new row/tuple into a table|`INSERT INTO Student VALUES (1, 'Abebe', 3.8);`|
|`UPDATE`|DML|Modifies existing rows (use `WHERE` to target specific rows)|`UPDATE Student SET GPA = 3.9 WHERE ID = 1;`|
|`DELETE`|DML|Removes rows from a table (use `WHERE` to avoid deleting all)|`DELETE FROM Student WHERE ID = 1;`|
|`SELECT`|DML|Retrieves data from one or more tables|`SELECT Name, GPA FROM Student WHERE GPA > 3.5;`|

> ⭐ **Exam Tip:** `DROP TABLE` removes the entire table including its structure. `DELETE` (without `WHERE`) removes all rows but leaves the table structure intact. This is a frequently tested distinction.

---

## 3.3 Integrity Constraints

**Integrity constraints** are rules enforced by the DBMS to ensure that the data stored in the database is valid, consistent, and meaningful. The DBMS automatically checks every insert, update, and delete operation against all applicable constraints and rejects any operation that would violate them.

### Types of Integrity Constraints

#### Domain Constraints

Specifies that every value in a column must belong to a defined domain (data type and range).

- Example: An `Age` column must be of type `INTEGER`; a `GPA` must be `DECIMAL` between 0.0 and 4.0

#### Key Constraints

Ensures that one or more columns uniquely identify each row. Two specific keywords:

|Keyword|Allows NULL?|Duplicates?|Purpose|
|---|---|---|---|
|`UNIQUE`|✅ (in some DBMS)|❌|Identifies candidate keys — no duplicates allowed|
|`PRIMARY KEY`|❌ Never|❌|The main identifier — no duplicates AND no nulls|

#### Foreign Key Constraints (Referential Integrity)

A **foreign key** is an attribute in one table that references the **primary key** of another table. This enforces the relationship between tables and ensures that referenced records actually exist.

- Example: In an `ENROLLMENT` table, `StudentID` is a foreign key that references `Student.StudentID`. You cannot enroll a student that doesn't exist in the `Student` table.

### Handling Foreign Key Violations

When a DELETE or UPDATE on a referenced (parent) table would violate referential integrity, the DBMS can respond in four ways:

|Response|Behavior|Use Case|
|---|---|---|
|`NO ACTION`|Rejects the operation entirely|**Default behavior** — safest option|
|`CASCADE`|Automatically deletes/updates all referencing rows in the child table|When child records are meaningless without the parent|
|`SET NULL`|Sets the foreign key in the child table to `NULL`|When the child record can exist independently|
|`SET DEFAULT`|Sets the foreign key to a pre-specified default value|When there is a meaningful default parent|

> ⭐ **Exam Tip:** Understanding `ON DELETE CASCADE` vs `ON DELETE SET NULL` vs `NO ACTION` is a very commonly tested concept. Know what each does and when you'd use it.

---

## 3.4 Mapping ER Diagrams to Relational Schemas

Once the ER diagram is complete, it must be converted into an actual relational database schema (a set of table definitions). This translation follows a set of well-established rules.

### Rule 1 — Entities Become Tables

Every entity in the ER diagram becomes a table. The entity's name becomes the table's name.

### Rule 2 — Attributes Become Columns

|Attribute Type|How to Map|
|---|---|
|**Simple / Atomic**|Becomes a column directly in the entity's table|
|**Composite**|The composite parent is ignored; only the individual sub-attributes become columns|
|**Multi-valued**|Creates a **brand new separate table** containing: the primary key of the main entity (as a foreign key) + the multi-valued attribute|
|**Derived**|Generally **not stored** — calculated at query time using SQL expressions|

### Rule 3 — Relationships Become Foreign Keys

|Cardinality|How to Map|
|---|---|
|**1:1 (One-to-One)**|Post the primary key of one entity as a foreign key into the other entity's table (choose the side with total participation if it exists)|
|**1:M (One-to-Many)**|Post the primary key from the **"One" side** as a foreign key into the table on the **"Many" side**|
|**M:N (Many-to-Many)**|Create a new **junction table** (also called an associative entity or bridge table) containing the primary keys of **both** participating entities as foreign keys. Together, these two foreign keys form the **compound primary key** of the new table|

### Example — Mapping a M:N Relationship

```
STUDENT (StudentID, Name, GPA)          ← original tables
COURSE  (CourseID, CourseName, Credits)

ENROLLMENT (StudentID, CourseID, Grade) ← new junction table
              FK→STUDENT  FK→COURSE
              └──────────── compound PK
```

### Mapping Class Hierarchies (EER Superclass/Subclass)

**Approach 1 — Table per class:**

- Create one table for the superclass with all shared attributes
- Create one table for each subclass with its unique attributes + the superclass primary key (as FK and PK)
- Preferred when subclasses have many unique attributes

**Approach 2 — Subclass tables only:**

- Create only subclass tables, each absorbing all superclass attributes
- Only valid when there is **total coverage** (every superclass instance belongs to at least one subclass)
- Results in data redundancy for shared attributes

---

## 3.5 Functional Dependencies and Anomalies

Before learning normalization, you must understand what problems it is trying to solve and the formal concept used to describe them.

### Update Anomalies — The Problem with Poor Design

When a table is poorly designed and contains redundant (repeated) data, three types of anomalies can occur:

|Anomaly|Description|Example|
|---|---|---|
|**Insert Anomaly**|Cannot insert certain data because other, unrelated data doesn't exist yet|Cannot add a new `Project` without first assigning an `Employee` to it, even if the employee isn't known yet|
|**Delete Anomaly**|Deleting one piece of data accidentally destroys other valid, unrelated information|Deleting an employee who is the only one assigned to a project also deletes the project's information entirely|
|**Update Anomaly**|Changing a value requires updating the same data in multiple redundant rows; missing even one creates inconsistency|If a department's phone number changes, it must be updated in every employee row — missing one row makes the data inconsistent|

### Functional Dependencies (FDs)

A **functional dependency** is a formal, mathematical way to describe the relationship between attributes in a table. It is the theoretical foundation of normalization.

**Notation:** `X → Y` reads as "X **determines** Y" or "X **functionally determines** Y"

**Meaning:** If any two tuples (rows) in a table have the same value for attribute X, they must also have the same value for attribute Y. In other words, knowing the value of X is sufficient to uniquely determine the value of Y.

**Examples:**

- `StudentID → Name` — knowing a student's ID tells you their name exactly
- `StudentID → GPA` — knowing a student's ID tells you their GPA
- `CourseID → CourseName, Credits` — knowing a course ID determines both its name and credit hours
- `StudentID, CourseID → Grade` — only by knowing _both_ the student and the course can you determine the grade

> ⭐ **Exam Tip:** Functional dependencies are the heart of normalization. You cannot understand 2NF, 3NF, or BCNF without being completely comfortable with FDs. Practice identifying them from table descriptions.

---

## 3.6 Keys and Attribute Terminology

A precise understanding of key types is essential for normalization:

|Key Type|Definition|
|---|---|
|**Superkey**|Any set of attributes that uniquely identifies a row. May contain extra, unnecessary attributes.|
|**Candidate Key**|A **minimal** superkey — a superkey with no unnecessary attributes. Every attribute in it is needed for uniqueness.|
|**Primary Key**|The one candidate key chosen by the designer to be the official row identifier|
|**Alternate Key**|Any candidate key that was not chosen as the primary key|
|**Prime Attribute**|Any attribute that is a member of **at least one** candidate key|
|**Non-prime Attribute**|Any attribute that is **not** a member of any candidate key|

> 📌 **Note:** The distinction between prime and non-prime attributes is critical for understanding 2NF and 3NF, which are defined specifically in terms of non-prime attributes and their dependencies.

---

## 3.6.1 Backus-Naur Form (BNF)

**Backus-Naur Form (BNF)** is a formal metalanguage (a language for describing languages) used to express the grammar — the valid structure — of a language. In database courses, BNF is used to:

- Describe the precise syntax of SQL statements in an unambiguous way
- Express relation schemas compactly and formally
- Specify how functional dependencies and constraints are written

> ⭐ **Critical Exam Tip:** **BNF ≠ BCNF.** These two acronyms look similar but are completely unrelated concepts:
> 
> - **BNF = Backus-Naur Form** — a grammar notation tool (this section)
> - **BCNF = Boyce-Codd Normal Form** — a database normalization rule (Section 3.7) Do not confuse them. This is a very common source of mistakes in exams.

### BNF Notation Reference

|Symbol|Meaning|
|---|---|
|`::=`|"is defined as"|
|`\|`|"or" (alternative option)|
|`<name>`|Non-terminal — a grammar category that needs further definition|
|`terminal`|A literal token — an actual keyword or punctuation that appears as-is|
|`{ ... }`|Zero or more repetitions|
|`[ ... ]`|Optional element|

### BNF Examples in Database Context

**Example 1 — Relation Schema Grammar:**

```
<relation>        ::= <relation_name> '(' <attribute_list> ')'
<attribute_list>  ::= <attribute> | <attribute_list> ',' <attribute>
<attribute>       ::= <attribute_name> <domain>
<domain>          ::= INTEGER | VARCHAR '(' <length> ')' | DATE
```

This grammar says a valid relation like `Student(StudentID INTEGER, Name VARCHAR(50))` is one valid `<relation>`.

**Example 2 — CREATE TABLE Grammar:**

```
<create_table> ::= CREATE TABLE <relation_name> '(' <column_defs> ')'
<column_defs>  ::= <column_def> | <column_def> ',' <column_defs>
<column_def>   ::= <attribute_name> <domain> [ PRIMARY KEY ] [ NOT NULL ]
```

**Example 3 — Functional Dependency Notation:**

```
<fd>            ::= <attribute_set> '->' <attribute_set>
<attribute_set> ::= <attribute_name> | <attribute_name> <attribute_set>
```

Example FDs written formally: `StudentID -> Name`, `CourseID -> CourseName, Credits`

---

## 3.7 Normalization and Normal Forms

**Normalization** is the systematic process of restructuring and decomposing poorly designed database tables into smaller, well-structured tables that eliminate redundancy and update anomalies, while preserving the original data completely and correctly.

### What Makes a Good Decomposition?

When a table is split into smaller tables, two properties must hold:

- **Lossless Join:** When the decomposed tables are joined back together using their keys, the result must be exactly the original table — no extra (spurious) rows and no missing rows
- **Dependency Preserving:** Every functional dependency that existed in the original table can still be checked in the decomposed tables without needing to perform a join (not always achievable — BCNF sometimes sacrifices this)

---

### First Normal Form (1NF)

**The Fundamental Rule:** Every attribute value in every cell must be **atomic** — meaning single, indivisible, and from a single domain. No cell can contain a list, a set, or a repeating group.

**What violates 1NF?**

- A cell containing multiple values: `Phones = "0911, 0922"`
- A repeating group of columns: `Phone1`, `Phone2`, `Phone3`
- A nested table or array stored in a cell

**Example of a 1NF Violation:**

|StudentID|Name|Phones|
|---|---|---|
|S1|Abebe|0911, 0922|
|S2|Tigist|0933|

**Fixed to 1NF (flatten into separate rows):**

|StudentID|Name|Phone|
|---|---|---|
|S1|Abebe|0911|
|S1|Abebe|0922|
|S2|Tigist|0933|

> 📌 **Note:** 1NF alone does NOT eliminate redundancy or anomalies. It is only the first step. A table in 1NF can still have serious problems — which is why further normal forms exist.

---

### Second Normal Form (2NF)

**The Rule:** A table is in 2NF if and only if:

1. It is already in **1NF**, AND
2. Every **non-prime attribute** is **fully functionally dependent** on the **entire primary key** — not just part of it

This rule only applies when the primary key is **composite** (made up of more than one attribute). If the primary key is a single attribute, 1NF automatically implies 2NF.

**What violates 2NF?** A **partial dependency** — when a non-prime attribute depends on only _some_ of the composite key's attributes, not all of them.

**Example of a 2NF Violation:**

Table: `Enrollment(StudentID, CourseID, Grade, CourseName)`

- Primary Key: `{StudentID, CourseID}` (composite)
- `StudentID, CourseID → Grade` ✅ — full dependency on the whole key
- `CourseID → CourseName` ❌ — `CourseName` depends only on `CourseID`, a _part_ of the key (partial dependency)

**Fix — Decompose:**

```
Enrollment(StudentID, CourseID, Grade)     ← full dependency preserved
Course(CourseID, CourseName)               ← partial dependency moved here
```

---

### Third Normal Form (3NF)

**The Rule:** A table is in 3NF if and only if:

1. It is already in **2NF**, AND
2. No **non-prime attribute** is **transitively dependent** on the primary key

**What is a transitive dependency?** When `PK → A` and `A → B` (where A is a non-prime attribute), then `PK → B` indirectly _through_ A. This indirect chain is a transitive dependency and violates 3NF.

**Example of a 3NF Violation:**

Table: `Student(StudentID, Name, DeptID, DeptName)`

- `StudentID → DeptID` ✅
- `DeptID → DeptName` ❌ — `DeptName` depends on `DeptID`, which is itself a non-prime attribute (transitive dependency)
- Therefore: `StudentID → DeptName` only _transitively_ through `DeptID`

**Fix — Decompose:**

```
Student(StudentID, Name, DeptID)          ← DeptName removed
Department(DeptID, DeptName)              ← transitive chain resolved
```

---

### Boyce-Codd Normal Form (BCNF)

**The Rule:** A table is in BCNF if and only if:

- For every **non-trivial** functional dependency `X → Y` in the relation, **X must be a superkey**

BCNF is a **stricter version of 3NF**. The difference is subtle: 3NF allows certain FDs where the determinant (X) is not a superkey, as long as the dependent (Y) is a prime attribute. BCNF eliminates even those cases.

> ⭐ **Key difference:** 3NF → BCNF when **every determinant is also a candidate key**. BCNF is always lossless but **may NOT be dependency preserving** (a trade-off).

**BCNF Violation Example:**

Table: `StudentAdvisor(StudentID, CourseID, Instructor)`

FDs:

- `StudentID, CourseID → Instructor` (composite key)
- `Instructor → CourseID` ← violates BCNF! `Instructor` is not a superkey

**Fix — Decompose into BCNF:**

```
S1(StudentID, Instructor)     ← with key StudentID
S2(Instructor, CourseID)      ← with key Instructor
```

---

### Fourth Normal Form (4NF)

**The Rule:** A table is in 4NF if and only if:

1. It is in **BCNF**, AND
2. It contains **no non-trivial multivalued dependencies (MVDs)** except those implied by a candidate key

**What is a Multivalued Dependency (MVD)?** Written as `X →→ Y`, it means: for a given value of X, a **set of Y values** exists that is completely independent of all other attributes in the table.

**4NF Violation Example:**

A course can have multiple textbooks AND multiple teachers, but textbooks and teachers are completely independent of each other:

- `Course →→ Textbook` and `Course →→ Teacher`

Storing both in one table forces every textbook to be paired with every teacher — creating redundant and misleading combinations.

**Fix — Decompose:**

```
CourseTextbook(Course, Textbook)
CourseTeacher(Course, Teacher)
```

---

### Fifth Normal Form (5NF) / Project-Join Normal Form (PJ/NF)

**The Rule:** A table is in 5NF if and only if:

1. It is in **4NF**, AND
2. Every **join dependency** is implied by the candidate keys — the relation cannot be decomposed further into projections without lossless join problems

5NF addresses situations where **three or more independent binary relationships** interact. Storing them in a single ternary table can generate **invalid combinations** of data that were never actually intended.

**Classic Example:** `Supply(Provider, Part, Project)`

- A provider supplies certain parts
- A provider works on certain projects
- A project uses certain parts
- But there is NO constraint that "Provider X supplies Part Y to Project Z" — the three binary relationships are independent

**Fix — Decompose into three binary tables:**

```
ProviderPart(Provider, Part)
ProviderProject(Provider, Project)
ProjectPart(Project, Part)
```

---

### Domain-Key Normal Form (DKNF)

**The Rule:** Every constraint on the relation is a logical consequence of **domain constraints** and **key constraints** only — no other types of constraints are needed.

DKNF is the **most general normal form** in theory. A relation in DKNF has no anomalies whatsoever. However, it is very difficult to verify formally and is rarely used directly in practical undergraduate database design.

---

### Sixth Normal Form (6NF) — Temporal Data

**The Rule:** A relation is in 6NF if it cannot be further decomposed without losing information — specifically in the context of **time-varying or historical data**.

6NF is used in **temporal databases** where every fact needs to be stored alongside its **valid time period** (when it was true). It is primarily relevant in data warehousing, audit trail systems, and historical record-keeping applications. Detailed decomposition at this level is typically covered at the graduate level.

---

## 3.8 Complete Normal Forms Reference Table

|Normal Form|Full Name|Main Requirement|Problem It Eliminates|
|---|---|---|---|
|**1NF**|First Normal Form|All attribute values are atomic|Repeating groups, multi-valued cells|
|**2NF**|Second Normal Form|No partial dependencies on composite key|Non-prime attribute depends on part of the key|
|**3NF**|Third Normal Form|No transitive dependencies through non-key|Non-key → non-key dependency chains|
|**BCNF**|Boyce-Codd Normal Form|Every determinant is a superkey|Stricter violations not caught by 3NF|
|**4NF**|Fourth Normal Form|No non-trivial multivalued dependencies|Independent multi-valued facts in one table|
|**5NF**|Fifth Normal Form (PJ/NF)|No non-trivial join dependencies|Incorrect recombinations from three-way relationships|
|**DKNF**|Domain-Key Normal Form|All constraints follow from domain + key constraints|All constraint-based anomalies|
|**6NF**|Sixth Normal Form|No further lossless decomposition possible|Temporal / time-varying data redundancy|

> ⭐ **Critical Reminder:** **BNF (Backus-Naur Form) is NOT a normal form.** It is grammar notation (§3.6.1). Do not list it alongside 1NF, 2NF, etc.

### Practical Normalization Workflow

```
Start: Raw ER diagram → mapped tables
  ↓
Step 1: Ensure 1NF — eliminate multi-valued cells and repeating groups
  ↓
Step 2: List all functional dependencies for each table
  ↓
Step 3: Eliminate partial dependencies → achieve 2NF
  ↓
Step 4: Eliminate transitive dependencies → achieve 3NF
  ↓
Step 5 (optional): Apply BCNF if every determinant must be a candidate key
  ↓
Step 6 (optional): Apply 4NF if independent multi-valued facts exist
  ↓
Step 7: Verify all decompositions are lossless (no spurious tuples on re-join)
```

---

## 3.9 Denormalization

**Denormalization** is the deliberate, intentional process of **reversing some normalization steps** — merging tables back into a less-normalized form to improve **read performance**.

This might seem counterintuitive, but it is a real and widely used technique in specific contexts:

### When is Denormalization Used?

|System Type|Approach|Reason|
|---|---|---|
|**OLTP** (Online Transaction Processing)|Fully normalized (3NF or BCNF)|Frequent inserts/updates need data integrity; anomalies are unacceptable|
|**OLAP** (Online Analytical Processing) / Data Warehouses|Deliberately denormalized|Complex reports join many tables — denormalization reduces joins and speeds up read-heavy queries|

### Trade-Offs of Denormalization

|Benefit|Cost|
|---|---|
|Faster query performance — fewer joins needed|More data redundancy — same data stored in multiple places|
|Simpler queries — fewer tables to join|Higher risk of update anomalies|
|Better read throughput for reporting|More storage space required|

> 📌 **Rule of Thumb:** Always **normalize first** to remove anomalies. Only **denormalize deliberately and selectively** when measured performance requirements demand it — and only for read-heavy analytical systems, never for transactional systems where data integrity is paramount.


# **CHAPTER 3 — PRACTICE QUESTIONS**

_Relational Database Design_

30 Multiple Choice Questions  

**1.** **In relational database terminology, what is a 'tuple'?**

A.  A column that stores a specific type of data value

B.  The structural blueprint (schema) of a table

**C.  A single row/record — one complete set of values for one entity instance**

D.  The set of valid values that an attribute can hold

**✔ Answer: C**   _A tuple is a single row in a relation (table) — it represents one data instance. The formal equivalents are: Tuple = Row = Record. Column = Attribute = Field. The set of valid values = Domain._

**2.** **The 'degree' (arity) of a relation refers to:**

A.  The number of rows (tuples) currently in the table

**B.  The number of columns (attributes) in the table**

C.  The number of foreign keys in the table

D.  The number of tables in the entire database

**✔ Answer: B**   _Degree (Arity) = the number of COLUMNS (attributes) in a relation. Cardinality = the number of ROWS (tuples). These two are commonly confused in exams._

**3.** **Which SQL command is used to permanently delete BOTH a table's data AND its schema structure?**

A.  DELETE TABLE

B.  REMOVE TABLE

C.  TRUNCATE TABLE

**D.  DROP TABLE**

**✔ Answer: D**   _DROP TABLE removes the entire table including its structure (schema). DELETE (without WHERE) removes all rows but leaves the table structure intact. This is a frequently tested distinction._

**4.** **A database designer executes: DELETE FROM Employee; (without a WHERE clause). What is the result?**

A.  The Employee table and all its schema are permanently deleted

B.  Only the first row of the Employee table is deleted

**C.  All rows are removed but the table structure (schema) remains intact**

D.  A compile error — DELETE requires a WHERE clause

**✔ Answer: C**   _DELETE without WHERE removes ALL rows from the table but preserves the table structure. DROP TABLE would remove both the data and the structure. DELETE always requires caution without a WHERE clause._

**5.** **Which SQL command is used to add a new column to an existing table?**

A.  CREATE COLUMN

B.  INSERT COLUMN

**C.  ALTER TABLE ... ADD**

D.  UPDATE TABLE ... ADD COLUMN

**✔ Answer: C**   _ALTER TABLE is used to modify an existing table structure. ALTER TABLE tableName ADD columnName dataType; adds a new column. ALTER TABLE tableName DROP COLUMN columnName; removes a column._

**6.** **Which integrity constraint ensures that a specific column cannot contain NULL values AND no two rows can have the same value in that column?**

A.  UNIQUE constraint

B.  NOT NULL constraint

C.  FOREIGN KEY constraint

**D.  PRIMARY KEY constraint**

**✔ Answer: D**   _PRIMARY KEY enforces both uniqueness (no duplicates) AND non-nullability (no NULL values). UNIQUE enforces no duplicates but may allow NULLs. NOT NULL only prevents NULLs but allows duplicates._

**7.** **What does a FOREIGN KEY constraint enforce in a relational database?**

A.  That every value in a column falls within a specified data type range

B.  That the column values are unique across all rows in the table

**C.  That values in one table's column must exist as primary key values in a referenced table**

D.  That no column in the table can accept NULL values

**✔ Answer: C**   _A foreign key enforces referential integrity — the value in the referencing (child) table must exist as a primary key value in the referenced (parent) table. It prevents orphaned records._

**8.** **When a parent record is deleted and ON DELETE CASCADE is specified for the foreign key, what happens to the child records?**

A.  The deletion is rejected — child records prevent parent deletion

B.  Child records' foreign key values are set to NULL

C.  Child records' foreign key values are set to a default value

**D.  Child records are automatically deleted along with the parent**

**✔ Answer: D**   _ON DELETE CASCADE automatically deletes all child records in the referencing table when the parent record is deleted. This is used when child records have no meaning without their parent._

**9.** **What is the DEFAULT behavior in SQL when a DELETE or UPDATE violates a foreign key constraint (if no ON DELETE/UPDATE action is specified)?**

A.  CASCADE — automatically propagates the change to child records

B.  SET NULL — sets child foreign key values to NULL

**C.  NO ACTION — the operation is rejected**

D.  SET DEFAULT — sets child values to a default

**✔ Answer: C**   _NO ACTION is the default foreign key violation behavior — it rejects (rolls back) the offending transaction. The other behaviors (CASCADE, SET NULL, SET DEFAULT) must be explicitly specified._

**10.** **According to the ER-to-Relational mapping rules, how is a One-to-Many (1:M) relationship mapped?**

A.  Create a new junction table containing the primary keys of both entities

B.  Post the primary key from the 'Many' side into the 'One' side table as a foreign key

**C.  Post the primary key from the 'One' side into the 'Many' side table as a foreign key**

D.  Merge both entities into a single combined table

**✔ Answer: C**   _For 1:M relationships, the primary key from the 'One' side is placed as a foreign key in the 'Many' side table. Example: BRANCH (One) — EMPLOYEE (Many): BranchID is added as a FK to the EMPLOYEE table._

**11.** **How is a Many-to-Many (M:N) relationship correctly mapped to a relational schema?**

A.  Add a foreign key column to both tables referencing each other

**B.  Create a new junction (bridge) table containing the primary keys of both entities as foreign keys**

C.  Post the primary key of one entity into the other entity's table

D.  Store the related IDs as a comma-separated list in one column

**✔ Answer: B**   _M:N relationships require a new junction/associative table. This table contains the PKs of both entities as FKs. Together, these two FKs typically form the compound primary key of the junction table. Example: ENROLLMENT(StudentID, CourseID)._

**12.** **When mapping a composite attribute 'FullName' (composed of FirstName, MiddleName, LastName) to a relational table, what is the correct approach?**

A.  Create a new FULL_NAME table with its own primary key

B.  Store the entire FullName as one VARCHAR column

**C.  Ignore the 'FullName' parent — create FirstName, MiddleName, and LastName as separate columns**

D.  Create a multi-valued column storing all name parts together

**✔ Answer: C**   _Composite attributes are mapped by ignoring the parent attribute and creating individual columns for each sub-component. FullName disappears; FirstName, MiddleName, LastName become separate columns._

**13.** **A 'functional dependency' X → Y means:**

A.  X and Y must always have the same value in every row

B.  Y is always stored in the same column as X

**C.  Knowing the value of X uniquely determines the value of Y — any two rows with the same X must have the same Y**

D.  X is a foreign key that references Y in another table

**✔ Answer: C**   _X → Y (X functionally determines Y) means: if two tuples have the same value for X, they must also have the same value for Y. Knowing X is sufficient to uniquely identify Y's value._

**14.** **Which update anomaly occurs when deleting one piece of data accidentally destroys other valid, unrelated information?**

A.  Insert anomaly

**B.  Delete anomaly**

C.  Update anomaly

D.  Redundancy anomaly

**✔ Answer: B**   _A Delete Anomaly occurs when removing one record unintentionally eliminates other valid data. Classic example: deleting the only employee assigned to a project also wipes out all information about that project._

**15.** **A database cannot add a new project record because no employee has been assigned to it yet, even though the project details are fully known. This is an example of:**

A.  Delete anomaly

B.  Update anomaly

**C.  Insert anomaly**

D.  Referential integrity violation

**✔ Answer: C**   _An Insert Anomaly occurs when you cannot insert certain data because other, unrelated required data doesn't exist yet. The poor table design forces the two pieces of information to be stored together._

**16.** **A 'candidate key' is best defined as:**

A.  Any set of attributes that uniquely identifies a row, including unnecessary ones

**B.  A minimal superkey — a set of attributes that uniquely identifies a row with no unnecessary attributes**

C.  The primary key chosen by the designer from all available options

D.  A foreign key that references the primary key of another table

**✔ Answer: B**   _A candidate key is a MINIMAL superkey — every attribute in it is necessary for uniqueness (none can be removed without losing uniqueness). A superkey may contain extra attributes. The chosen candidate key becomes the primary key._

**17.** **A 'prime attribute' is defined as:**

A.  Any attribute that stores numeric (integer or decimal) values

B.  The attribute with the highest information content in the table

**C.  Any attribute that is a member of at least one candidate key**

D.  The first attribute listed in the table schema

**✔ Answer: C**   _A prime attribute is any attribute that participates in (is a member of) at least one candidate key. Non-prime attributes are those that are not part of any candidate key. This distinction is critical for 2NF and 3NF._

**18.** **What is Backus-Naur Form (BNF) used for in the context of database studies?**

A.  A normalization rule that removes partial dependencies from tables

B.  A type of ER diagram notation for showing class hierarchies

**C.  A formal grammar notation for describing valid syntax of SQL and schemas**

D.  A method for calculating the cardinality of database relationships

**✔ Answer: C**   _BNF (Backus-Naur Form) is a formal metalanguage for describing grammar rules. In databases, it defines the valid syntax of SQL statements and relation schemas. It is completely unrelated to BCNF (Boyce-Codd Normal Form)._

**19.** **In BNF notation, what does the symbol '::=' mean?**

A.  Is approximately equal to

B.  Is greater than or equal to

**C.  Is defined as**

D.  Is an alternative to

**✔ Answer: C**   _'::=' in BNF means 'is defined as.' It separates the left-hand non-terminal from its right-hand definition. The '|' symbol means 'or' (alternative). These are the two most important BNF symbols._

**20.** **First Normal Form (1NF) requires that:**

A.  Every non-prime attribute is fully dependent on the entire primary key

B.  No non-prime attribute is transitively dependent on the primary key

**C.  Every attribute value is atomic — single and indivisible, with no repeating groups**

D.  Every determinant in the table is a superkey

**✔ Answer: C**   _1NF requires all attribute values to be atomic (single, indivisible). No cell can hold a list, set, or repeating group. This is the most basic requirement — all other normal forms build on top of 1NF._

**21.** **A table has composite primary key (StudentID, CourseID). The attribute 'CourseName' depends only on CourseID, not on the full key. This violates:**

A.  First Normal Form (1NF) — non-atomic values

**B.  Second Normal Form (2NF) — partial dependency on part of the composite key**

C.  Third Normal Form (3NF) — transitive dependency through a non-key attribute

D.  BCNF — the determinant CourseID is not a superkey

**✔ Answer: B**   _2NF violation: CourseName depends on only CourseID (part of the composite key), not the full key (StudentID, CourseID). This is a partial dependency. Fix: move CourseName to a separate Course(CourseID, CourseName) table._

**22.** **A Student table contains: StudentID → DeptID and DeptID → DeptName. Storing DeptName in the Student table violates:**

A.  1NF — the value is not atomic

B.  2NF — DeptName partially depends on the key

**C.  3NF — DeptName is transitively dependent on StudentID through DeptID**

D.  4NF — there is a multivalued dependency

**✔ Answer: C**   _3NF violation: StudentID → DeptID (direct) and DeptID → DeptName (indirect). DeptName is transitively dependent on the primary key through the non-prime attribute DeptID. Fix: create a separate Department(DeptID, DeptName) table._

**23.** **Boyce-Codd Normal Form (BCNF) is STRICTER than 3NF. What additional requirement does BCNF add?**

A.  All multi-valued dependencies must be eliminated

B.  All attribute values must be atomic

**C.  For every non-trivial FD X → Y, X must be a superkey — no exceptions**

D.  No two candidate keys can share any attributes

**✔ Answer: C**   _BCNF requires that for EVERY non-trivial FD X → Y, X must be a superkey. 3NF allows exceptions when Y is a prime attribute. BCNF removes those exceptions, making it stricter. BCNF is always lossless but may not be dependency-preserving._

**24.** **A BCNF decomposition is guaranteed to be:**

A.  Dependency-preserving but may lose data on re-joining

B.  Both lossless AND dependency-preserving in all cases

**C.  Lossless, but may NOT preserve all functional dependencies**

D.  Neither lossless nor dependency-preserving

**✔ Answer: C**   _BCNF decompositions are always LOSSLESS (re-joining recovers the exact original data), but they are NOT always dependency-preserving (some FDs may need a join to verify). This is the key trade-off with BCNF._

**25.** **Fourth Normal Form (4NF) addresses which type of dependency that BCNF does not?**

A.  Partial functional dependencies on composite keys

B.  Transitive dependencies through non-prime attributes

**C.  Non-trivial multivalued dependencies (MVDs)**

D.  Join dependencies across three or more tables

**✔ Answer: C**   _4NF eliminates non-trivial multivalued dependencies (MVDs). A multivalued dependency X →→ Y means a set of Y values is associated with X independently of other attributes. BCNF does not address MVDs._

**26.** **A Course has many Textbooks AND many Teachers, and these two facts are completely independent of each other. To achieve 4NF, you should:**

A.  Keep all data in one table but add an index on CourseID

B.  Create a single Course table with Textbook and Teacher as multi-valued columns

**C.  Decompose into two separate tables: CourseTextbook(Course, Textbook) and CourseTeacher(Course, Teacher)**

D.  Merge Textbook and Teacher into a single 'Resource' attribute

**✔ Answer: C**   _Independent multivalued facts (Course →→ Textbook and Course →→ Teacher) must be decomposed into separate tables. Keeping them together creates redundant combinations of every textbook with every teacher for each course._

**27.** **What is the main difference between 'Normalization' and 'Denormalization'?**

A.  Normalization adds tables; denormalization removes all tables

**B.  Normalization reduces redundancy by decomposing tables; denormalization deliberately reintroduces redundancy to improve read performance**

C.  Normalization is used for reporting systems; denormalization is used for transactional systems

D.  Normalization and denormalization are two names for the same process

**✔ Answer: B**   _Normalization decomposes tables to eliminate redundancy and anomalies. Denormalization deliberately merges tables back together (accepting more redundancy) to reduce joins and improve read performance in analytics/reporting systems._

**28.** **Which of the following is a property that every good table decomposition during normalization must satisfy?**

A.  Every decomposed table must have exactly two columns

**B.  The decomposition must be lossless — re-joining the tables must recover the exact original data**

C.  Each decomposed table must be stored in a separate database file

D.  All decomposed tables must share the same primary key

**✔ Answer: B**   _A lossless join is the most critical requirement of normalization decomposition. When you rejoin the decomposed tables, you must get exactly the original data — no spurious (fake) rows and no missing rows._

**29.** **In the context of normalization, what are 'spurious tuples'?**

A.  Duplicate rows that violate the primary key constraint

B.  Rows with NULL values in non-nullable columns

**C.  False/extra rows generated when decomposed tables are rejoined due to a lossy decomposition**

D.  Rows that were deleted during the normalization process

**✔ Answer: C**   _Spurious tuples are invalid, artificially generated rows that appear when a lossy decomposition is rejoined. They represent data combinations that never existed in the original table — a sign that the decomposition was incorrect._

**30.** **BNF and BCNF — which statement about these two acronyms is CORRECT?**

A.  BNF (Boyce Normal Form) is an earlier, simpler version of BCNF (Boyce-Codd Normal Form)

**B.  BCNF (Boyce-Codd Normal Form) and BNF (Backus-Naur Form) are completely unrelated concepts from different fields**

C.  BNF stands for 'Basic Normal Form' and is equivalent to 1NF

D.  BNF and BCNF are two names used interchangeably for the same normalization rule

**✔ Answer: B**   _BNF = Backus-Naur Form (a grammar notation for describing language syntax — used in compilers and SQL parsers). BCNF = Boyce-Codd Normal Form (a database normalization rule stricter than 3NF). They are completely unrelated — do NOT confuse them._