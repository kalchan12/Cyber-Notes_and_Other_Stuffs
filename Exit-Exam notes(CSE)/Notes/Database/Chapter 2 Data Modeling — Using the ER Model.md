
## 2.1 What is Data Modeling and Why Does It Matter?

**Data modeling** is the very first step in the database design process. Before a single table is created or a single line of SQL is written, a designer must build a **model** — a structured, abstract representation of the real-world information the database will manage.

Think of data modeling the same way you think of architectural blueprints. No responsible engineer would begin constructing a building without a complete, reviewed blueprint. In the same way, no good database can be built without a well-thought-out data model.

### The Core Problem Data Modeling Solves

Different stakeholders view data in fundamentally different ways:

- **End-users** think about data in terms of business tasks and daily workflows
- **Programmers** think about data in terms of variables, structures, and logic
- **Database designers** think about data in terms of tables, keys, and relationships

If a database is built without first resolving and reconciling these different perspectives, the final product will inevitably fail to meet real user needs — causing costly redesigns later. The data model acts as the **common language** — a communication bridge — among all of these groups.

### What Makes a Good Data Model?

A good data model is:

- **Simple** — easy enough for non-technical stakeholders to understand
- **Graphical** — visual representations communicate structure far better than text alone
- **Abstract** — it captures the essential structure without getting lost in technical implementation details
- **Accurate** — it faithfully reflects the real-world rules and constraints of the organization

---

## 2.2 The Four Basic Building Blocks of Any Data Model

Every data model — regardless of its type or complexity — is built from four fundamental components. Understanding these building blocks is essential before attempting to draw any diagram.

### 1. Entity

An **entity** is anything about which data needs to be collected and stored. Entities are the "nouns" of the data world — they represent real-world objects, people, places, things, or events.

- Examples: `STUDENT`, `COURSE`, `EMPLOYEE`, `DEPARTMENT`, `ORDER`, `PRODUCT`
- In an ER diagram, entities are represented by **rectangles**

> 📌 **Note:** An entity represents a _category_ of things (e.g., all students), not a single specific instance. A specific student like "Abebe Girma" is called an **entity instance**.

### 2. Attribute

An **attribute** is a specific characteristic or property that describes an entity. Attributes hold the actual data values we want to store.

- Examples: A `STUDENT` entity might have attributes like `StudentID`, `Name`, `DateOfBirth`, `GPA`
- In an ER diagram, attributes are represented by **ovals**

### 3. Relationship

A **relationship** describes a meaningful association or connection between two or more entities. Relationships are the "verbs" of the data world.

- Examples: A `STUDENT` _enrolls in_ a `COURSE`; an `EMPLOYEE` _works for_ a `DEPARTMENT`
- In an ER diagram, relationships are represented by **diamonds**
- Relationships are **bidirectional** — they can be read in both directions

### 4. Constraint

A **constraint** is a rule or restriction placed on the data to ensure accuracy, validity, and compliance with business rules. Constraints prevent invalid data from being stored.

- Examples: A student's GPA must be between 0.0 and 4.0; an employee must belong to exactly one department
- Constraints are enforced by the DBMS automatically on every insert, update, and delete

---

## 2.3 Business Rules — The Foundation of Every Data Model

Before a designer can identify entities, attributes, or relationships, they must first thoroughly understand the **business rules** of the organization. You cannot model what you do not understand.

### What Are Business Rules?

**Business rules** are clear, concise, written descriptions of the policies, procedures, and principles that govern how an organization operates. They define what data exists, how it behaves, and what restrictions apply to it.

### Where Do Business Rules Come From?

Business rules are gathered from multiple sources:

- Interviews with company managers and end-users
- Existing policy documents and operations manuals
- Standard procedures and workflow descriptions
- Industry standards and regulatory requirements

### How to Translate Business Rules into Model Components

A useful rule of thumb for translation:

|Business Rule Element|Translates To|Example|
|---|---|---|
|**Nouns** (people, things, places)|**Entities**|"A _student_ enrolls in courses" → `STUDENT` entity|
|**Verbs** (actions, associations)|**Relationships**|"A student _enrolls in_ a course" → `ENROLLS_IN` relationship|
|**Adjectives / descriptors**|**Attributes**|"A student has a _name_, _ID_, _GPA_" → attributes|
|**Quantifiers / limits**|**Constraints**|"Each student must be enrolled in at least one course" → constraint|

> ⭐ **Exam Tip:** In an exam question that gives you a paragraph describing a business scenario, your first task is to identify the nouns (entities) and verbs (relationships). This is a very common exam exercise.

---

## 2.4 Levels of Data Abstraction

Data modeling manages complexity by allowing designers to view data at different levels of detail — from the big picture all the way down to physical storage. These are called **levels of abstraction**.

### The Three-Level Architecture

```
┌─────────────────────────────────────────────────┐
│          CONCEPTUAL MODEL (External View)        │
│   Global, DBMS-independent, hardware-independent │
│   "What data exists and how it relates"          │
└─────────────────────┬───────────────────────────┘
                      │  Logical Independence
┌─────────────────────▼───────────────────────────┐
│           INTERNAL MODEL (Logical View)          │
│   DBMS-specific view — how tables are organized  │
│   "How the DBMS sees the data"                   │
└─────────────────────┬───────────────────────────┘
                      │  Physical Independence
┌─────────────────────▼───────────────────────────┐
│          PHYSICAL MODEL (Storage View)           │
│   Describes exact storage on disks, tapes, etc.  │
│   "Where and how bytes are stored"               │
└─────────────────────────────────────────────────┘
```

### Detailed Comparison

|Level|Also Known As|What It Describes|Independent Of|
|---|---|---|---|
|**Conceptual**|External / Global model|The entire database as a whole — entities, relationships, constraints — in a DBMS-neutral way|Both DBMS software AND hardware|
|**Internal**|Logical model|How the chosen DBMS organizes the data — tables, indexes, views|Hardware (changing hardware does not affect this level)|
|**Physical**|Storage model|Exact physical storage — file formats, disk blocks, access paths|Higher levels — changing physical storage doesn't affect internal model|

### The Two Types of Independence

- **Logical Independence:** You can modify the internal (logical) model without changing the conceptual model. For example, reorganizing tables in the DBMS doesn't invalidate the ER diagram.
- **Physical Independence:** You can modify the physical storage (e.g., switching from HDD to SSD, changing file formats) without affecting the internal or conceptual models.

> ⭐ **Exam Tip:** This three-level architecture is sometimes called the **ANSI/SPARC three-schema architecture**. Physical independence is easier to achieve than logical independence.

---

## 2.5 The Entity-Relationship (ER) Model

The **Entity-Relationship (ER) Model** is by far the most widely used tool for conceptual data modeling. It was originally proposed by Peter Chen in 1976 and remains the industry standard today.

An ER model is expressed visually as an **ER Diagram (ERD)** using a small set of simple symbols:

|Symbol|Shape|Represents|
|---|---|---|
|Entity|Rectangle|A category of real-world objects|
|Relationship|Diamond|An association between entities|
|Attribute|Oval/Ellipse|A property of an entity|
|Connection|Line|Links entities to relationships and attributes|
|Primary Key|Underlined attribute oval|The uniquely identifying attribute|
|Weak Entity|Double rectangle|An entity that depends on another to exist|
|Identifying Relationship|Double diamond|The relationship connecting a weak entity to its owner|
|Multi-valued Attribute|Double oval|An attribute with multiple values|
|Derived Attribute|Dashed oval|An attribute calculated from other attributes|

---

## 2.6 Types of Attributes and Keys

Not all attributes are created equal. Understanding the different types of attributes — and the concept of keys — is critical for correct ER modeling.

### Attribute Types

#### Simple (Atomic) Attribute

A simple attribute holds a **single, indivisible value** — it cannot be broken down further into meaningful sub-parts.

- Examples: `SSN` (Social Security Number), `Sex`, `Age`, `StudentID`

#### Composite Attribute

A composite attribute is made up of **multiple smaller sub-attributes** that together form a meaningful whole. The full attribute is made up of parts.

- Example: `Address` is composed of `StreetNumber`, `StreetName`, `City`, `Region`, `PostalCode`
- In an ERD, composite attributes show sub-attributes branching off from the main oval

#### Multi-Valued Attribute

A multi-valued attribute is one where a **single entity instance can have more than one value** for that attribute simultaneously.

- Examples: A person's `CollegeDegrees` (someone might have a B.Sc. and an M.Sc.), a car's `Colors` (a car could be two-toned)
- Represented in an ERD with a **double oval**
- These require special handling when converting to tables (a new table is created)

#### Derived Attribute

A derived attribute's value is **calculated or derived from other stored attributes** rather than being stored directly.

- Example: `Age` can be derived from `DateOfBirth` and today's date; `TotalSales` can be derived from individual sale records
- Represented in an ERD with a **dashed oval**

### Keys

#### Key Attribute

An attribute (or a minimal combination of attributes) where **every entity instance in the set must have a unique value**. Keys are the foundation of data integrity.

#### Primary Key

The **single chosen key** that will be used to uniquely identify every record in the table. Choosing a good primary key is one of the most important design decisions.

Characteristics of a good primary key:

- **Unique** — no two records can have the same value
- **Non-null** — must always have a value; it can never be NULL
- **Stable** — should rarely or never change over time (e.g., a national ID is better than a person's name)
- **Minimal** — should use as few attributes as possible

> In an ERD, primary key attributes are indicated by **underlining** the attribute name inside the oval.

#### Candidate Key

Any attribute (or minimal set of attributes) that _could_ serve as the primary key. There may be multiple candidate keys; only one is chosen as the primary key. The rest are called **alternate keys**.

---

## 2.7 Relationships: Degree and Cardinality

### Degree of a Relationship

The **degree** refers to how many entity types participate in a relationship:

|Degree|Name|Description|Example|
|---|---|---|---|
|1|**Unary / Recursive**|An entity relates to itself|`EMPLOYEE` _supervises_ another `EMPLOYEE`|
|2|**Binary**|Two entities are associated|`STUDENT` _enrolls in_ `COURSE`|
|3|**Ternary**|Three entities are associated|`SUPPLIER` _supplies_ `PART` _to_ `PROJECT`|
|N|**N-ary**|N entities are associated|Any arbitrary number of entities|

> 📌 **Note:** Binary relationships are by far the most common in real-world database design. Ternary and N-ary relationships exist but are less frequent.

### Cardinality Constraints

**Cardinality** defines the **numerical limit** on how many instances of one entity can be associated with instances of another entity in a relationship. This is one of the most important constraints in database design because it directly shapes how the database is structured.

#### One-to-One (1:1)

Each instance of Entity A is associated with **at most one** instance of Entity B, and vice versa.

- Example: Each `EMPLOYEE` is assigned exactly one `PARKING_SPOT`, and each parking spot is assigned to exactly one employee
- Relatively rare in practice

#### One-to-Many (1:M)

Each instance of Entity A can be associated with **many** instances of Entity B, but each instance of Entity B is associated with **only one** instance of Entity A.

- Example: One `BRANCH` employs many `EMPLOYEES`, but each employee works at only one branch
- This is the **most common** cardinality in real databases

#### Many-to-Many (M:N)

Any instance of Entity A can be associated with **any number** of instances of Entity B, and vice versa.

- Example: A `STUDENT` can enroll in many `COURSES`, and a `COURSE` can be taken by many students
- Requires a special implementation (a new junction/bridge table) when converting to relational tables

|Cardinality|Notation|Real-World Example|
|---|---|---|
|One-to-One|1:1|Employee — Parking Spot|
|One-to-Many|1:M|Branch — Employees|
|Many-to-Many|M:N|Student — Course|

> ⭐ **Exam Tip:** Cardinality directly determines how relationships are mapped to tables. 1:M relationships use foreign keys; M:N relationships require an entirely new junction table. This is one of the most frequently examined topics in database courses.

---

## 2.8 Weak Entity Types

A **weak entity** is a special type of entity that cannot exist independently in the database — it depends on the existence of another entity (called its **owner** or **identifying entity**) to exist meaningfully.

### Characteristics of a Weak Entity

- It does **not have its own primary key**. It is instead identified by a combination of a **partial key** (attributes it owns) **plus the primary key of its identifying entity**
- It has **total participation** in its identifying relationship — every instance of the weak entity must be associated with an owner
- It participates in a **One-to-Many identifying relationship** (one owner can have many weak entities)
- In an ERD: represented by a **double rectangle** for the entity and a **double diamond** for the identifying relationship

### Example

Consider an `ORDER` entity and an `ORDER_LINE` entity:

- An `ORDER_LINE` cannot exist without an `ORDER` — if the order is deleted, all its lines cease to make sense
- `ORDER_LINE` has a partial key (e.g., `LineNumber` like 1, 2, 3...) that is only unique **within** a specific order
- Its full identifier is the combination: `OrderID` (from `ORDER`) + `LineNumber`

---

## 2.9 Extended ER Model — Class Hierarchies (Specialization & Generalization)

The extended ER model (EER model) adds concepts borrowed from Object-Oriented Programming to handle situations where entities naturally form hierarchies.

### The IS-A Relationship

Just like in OOP, some entities are special types of a more general entity. A `PART_TIME_EMPLOYEE` IS-A `EMPLOYEE`. This hierarchy is called a **class hierarchy** or **type hierarchy**.

### Specialization

**Specialization** is a **top-down** process. You start with a general entity and identify more specific subsets that have additional unique attributes.

- Example: Start with `EMPLOYEE`. Identify that some employees are `MANAGER` (with a `BonusRate`) and others are `TECHNICIAN` (with a `SkillSet`). You have _specialized_ the general entity into subclasses.

### Generalization

**Generalization** is a **bottom-up** process. You start with several specific entities and identify their common characteristics to create a broader, more general parent entity.

- Example: You have `CAR` and `TRUCK` entities. You notice both share `VehicleID`, `Make`, `Model`, `Year`. You _generalize_ these into a `VEHICLE` superclass.

### Inheritance

Just as in OOP, a **subclass inherits all attributes and relationships** of its parent superclass. The designer only needs to define the _additional_ attributes that are unique to each subclass.

- This prevents having to redefine common attributes (like `Name`, `ID`, `DateOfBirth`) in every single subclass table
- Subclasses can also participate in their own unique relationships that the superclass does not

|Concept|Direction|Starting Point|Result|
|---|---|---|---|
|**Specialization**|Top-down|General superclass|Specific subclasses with unique attributes|
|**Generalization**|Bottom-up|Specific entities|Broader superclass with shared attributes|
|**Inheritance**|Both|Parent to child|Child inherits all parent attributes/relationships|



# **CHAPTER 2 — PRACTICE QUESTIONS**

_Data Modeling — Using the ER Model_


**1.** **What is the PRIMARY purpose of data modeling in database design?**

A.  To write SQL queries for retrieving data from existing tables

**B.  To serve as a bridge between real-world objects and the database, resolving different stakeholder views**

C.  To optimize the physical storage of data on disk hardware

D.  To select the appropriate DBMS software for the project

**✔ Answer: B**   _Data modeling bridges the gap between real-world objects and the database. It resolves the different ways end-users, programmers, and designers view data, acting as a communication tool among all parties._

**2.** **Which of the following best describes a 'business rule' in the context of data modeling?**

A.  A SQL constraint written to enforce data integrity in a table

B.  A hardware specification that defines how data is physically stored

**C.  A written, easy-to-understand description of policies, procedures, or principles within an organization**

D.  A programming guideline for writing efficient database queries

**✔ Answer: C**   _Business rules are clear, concise descriptions of organizational policies and procedures. They are gathered from managers, manuals, and interviews, and form the foundation from which entities, relationships, and constraints are identified._

**3.** **When translating business rules into a data model, which element do NOUNS in a business rule typically translate into?**

A.  Relationships

B.  Constraints

C.  Attributes

**D.  Entities**

**✔ Answer: D**   _In data modeling, nouns (people, places, things, events) typically become Entities — the objects about which data is collected. Verbs typically become Relationships between those entities._

**4.** **Which of the following is NOT one of the four core building blocks of a data model?**

A.  Entity

B.  Attribute

**C.  Query**

D.  Constraint

**✔ Answer: C**   _The four fundamental building blocks of every data model are: Entity, Attribute, Relationship, and Constraint. A 'Query' is an operation performed on a completed database, not a building block of the model itself._

**5.** **A business rule states: 'A branch employs many employees, but each employee works at only one branch.' In a data model, the word 'employs' would translate to:**

A.  An entity named EMPLOYS

B.  An attribute of the BRANCH entity

**C.  A relationship between BRANCH and EMPLOYEE**

D.  A constraint on the EMPLOYEE table

**✔ Answer: C**   _Verbs in business rules translate to Relationships. 'Employs' describes the association between the BRANCH and EMPLOYEE entities, making it a relationship — specifically a One-to-Many relationship._

**6.** **Which level of data abstraction is completely independent of BOTH the DBMS software AND the hardware?**

A.  Physical Model

B.  Internal Model

**C.  Conceptual Model**

D.  Logical Model

**✔ Answer: C**   _The Conceptual Model (external/global level) is the highest level of abstraction. It describes what data exists and how it relates without any dependency on the specific DBMS software or the underlying hardware._

**7.** **The ability to change the physical storage medium (e.g., switching from HDD to SSD) without affecting the internal (logical) model is known as:**

A.  Logical independence

B.  Schema independence

**C.  Physical independence**

D.  Data transparency

**✔ Answer: C**   _Physical independence means changes to physical storage (hardware, file formats, access paths) do not affect the internal/logical model. Logical independence refers to changing the internal model without affecting the conceptual model._

**8.** **In an ER Diagram, which shape is used to represent a RELATIONSHIP between entities?**

A.  Rectangle

B.  Oval / Ellipse

**C.  Diamond**

D.  Double rectangle

**✔ Answer: C**   _In standard ER diagram notation: Rectangles = Entities, Diamonds = Relationships, Ovals = Attributes, Lines = Connections, Double rectangles = Weak entities, Double ovals = Multi-valued attributes._

**9.** **An 'Address' attribute composed of StreetNumber, City, Region, and PostalCode is an example of which attribute type?**

A.  Simple attribute

B.  Multi-valued attribute

C.  Derived attribute

**D.  Composite attribute**

**✔ Answer: D**   _A composite attribute is made up of multiple sub-components that together form a meaningful whole. 'Address' breaks down into smaller meaningful parts (StreetNumber, City, etc.), making it composite._

**10.** **A person's 'CollegeDegrees' attribute — where one person might hold a B.Sc., an M.Sc., AND a PhD — is an example of which attribute type?**

A.  Derived attribute

B.  Composite attribute

**C.  Multi-valued attribute**

D.  Key attribute

**✔ Answer: C**   _A multi-valued attribute allows a single entity instance to have MORE than one value for that attribute simultaneously. CollegeDegrees is multi-valued because one person can hold multiple degrees at the same time._

**11.** **An 'Age' attribute that is calculated from a person's 'DateOfBirth' and today's date is best described as a:**

A.  Key attribute

B.  Simple attribute

**C.  Derived attribute**

D.  Composite attribute

**✔ Answer: C**   _A derived attribute's value is computed from other stored data rather than being stored directly. Age is derived from DateOfBirth. In an ERD, derived attributes are shown with a dashed oval._

**12.** **Which of the following is NOT a required characteristic of a good Primary Key?**

A.  It must uniquely identify every record

B.  It must never contain a NULL value

C.  It should rarely or never change over time

**D.  It must consist of at least two attributes**

**✔ Answer: D**   _A good primary key must be unique, non-null, and stable. It should be minimal — using as few attributes as possible. A primary key can be a single attribute (like StudentID) and does NOT need to be composite._

**13.** **In an Entity-Relationship Diagram, a Primary Key attribute is distinguished from other attributes by:**

A.  Being placed inside a double oval

B.  Being placed inside a rectangle instead of an oval

**C.  Having its name underlined inside the oval**

D.  Being connected to the entity with a double line

**✔ Answer: C**   _In standard ER notation, the primary key attribute is shown as an underlined name inside the oval. Double ovals indicate multi-valued attributes, and double lines indicate total participation._

**14.** **A relationship where an EMPLOYEE entity is related to another EMPLOYEE entity (e.g., 'supervises') is called a:**

A.  Binary relationship

B.  Ternary relationship

C.  Hierarchical relationship

**D.  Unary (recursive) relationship**

**✔ Answer: D**   _A Unary (Recursive) relationship involves a single entity type that is related to itself. 'EMPLOYEE supervises EMPLOYEE' is a classic example — one employee manages other employees within the same entity set._

**15.** **Which degree of relationship involves exactly THREE entity types participating in a single relationship?**

A.  Unary

B.  Binary

**C.  Ternary**

D.  N-ary

**✔ Answer: C**   _A Ternary relationship involves exactly 3 entity types (e.g., SUPPLIER supplies PART to PROJECT). Unary=1 entity, Binary=2 entities, N-ary=any arbitrary number of entities._

**16.** **A BRANCH 'employs' many EMPLOYEES, but each EMPLOYEE works at only one BRANCH. What is the cardinality of this relationship?**

A.  One-to-One (1:1)

B.  Many-to-Many (M:N)

**C.  One-to-Many (1:M)**

D.  Many-to-One (M:1) from the Branch perspective

**✔ Answer: C**   _One-to-Many (1:M): one Branch is associated with many Employees, but each Employee is associated with only one Branch. This is the most common cardinality type in real-world databases._

**17.** **A STUDENT can enroll in many COURSES, and a COURSE can be taken by many STUDENTS. What is the cardinality of this relationship?**

A.  One-to-One (1:1)

B.  One-to-Many (1:M)

C.  Many-to-One (M:1)

**D.  Many-to-Many (M:N)**

**✔ Answer: D**   _Many-to-Many (M:N): any number of students can enroll in any number of courses. M:N relationships require a special junction/bridge table when converted to relational tables._

**18.** **What is the key characteristic of a WEAK ENTITY in an ER model?**

A.  It has more attributes than a regular entity

**B.  It cannot exist without the entity it depends on and has no primary key of its own**

C.  It participates in a Many-to-Many relationship only

D.  It is an entity that stores historical or archived data

**✔ Answer: B**   _A weak entity depends entirely on an owner/identifying entity for its existence. It has no primary key of its own — it is identified by a partial key combined with the primary key of its identifying entity._

**19.** **In an ER Diagram, how is a WEAK ENTITY visually represented?**

A.  A dashed rectangle

B.  A double oval

C.  A diamond shape

**D.  A double rectangle**

**✔ Answer: D**   _A weak entity is represented by a double rectangle (two concentric rectangles) in an ERD. Its identifying relationship is shown as a double diamond. Regular entities use a single rectangle._

**20.** **An ORDER_LINE entity cannot exist without a parent ORDER entity, and is identified by combining the OrderID with its own LineNumber. This is an example of:**

A.  A multi-valued attribute

B.  A ternary relationship

**C.  A weak entity with a partial key**

D.  A derived entity

**✔ Answer: C**   _ORDER_LINE is a weak entity — it depends on ORDER to exist. LineNumber alone is not unique across all orders (it repeats as 1, 2, 3 for each order). The combination of OrderID + LineNumber forms the full identifier._

**21.** **In the Extended ER (EER) Model, identifying common attributes among SAVING_ACCOUNT and CHECKING_ACCOUNT entities to create a parent ACCOUNT entity is called:**

A.  Specialization — top-down refinement of a general entity

**B.  Generalization — bottom-up abstraction of common features**

C.  Aggregation — combining entities into a single composite

D.  Normalization — removing redundant attributes

**✔ Answer: B**   _Generalization is a bottom-up process — you start with specific entities (SavingAccount, CheckingAccount), identify their shared attributes (AccountID, Balance, etc.), and create a broader parent entity (ACCOUNT). Specialization is the opposite (top-down)._

**22.** **In the EER Model, a PART_TIME_EMPLOYEE subclass inheriting all attributes from the EMPLOYEE superclass is an example of:**

A.  Generalization

B.  Aggregation

**C.  Inheritance**

D.  Encapsulation

**✔ Answer: C**   _Inheritance in the EER model means a subclass automatically acquires all attributes and relationships of its superclass. PART_TIME_EMPLOYEE inherits Name, ID, Salary, etc. from EMPLOYEE without redefining them._

**23.** **The EER Model concept of 'Specialization' is described as a _____ process.**

A.  Bottom-up — starting from specific subclasses to form a general superclass

**B.  Top-down — starting from a general superclass to identify specific subclasses**

C.  Lateral — connecting entities at the same level of the hierarchy

D.  Recursive — an entity relating back to itself

**✔ Answer: B**   _Specialization is top-down: you start with a general entity (e.g., EMPLOYEE) and identify specialized subsets with unique attributes (e.g., MANAGER, TECHNICIAN). Generalization is the opposite bottom-up process._

**24.** **Which of the following is the MOST important reason to build a data model before creating a database?**

A.  Data models automatically generate the SQL code for the database

B.  Data models eliminate the need for a DBMS entirely

**C.  Data models ensure all stakeholders share a common understanding of the data structure, preventing costly redesigns**

D.  Data models define the exact hardware configuration needed for the database server

**✔ Answer: C**   _The most critical purpose of data modeling is communication and shared understanding. Without it, the database will be built on different assumptions, leading to a system that fails to meet user needs and requires expensive redesigns._

**25.** **Which of the following relationships is an example of a One-to-One (1:1) cardinality?**

A.  A STUDENT enrolls in many COURSES

B.  A DEPARTMENT employs many EMPLOYEES

**C.  Each EMPLOYEE is assigned exactly one COMPANY_CAR, and each car belongs to one employee**

D.  A COURSE is taught by many INSTRUCTORS

**✔ Answer: C**   _One-to-One (1:1) means each instance of A is associated with at most one instance of B, and vice versa. One employee ↔ one company car is a 1:1 relationship. The other options are 1:M or M:N._

**26.** **A 'derived attribute' like 'TotalSalary' (calculated by summing individual salary components) is typically shown in an ERD with a:**

A.  Bold, solid oval

B.  Double oval (two concentric ovals)

**C.  Dashed (dotted) oval**

D.  Rectangle with rounded corners

**✔ Answer: C**   _Derived attributes are displayed using a dashed (dotted) oval in ER diagrams. They are usually NOT stored in the database but computed on demand. Multi-valued attributes use a double (solid) oval._

**27.** **The three-level data abstraction architecture is sometimes referred to as the:**

A.  ISO/IEC Three-Tier Architecture

**B.  ANSI/SPARC Three-Schema Architecture**

C.  Chen-Codd Three-Model Framework

D.  Client-Server-Database Architecture

**✔ Answer: B**   _The three-level abstraction (conceptual/internal/physical) is formally known as the ANSI/SPARC Three-Schema Architecture, proposed by the American National Standards Institute in the 1970s._

**28.** **Which of the following correctly describes the 'degree' of a relationship in the ER model?**

A.  The number of instances of one entity associated with another entity

B.  The number of attributes that make up the relationship

**C.  The number of entity types that participate in the relationship**

D.  The level of importance or priority assigned to a relationship

**✔ Answer: C**   _The Degree of a relationship refers to HOW MANY entity types participate in it (Unary=1, Binary=2, Ternary=3). Cardinality (not degree) refers to the number of instances of one entity associated with another._

**29.** **When converting a composite attribute like 'Address' (made of Street, City, PostalCode) to a relational table, what is the correct approach?**

A.  Create a separate table named 'Address' with its own primary key

B.  Store the entire address as a single text string in one column

**C.  Ignore the parent 'Address' attribute; make Street, City, and PostalCode individual columns**

D.  Create a multi-valued column that stores all address components together

**✔ Answer: C**   _When mapping a composite attribute to a relational schema, the composite parent (Address) is ignored. Only its sub-components (Street, City, PostalCode) become individual columns in the table._

**30.** **A STUDENT can have multiple phone numbers stored. When this multi-valued attribute is converted to a relational schema, the correct approach is to:**

A.  Add multiple PhoneNumber columns (Phone1, Phone2, Phone3) to the Student table

B.  Store all phone numbers as a comma-separated string in one column

**C.  Create a separate STUDENT_PHONE table containing StudentID (FK) and PhoneNumber**

D.  Convert it to a derived attribute calculated from the contact table

**✔ Answer: C**   _Multi-valued attributes must be mapped to a new separate table. This table contains the primary key of the original entity (as a foreign key) plus the multi-valued attribute. This preserves 1NF and avoids repeating groups._