
## 1.1 Data, Information, Database and DBMS

### Data

Data refers to raw, unprocessed facts and figures that by themselves have little or no meaning.

Data may consist of:

- Numbers
    
- Text
    
- Images
    
- Audio
    
- Video
    

Examples:

```text
3.8
Abebe
1001
```

These values alone do not provide meaningful information.

---

### Information

Information is processed and organized data that has meaning and context.

Example:

```text
Abebe has a GPA of 3.8
```

Here, the raw data becomes useful information because context has been added.

---

### Database

A database is an organized collection of logically related data that is stored electronically and designed to meet the information needs of an organization.

Unlike ordinary files, a database maintains:

- Relationships between data
    
- Integrity constraints
    
- Security rules
    
- Shared access
    

Examples:

- University Student Database
    
- Banking Database
    
- Hospital Database
    
- E-Commerce Database
    

---

### Database Management System (DBMS)

A Database Management System (DBMS) is software used to create, manage, store, retrieve, and control access to databases.

The DBMS acts as an intermediary between users/applications and the stored data.

Examples:

- MySQL
    
- PostgreSQL
    
- Oracle
    
- Microsoft SQL Server
    

---

### Database System

A Database System consists of:

```text
Database System = Database + DBMS
```

---

### Main Objectives of a DBMS

- Organize data efficiently
    
- Support multiple users
    
- Ensure data security
    
- Maintain consistency
    
- Provide fast retrieval
    
- Prevent unnecessary duplication
    

---

## Characteristics of a Database

|Property|Description|
|---|---|
|Persistent|Data remains stored even after programs terminate|
|Shared|Multiple users can access data simultaneously|
|Interrelated|Data items are logically connected|
|Self-Describing|Contains both data and metadata|

---

### Metadata

Metadata means:

```text
Data about Data
```

Examples:

- Table names
    
- Column names
    
- Data types
    
- Constraints
    

The collection of metadata is often called the:

```text
Data Dictionary
System Catalog
```

---

### Exam Notes

```text
Data = Raw facts

Information = Processed data

Database = Collection of related data

DBMS = Software used to manage databases

Metadata = Data about data
```

---

# 1.2 Evolution of Data Management

Data management evolved through three major stages.

---

## Manual Approach

The earliest method of data management.

Information was stored using:

- Paper records
    
- Filing cabinets
    
- Index cards
    

### Limitations

- Difficult to search
    
- Difficult to update
    
- High probability of human error
    
- Poor integration
    

---

## Traditional File-Based Approach

Each application maintained its own separate files.

Example:

```text
Payroll System → payroll.dat

Library System → library.dat

Registration System → students.dat
```

Each application controlled its own data.

### Problems

- Data redundancy
    
- Data inconsistency
    
- Poor data sharing
    
- Difficult maintenance
    

---

## Database Approach

The modern approach stores organizational data in a centralized database.

Applications share the same database.

Advantages include:

- Reduced redundancy
    
- Better security
    
- Improved consistency
    
- Easier maintenance
    

---

### Data Independence

One of the most important concepts in Database Systems.

Data Independence means:

> Changes in data structure should not require changes in application programs.

This is one of the biggest improvements of the database approach over file systems.

---

### Exam Notes

```text
Manual → Paper

File System → Separate Files

Database → Shared Centralized Data
```

---

# 1.3 Database vs File System

|Problem|File System|Database System|
|---|---|---|
|Data Redundancy|Same data stored repeatedly|Data shared and centralized|
|Data Inconsistency|Different copies may disagree|Consistency maintained|
|Data Isolation|Data scattered across files|Centralized access|
|Integrity|Difficult to enforce constraints|Constraints built into schema|
|Security|Limited access control|Fine-grained access control|
|Concurrency|Weak multi-user support|Transaction management supported|

---

## Data Redundancy

Occurs when the same data is stored multiple times.

Example:

```text
Student Name stored in
Registration File

Student Name stored in
Finance File

Student Name stored in
Library File
```

This wastes storage and creates maintenance problems.

---

## Data Inconsistency

Occurs when duplicate copies of data contain different values.

Example:

```text
Registration File:
Abebe

Library File:
Abebe K.
```

The database no longer represents a single truth.

---

### Exam Favorite

```text
Redundancy → Duplication

Inconsistency → Conflicting Data
```

---

# 1.4 Types of Databases

|Database Type|Description|Example|
|---|---|---|
|Relational|Tables consisting of rows and columns|MySQL, PostgreSQL|
|Document|Stores JSON-like documents|MongoDB|
|Key-Value|Stores key-value pairs|Redis|
|Column-Family|Stores data by columns|Cassandra|
|Graph|Stores nodes and relationships|Neo4j|
|Object-Oriented|Stores objects directly|ObjectDB|
|Distributed|Data stored across multiple locations|Distributed DBMS|
|Cloud Database|Hosted on cloud infrastructure|AWS RDS|

---

## Relational Database

The most important database model for exams.

Data is organized into:

```text
Tables (Relations)
Rows (Tuples)
Columns (Attributes)
```

Example:

|StudentID|Name|GPA|
|---|---|---|
|101|Abebe|3.8|
|102|Hana|3.5|

---

# 1.5 Components of a Database System

A database system consists of five major components.

|Component|Description|
|---|---|
|Hardware|Physical devices such as servers, disks, RAM|
|Software|DBMS, Operating System, Applications|
|Procedure|Rules and policies governing usage|
|Data|Actual stored data and metadata|
|People|Users who interact with the system|

---

## People in a Database System

|Role|Responsibility|
|---|---|
|DBA|Administration, security, backup, recovery|
|Designer|Creates database structures and schemas|
|Programmer|Develops database applications|
|End User|Uses the database|

---

### Exam Favorite

Question:

> What are the five components of a database system?

Answer:

```text
Hardware
Software
Procedure
Data
People
```

---

# Chapter 1: Introduction to DBMS – Practice Questions

### 1. What is data?

A. Processed information  
B. Raw facts and figures  
C. Organized records  
D. Database schema

**Answer: B**

**Explanation:** Data refers to raw, unprocessed facts that may not have meaning by themselves.

---

### 2. Information is:

A. Raw data  
B. Processed and meaningful data  
C. Metadata  
D. A database

**Answer: B**

---

### 3. Which of the following best defines a database?

A. A collection of programs  
B. A collection of related data organized for easy access and management  
C. A computer network  
D. An operating system

**Answer: B**

---

### 4. DBMS stands for:

A. Database Monitoring System  
B. Database Management System  
C. Data Backup Management System  
D. Distributed Business Management System

**Answer: B**

---

### 5. A database system consists of:

A. Database only  
B. DBMS only  
C. Database + DBMS  
D. Database + OS

**Answer: C**

---

### 6. Which database property means data remains available after a program terminates?

A. Shared  
B. Persistent  
C. Interrelated  
D. Self-describing

**Answer: B**

---

### 7. Which database property allows multiple users to access data simultaneously?

A. Persistent  
B. Interrelated  
C. Shared  
D. Self-describing

**Answer: C**

---

### 8. Metadata is:

A. Data stored in tables  
B. Data about data  
C. Backup data  
D. Temporary data

**Answer: B**

---

### 9. Which approach used filing cabinets and paper records?

A. Database Approach  
B. File-Based Approach  
C. Manual Approach  
D. Relational Approach

**Answer: C**

---

### 10. Which was a major problem of traditional file systems?

A. Too much security  
B. Data redundancy  
C. Excessive normalization  
D. Transaction control

**Answer: B**

---

### 11. Data redundancy means:

A. Missing data  
B. Duplicate copies of the same data  
C. Encrypted data  
D. Shared data

**Answer: B**

---

### 12. Data inconsistency occurs when:

A. Data is deleted  
B. Duplicate copies contain different values  
C. Data is encrypted  
D. Data is normalized

**Answer: B**

---

### 13. Which feature allows applications to remain unaffected by changes in data structure?

A. Data Mining  
B. Data Independence  
C. Data Replication  
D. Data Encryption

**Answer: B**

---

### 14. Which database model stores data in tables?

A. Graph Database  
B. Relational Database  
C. Object-Oriented Database  
D. Document Database

**Answer: B**

---

### 15. Which of the following is an example of a Relational DBMS?

A. MongoDB  
B. Redis  
C. PostgreSQL  
D. Neo4j

**Answer: C**

---

### 16. MongoDB is classified as a:

A. Relational Database  
B. Graph Database  
C. Document Database  
D. Object Database

**Answer: C**

---

### 17. Redis is an example of:

A. Key-Value Database  
B. Relational Database  
C. Graph Database  
D. Distributed Database

**Answer: A**

---

### 18. Which DBMS language is used to define database structure?

A. DML  
B. DCL  
C. DDL  
D. TCL

**Answer: C**

---

### 19. Which SQL statement belongs to DDL?

A. SELECT  
B. INSERT  
C. CREATE TABLE  
D. COMMIT

**Answer: C**

---

### 20. Which language is used to retrieve and modify data?

A. DDL  
B. DML  
C. DCL  
D. TCL

**Answer: B**

---

### 21. Which SQL command belongs to DML?

A. ALTER TABLE  
B. GRANT  
C. UPDATE  
D. COMMIT

**Answer: C**

---

### 22. Which language controls user permissions?

A. DDL  
B. DML  
C. DCL  
D. TCL

**Answer: C**

---

### 23. Which command belongs to DCL?

A. SELECT  
B. CREATE  
C. GRANT  
D. ROLLBACK

**Answer: C**

---

### 24. Which command is part of TCL?

A. INSERT  
B. COMMIT  
C. ALTER  
D. GRANT

**Answer: B**

---

### 25. Which of the following is NOT one of the five major components of a database system?

A. Hardware  
B. Software  
C. Data  
D. Compiler

**Answer: D**

**Explanation:** The five components are Hardware, Software, Procedure, Data, and People.