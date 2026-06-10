
## 8.1 Introduction to Database Security and Threats

### Why Database Security Matters

Data is one of the most valuable assets an organization possesses. Unauthorized access, modification, or destruction of data can have devastating consequences:

- Military databases containing classified information could endanger lives if compromised
- Academic grade databases could be manipulated for fraud
- Financial records could be altered for theft or embezzlement
- Medical records could be accessed or modified with life-threatening results

A **threat** is any situation or event — intentional or accidental — that can adversely affect the system and the data it holds.

### The Three Core Security Goals

Every database security system is designed to protect against three types of loss:

|Security Goal|Description|Example Violation|
|---|---|---|
|**Integrity**|Only authorized users can modify data in authorized ways|A student changes their own grade in the academic database|
|**Availability**|Authorized users can access data they have a legal right to use, whenever they need it|A Denial-of-Service attack prevents faculty from accessing student records|
|**Confidentiality**|Information is not disclosed to unauthorized users|A student views another student's private grade records|

> 📌 **Note:** These three goals are sometimes called the **CIA Triad** (Confidentiality, Integrity, Availability) — the foundation of all information security, not just database security.

---

## 8.2 Authentication, Authorization, and Auditing

These three processes form the complete security pipeline for every database access request:

### Authentication

**Authentication** verifies that a user is who they claim to be. It answers the question: _"Are you really who you say you are?"_

- Implemented through username/password verification
- May include multi-factor authentication (MFA) in high-security environments
- Also verifies the user's **access level** (what category of user they are)

### Authorization (Privileges)

**Authorization** determines what an authenticated user is actually **permitted to do**. It answers: _"What are you allowed to do here?"_

Every database access request has exactly **three components** that the authorization system evaluates:

|Component|Question|Example|
|---|---|---|
|**Requested Operation**|What kind of action?|SELECT, INSERT, UPDATE, DELETE|
|**Requested Object**|On which data resource?|The `Grades` table, the `Employee` view|
|**Requesting User**|Who is asking?|User account `john_doe`, Role `Manager`|

The authorization system grants or denies access based on the privileges associated with the requesting user for the specific object and operation.

### Database Auditing (Audit Trails)

**Auditing** is a retrospective security measure — it records who did what and when, creating an **audit trail** for investigation after the fact.

- When tampering or unauthorized access is suspected, the **DBA performs an audit**
- This involves reviewing the **system log** to examine all operations performed by users during a specific time window
- Helps identify the source and nature of security breaches

> ⭐ **Exam Tip:** Authentication = verifying IDENTITY. Authorization = verifying PERMISSION. Auditing = reviewing ACCESS HISTORY. These three are distinct but complementary security mechanisms.

---

## 8.3 The Role of the Database Administrator (DBA)

The **DBA** is the central authority responsible for managing all aspects of the database, including its security. The DBA's security responsibilities include:

|Responsibility|Description|
|---|---|
|**Creating accounts**|Setting up user accounts with appropriate passwords|
|**Granting privileges**|Assigning specific permissions to users or roles|
|**Revoking privileges**|Removing permissions when no longer appropriate|
|**User and data classification**|Assigning security levels to users and data items according to organizational policy|
|**Security monitoring**|Reviewing audit logs and responding to security incidents|

---

## 8.4 Discretionary Access Control (DAC)

**DAC** is the most commonly used access control model in commercial database systems. It is based on the concept of an **owner** who controls access to their data by granting and revoking privileges to other users.

### The Access Matrix Model

DAC can be visualized as a two-dimensional matrix:

||Table: Students|Table: Grades|Table: Payroll|
|---|---|---|---|
|**User: Alice (DBA)**|Read, Write, Delete|Read, Write, Delete|Read, Write, Delete|
|**User: Bob (Faculty)**|Read|Read, Write|—|
|**User: Carol (Student)**|Read (own row only)|Read (own row only)|—|

- **Rows (Subjects):** Users or application accounts
- **Columns (Objects):** Tables, views, or other database resources
- **Intersections:** The specific privileges granted (Read/SELECT, INSERT, UPDATE, DELETE)

### Levels of Privileges

|Level|Scope|Examples|
|---|---|---|
|**Account Level**|Applies to the user's account, regardless of specific tables|CREATE TABLE, DROP TABLE, CREATE VIEW, CREATE INDEX|
|**Table Level**|Applies to a specific relation or view|SELECT, INSERT, UPDATE, DELETE on a named table|

### GRANT and REVOKE

sql

```sql
-- Grant SELECT and INSERT on Students table to user Bob
GRANT SELECT, INSERT ON Students TO Bob;

-- Grant SELECT with the ability to pass it on to others
GRANT SELECT ON Grades TO Bob WITH GRANT OPTION;

-- Revoke INSERT privilege from Bob
REVOKE INSERT ON Students FROM Bob;
```

**Key propagation rule:** If User A grants a privilege to User B, and B grants it to User C — when A revokes the privilege from B, the system **automatically revokes it from C as well** (cascading revocation).

### Views as a DAC Mechanism

One of the most powerful and elegant DAC tools is the **database view**:

sql

```sql
-- Create a view that only shows non-sensitive columns
CREATE VIEW StudentPublicInfo AS
    SELECT StudentID, Name, Major
    FROM Students;
    -- Note: GPA, Address, Phone are excluded

-- Grant access to the VIEW (not the base table)
GRANT SELECT ON StudentPublicInfo TO PublicUser;
```

A view acts as a **security filter** — the user gets access only to the specific rows and columns that the owner chooses to expose. The underlying base table with sensitive columns remains hidden.

### DAC Vulnerability

The main weakness of DAC is its susceptibility to **Trojan Horse attacks**. A malicious application can be disguised as a legitimate program and, when executed by a user with legitimate privileges, uses those privileges to steal or corrupt data — without the user's knowledge.

> ⭐ **Exam Tip:** The key features of DAC: owner-controlled, GRANT/REVOKE commands, cascading revocation, views as security mechanism, and vulnerability to Trojan horses.

---

## 8.5 Mandatory Access Control (MAC)

**MAC** is a far more rigid and strict security model than DAC. It is typically used in highly sensitive environments (military, government intelligence) where information leakage between security levels is absolutely unacceptable.

### Security Classification System

Both data objects and user subjects are assigned security labels in a strict hierarchy:

```
Top Secret (TS)  ←  highest classification
     ↑
   Secret (S)
     ↑
 Confidential (C)
     ↑
 Unclassified (U)  ←  lowest classification
```

- A **User/Subject** has a **Clearance level**
- A **Data Object** has a **Classification level**

### The Bell-LaPadula Model Rules

The Bell-LaPadula model enforces information flow using two strict rules:

|Rule|Name|Condition|Purpose|
|---|---|---|---|
|**Simple Security Rule**|"No Read Up"|A subject can READ an object only if: **Subject Clearance ≥ Object Classification**|Prevents low-clearance users from reading high-sensitivity data|
|**Star Property (★)**|"No Write Down"|A subject can WRITE to an object only if: **Subject Clearance ≤ Object Classification**|Prevents high-clearance users from leaking sensitive data into low-classification objects|

**The "No Write Down" rule explained:** A user with Top Secret clearance reading a TS document could accidentally (or maliciously) copy its contents into an Unclassified document if writing down were allowed. The ★ property prevents this information leakage.

```
Example:
- A "Secret" user CANNOT read a "Top Secret" document (clearance < classification)
- A "Top Secret" user CANNOT write to a "Confidential" document (clearance > classification)
- A "Secret" user CAN read a "Confidential" document (clearance ≥ classification)
- A "Secret" user CAN write to a "Secret" or "Top Secret" document (clearance ≤ classification)
```

### Polyinstantiation

MAC creates a unique problem: a low-clearance user might try to insert a record with the same primary key as an existing high-clearance record — and the system cannot tell them the record already exists (that itself would leak information). The solution is **polyinstantiation**:

- The system allows **multiple versions of the same record** (same primary key) to coexist at different classification levels
- Low-clearance users see and interact with the lower-classified version
- High-clearance users see the actual, higher-classified version
- Neither group is aware of the other's version

> ⭐ **Exam Tip:** The Bell-LaPadula rules seem counterintuitive at first. Remember: "No Read Up" (can't read higher than your clearance) and "No Write Down" (can't write lower than your clearance). The "No Write Down" rule is what many students get wrong.

---

## 8.6 Role-Based Access Control (RBAC)

**RBAC** is a modern, practical alternative to both DAC and MAC. Instead of assigning permissions to individual users directly, permissions are assigned to **roles**, and users are then assigned to roles.

### How RBAC Works

```
Permissions ──► Roles ──► Users

Example:
  SELECT on Grades ──► Role: "Faculty" ──► User: Bob
  SELECT on Grades ──► Role: "Faculty" ──► User: Alice
  UPDATE on Grades ──► Role: "Faculty" ──► User: Bob, Alice

  When Bob changes jobs: just change his role assignment
  → All his permissions update automatically
```

### RBAC vs. DAC vs. MAC

|Feature|DAC|MAC|RBAC|
|---|---|---|---|
|**Control basis**|Owner grants to individuals|System-enforced security labels|Roles assigned to users|
|**Flexibility**|High — owners decide|Low — rigid classification rules|High — easy role changes|
|**Security**|Moderate — Trojan horse vulnerable|Very high — enforces info flow|High — centrally managed|
|**Admin complexity**|High for many users|Very high — labeling everything|Low — manage roles, not users|
|**Typical use**|Commercial DBMS|Military/government|Enterprise business systems|

### Role Hierarchy

RBAC supports a **role hierarchy** that mirrors an organization's actual authority structure:

```
          ┌─────────────────┐
          │     MANAGER     │  ← inherits all Teller + Clerk privileges
          └────────┬────────┘
                   │
          ┌────────▼────────┐
          │     TELLER      │  ← inherits all Clerk privileges
          └────────┬────────┘
                   │
          ┌────────▼────────┐
          │      CLERK      │  ← base privileges
          └─────────────────┘
```

> 📌 **Note:** RBAC is considered the most practical and scalable access control model for real-world enterprise systems. When an employee changes jobs or leaves, the administrator simply changes their role assignment — all permissions update automatically.

---

## 8.7 Statistical Database Security

### The Problem

A **statistical database** is designed to answer aggregate queries (SUM, AVG, COUNT, MIN, MAX) about a population without revealing any individual's private data. However, a clever attacker can sometimes **infer individual values** from a combination of carefully crafted aggregate queries.

**Example attack:**

sql

```sql
-- Attacker knows only one employee earns over $200,000
Query 1: SELECT COUNT(*) FROM Employees WHERE salary > 200000;
→ Returns: 1

Query 2: SELECT SUM(salary) FROM Employees WHERE salary > 200000;
→ Returns: 250000

-- The attacker now knows exactly who earns $250,000!
```

This is called a **statistical inference attack** or **tracker attack**.

### Solutions to Statistical Database Security

|Solution|Description|
|---|---|
|**Disallow individual retrieval**|The DBMS must ensure no query can isolate or identify a single individual record|
|**Minimum population threshold**|Reject any query whose result is computed from fewer than N records (e.g., N=5). If the query population is too small, it's too easy to deduce individual values|
|**Query overlap restriction**|Detect and refuse sequences of queries that repeatedly target the same small population of records, which together could expose individual values|

---

## 8.8 Data Encryption and Digital Signatures

### Why Encryption?

Authorization controls protect data **within** the database system. But when data travels over a network (the internet, corporate networks), it passes through infrastructure that may be compromised. **Encryption** protects data in transit by converting it into an unreadable form.

|Term|Definition|
|---|---|
|**Plaintext**|The original, readable data|
|**Ciphertext**|The encrypted, unreadable version of the data|
|**Encryption**|Converting plaintext → ciphertext using a key and algorithm|
|**Decryption**|Converting ciphertext → plaintext using the correct key|

### Symmetric Encryption

|Feature|Detail|
|---|---|
|**How it works**|Uses the **same key** for both encryption and decryption|
|**Speed**|✅ Very fast — suitable for encrypting large amounts of data|
|**Key problem**|❌ The key must be securely shared between sender and receiver — this is the fundamental challenge|
|**Example algorithms**|AES (Advanced Encryption Standard), DES|

```
Sender:  Plaintext + Key → Ciphertext  ──► (over network) ──►
Receiver: Ciphertext + Same Key → Plaintext
```

### Asymmetric Encryption (Public Key Cryptography)

|Feature|Detail|
|---|---|
|**How it works**|Uses **two mathematically linked keys**: a public key (shared openly) and a private key (kept secret)|
|**Encryption**|Anything encrypted with the public key can ONLY be decrypted with the corresponding private key|
|**Speed**|❌ Slower than symmetric — used for key exchange, not bulk data|
|**Security**|✅ Very high — no need to share the private key|
|**Example algorithms**|RSA, ECC|

```
Sender encrypts with receiver's PUBLIC key
Receiver decrypts with their own PRIVATE key
(Only the receiver can decrypt — no key sharing problem)
```

### Digital Signatures

**Digital signatures** use asymmetric encryption to provide **authentication** — proving that a message genuinely came from a specific sender and has not been tampered with. This is essential in e-commerce and legal digital transactions.

**How a Digital Signature Works (Full Process):**

```
SENDER SIDE:
  1. Create the message (plaintext)
  2. Sign the message with sender's PRIVATE key  → Digital Signature
  3. Encrypt the signed message with receiver's PUBLIC key → Ciphertext
  4. Send the ciphertext

RECEIVER SIDE:
  5. Decrypt the ciphertext with receiver's own PRIVATE key → Signed message
  6. Verify the signature using sender's PUBLIC key → Confirms authenticity
```

|Digital Signature Provides|Explanation|
|---|---|
|**Authentication**|Proves the message came from the claimed sender (only they have their private key)|
|**Non-repudiation**|The sender cannot later deny sending the message|
|**Integrity**|Any tampering with the message during transit would invalidate the signature|

### Encryption Summary

|Method|Keys Used|Speed|Best For|Key Challenge|
|---|---|---|---|---|
|**Symmetric**|One shared key|Fast ✅|Encrypting large data volumes|Secure key distribution|
|**Asymmetric**|Public + Private key pair|Slow ❌|Authentication, key exchange, digital signatures|Computational overhead|
|**Digital Signature**|Sender's private + receiver's public|Moderate|Non-repudiation, e-commerce|Requires PKI infrastructure|

> ⭐ **Exam Tip:** For digital signatures: the sender signs with their own **PRIVATE** key and encrypts with the receiver's **PUBLIC** key. The receiver decrypts with their own **PRIVATE** key and verifies with the sender's **PUBLIC** key. This order matters — get it right in exams.


# Chapter 8 — Practice Questions

### Database Security

#### 25 Multiple Choice Questions · Correct answers marked with ✅

---

**1.** Which of the following BEST describes a "threat" in the context of database security?

- A) A software bug that causes incorrect query results
- B) An unauthorized SQL query submitted by a legitimate user
- C) A network connection timeout that prevents database access
- D) **Any situation or event — intentional or accidental — that can adversely affect the system and compromise security goals** ✅

> **Answer: D** — A threat is any event that endangers the CIA triad (Confidentiality, Integrity, Availability). Threats can be intentional (hacking, insider fraud) or accidental (misconfigurations, user errors) and can come from inside or outside the organization.

---

**2.** Which security goal is violated when an unauthorized student modifies their own grade in the academic database?

- A) Availability
- B) Confidentiality
- C) Authentication
- D) **Integrity** ✅

> **Answer: D** — Integrity means only authorized users can modify data in authorized ways. A student changing their grade is an unauthorized modification — the data is no longer trustworthy or accurate. Confidentiality would be violated if they viewed another student's grades.

---

**3.** A Denial-of-Service (DoS) attack prevents faculty members from accessing the student records they are legally authorized to view. Which security goal is violated?

- A) Integrity
- B) Confidentiality
- C) Authentication
- D) **Availability** ✅

> **Answer: D** — Availability means authorized users must be able to access data they have a right to use. A DoS attack denies access to legitimate users — it is an Availability violation. Confidentiality would be violated if unauthorized users COULD access the data.

---

**4.** A student accesses and reads another student's private grade records. Which security goal is violated?

- A) Integrity
- B) Availability
- C) Durability
- D) **Confidentiality** ✅

> **Answer: D** — Confidentiality means information must not be disclosed to unauthorized users. A student viewing another student's grades is unauthorized disclosure — a Confidentiality violation. Integrity would be violated if the student modified the grades.

---

**5.** What is the difference between AUTHENTICATION and AUTHORIZATION?

- A) Authentication controls what a user can do; authorization verifies who the user is
- B) Authentication is performed by the DBA; authorization is performed automatically by the DBMS
- C) Authentication applies to data objects; authorization applies to user accounts
- D) **Authentication verifies WHO a user is (identity); authorization determines WHAT they are allowed to do (permissions)** ✅

> **Answer: D** — Authentication = identity verification (username/password check). Authorization = permission check (is this user allowed to perform this operation on this object?). Both are required: you must first prove who you are, then the system determines what you can do.

---

**6.** Every database access request has three components that the authorization system evaluates. Which option lists all three correctly?

- A) Username, password, and IP address
- B) Table name, column name, and row count
- C) Transaction ID, timestamp, and isolation level
- D) **Requested Operation (what), Requested Object (on what), Requesting User (who)** ✅

> **Answer: D** — Authorization evaluates: (1) What operation is being requested (SELECT, INSERT, UPDATE, DELETE)? (2) On which database object (table, view, column)? (3) By which user or account? All three are necessary to make an access control decision.

---

**7.** What is a Database Audit Trail used for?

- A) Automatically optimizing slow queries by recording their execution plans
- B) Storing backup copies of all committed transactions for disaster recovery
- C) Tracking database performance metrics like query response time
- D) **Reviewing the system log to examine all accesses and operations performed by users during a specific time period — used when tampering is suspected** ✅

> **Answer: D** — An audit trail is a retrospective security tool. When unauthorized access or data tampering is suspected, the DBA reviews the audit log to see who accessed what data, when, and what operations were performed. It is forensic evidence for security investigations.

---

**8.** In Discretionary Access Control (DAC), who has the INITIAL full authority over a database table?

- A) The DBA — they must explicitly grant all privileges to every user
- B) The system administrator who installed the DBMS software
- C) The first user who performs a SELECT query on the table
- D) **The OWNER of the relation — they automatically have all privileges on objects they create** ✅

> **Answer: D** — In DAC, ownership determines initial authority. The user who creates a table (or other database object) automatically becomes its owner and has full privileges (SELECT, INSERT, UPDATE, DELETE, DROP). They can then grant subsets of these privileges to other users.

---

**9.** A user executes: `GRANT SELECT ON Grades TO Bob WITH GRANT OPTION;` What does the WITH GRANT OPTION allow Bob to do?

- A) Bob can grant himself UPDATE and DELETE privileges in addition to SELECT
- B) Bob can grant his SELECT privilege on ALL tables to other users
- C) Bob's SELECT privilege automatically expires after a set time period
- D) **Bob can further PASS the SELECT privilege on Grades to THIRD PARTIES** ✅

> **Answer: D** — WITH GRANT OPTION allows the recipient to propagate the privilege to others. Bob received SELECT on Grades AND the ability to grant that same SELECT privilege to additional users. Without this option, Bob could only use the privilege himself.

---

**10.** User A grants a privilege to User B, who then grants it to User C. If User A later revokes the privilege from User B, what happens to User C's privilege?

- A) User C keeps the privilege — revocation only affects the direct recipient
- B) User C keeps the privilege until the DBA explicitly revokes it separately
- C) User C's privilege is frozen — they can use it but cannot further grant it
- D) **User C's privilege is AUTOMATICALLY revoked — cascading revocation removes all downstream grants** ✅

> **Answer: D** — DAC uses cascading revocation. When a privilege is revoked from a user, it is automatically revoked from everyone that user granted it to, and everyone THEY granted it to, and so on. This maintains consistent access control without the DBA having to manually trace every grant chain.

---

**11.** A database VIEW is used as a security mechanism in DAC. How does it provide security?

- A) Views encrypt their data so unauthorized users cannot read the raw values
- B) Views require additional authentication before each access
- C) Views prevent any modification operations — they are read-only by definition
- D) **Views expose only a SAFE SUBSET of rows and columns from a base table, hiding sensitive data from users who should not see it** ✅

> **Answer: D** — A view acts as a filtered window into the base table. The owner creates a view containing only appropriate columns (excluding sensitive ones like salary or SSN) and grants access to the view. Users see only what the view exposes and have no direct access to the base table.

---

**12.** What is the main VULNERABILITY of Discretionary Access Control (DAC)?

- A) DAC cannot handle more than a few hundred users efficiently
- B) DAC does not support the REVOKE command — privileges are permanent once granted
- C) DAC requires all users to have the same privilege level
- D) **DAC is vulnerable to Trojan Horse attacks — malicious programs can exploit a legitimate user's granted privileges without their knowledge** ✅

> **Answer: D** — The fundamental weakness of DAC is that it only controls who has privileges, not how those privileges are used by applications. A Trojan horse embedded in an application runs with the executing user's privileges and can steal or corrupt data, even though the user is legitimate.

---

**13.** In Mandatory Access Control (MAC), a user/subject has a "Clearance" and a data object has a "Classification." In which environments is MAC typically used?

- A) Small businesses with simple access control needs
- B) Commercial e-commerce websites with customer accounts
- C) Educational institutions managing student grade records
- D) **Military and government environments where strict prevention of information leakage between security levels is critical** ✅

> **Answer: D** — MAC is designed for environments where information leakage between security levels is absolutely unacceptable — primarily military and intelligence agencies. Its rigid rules prevent even high-clearance users from leaking data to lower levels, making it far too restrictive for most commercial applications.

---

**14.** In the Bell-LaPadula Model, the "Simple Security Rule" (No Read Up) states:

- A) A subject can read an object only if Subject Clearance ≤ Object Classification
- B) A subject can read any object regardless of classification, but cannot write to higher-classified objects
- C) Subjects can only read objects within their exact clearance level — not higher or lower
- D) **A subject can read an object ONLY IF Subject Clearance ≥ Object Classification** ✅

> **Answer: D** — "No Read Up" means you can only read data at or BELOW your clearance level. A "Secret" user can read "Confidential" and "Unclassified" data but NOT "Top Secret" data. This prevents unauthorized disclosure of higher-sensitivity information.

---

**15.** In the Bell-LaPadula Model, the Star Property (★) — "No Write Down" — states:

- A) A subject can write to an object only if Subject Clearance ≥ Object Classification
- B) A subject can write to any object regardless of classification level
- C) Subjects can only write to objects below their clearance level
- D) **A subject can write to an object ONLY IF Subject Clearance ≤ Object Classification** ✅

> **Answer: D** — "No Write Down" prevents high-clearance users from leaking sensitive information into lower-classified objects. A "Top Secret" user cannot write to a "Confidential" document — otherwise they could copy TS-level secrets into a C-level document that lower-clearance users can read.

---

**16.** A user with "Secret" clearance tries to write to an "Unclassified" document. Under the Bell-LaPadula model, what happens?

- A) The write is permitted — Secret is higher than Unclassified, so the user has authority
- B) The write is permitted with a warning logged to the audit trail
- C) The write is permitted but the document is automatically reclassified to "Secret"
- D) **The write is DENIED — the Star Property (No Write Down) prevents writing to a LOWER classification level** ✅

> **Answer: D** — Secret clearance (level 2) > Unclassified classification (level 4 in hierarchy). Since Subject Clearance (2) > Object Classification (4 lowest), the condition Clearance ≤ Classification is violated → write is denied. This prevents the user from leaking Secret-level information into publicly readable Unclassified documents.

---

**17.** What problem does "Polyinstantiation" solve in Mandatory Access Control?

- A) It allows multiple users to simultaneously edit the same record without conflicts
- B) It provides multiple encryption keys for a single data record for added security
- C) It creates multiple backups of sensitive records across different storage locations
- D) **It allows multiple versions of the same record (same PK, different classification levels) to coexist — solving the problem that a low-clearance user cannot be told a high-clearance record exists** ✅

> **Answer: D** — Without polyinstantiation, a low-clearance user trying to insert a record with a PK that already exists as a high-clearance record would get a "duplicate key" error — which itself reveals that a high-clearance record exists. Polyinstantiation creates a separate low-clearance version, hiding the existence of the high-clearance one.

---

**18.** In Role-Based Access Control (RBAC), privileges are assigned to ROLES rather than directly to individual users. What is the PRIMARY advantage of this approach?

- A) Roles allow users to remain anonymous while still accessing the database
- B) Roles automatically encrypt all data accessed by users in sensitive positions
- C) Roles eliminate the need for the DBA to manage the database entirely
- D) **When a user changes jobs or leaves, only their role assignment needs to change — all permissions update automatically without manually reviewing individual grants** ✅

> **Answer: D** — RBAC's key advantage is administrative efficiency and simplicity. Instead of tracking individual user-privilege combinations (which becomes unmanageable at scale), administrators simply assign users to appropriate roles. Role changes automatically update all permissions.

---

**19.** Which access control model is considered the most PRACTICAL and SCALABLE for real-world enterprise business systems?

- A) DAC — because it gives data owners full flexibility to control access
- B) MAC — because its strict rules prevent all possible security breaches
- C) No access control — because modern encryption makes it unnecessary
- D) **RBAC — because it maps naturally to organizational job functions and scales efficiently as the organization grows** ✅

> **Answer: D** — RBAC is the dominant model in enterprise systems. It mirrors real organizational structures (Manager, Teller, Clerk), scales efficiently as users join/leave, and is easier to administer than DAC (which requires individual grant tracking) or MAC (which requires labeling everything).

---

**20.** In Statistical Database Security, an "inference attack" refers to:

- A) A hacker gaining direct SELECT access to a restricted table by exploiting a SQL injection vulnerability
- B) An attacker guessing a user's password by analyzing patterns in their login behavior
- C) A malicious program that infers the database schema by testing error messages
- D) **Using a sequence of aggregate (statistical) queries to mathematically deduce the exact value of a specific individual's private record** ✅

> **Answer: D** — An inference attack exploits the fact that aggregate queries can "leak" individual values when queries isolate a very small group. By combining multiple aggregate results cleverly, an attacker can calculate individual values that the database was designed to keep private.

---

**21.** Which solution to Statistical Database security prevents a query that would return results computed from fewer than 5 records?

- A) Disallowing all SELECT queries from non-DBA users
- B) Encrypting all statistical query results before returning them
- C) Requiring MAC clearance before any aggregate function can be executed
- D) **Enforcing a MINIMUM POPULATION THRESHOLD — rejecting queries whose result set contains fewer than N records** ✅

> **Answer: D** — A minimum population threshold (e.g., N=5) prevents queries that isolate very small groups. If your query's WHERE condition only matches 1 or 2 records, the DBMS refuses to return results — preventing inference of individual values from those tiny groups.

---

**22.** What is the FUNDAMENTAL PROBLEM with Symmetric Encryption?

- A) It is too slow to be used for encrypting large files or database records
- B) It produces ciphertext that is longer than the original plaintext
- C) It requires a different algorithm for every pair of communicating parties
- D) **The same key is used for both encryption and decryption — securely SHARING that key with the intended recipient without it being intercepted is a major challenge** ✅

> **Answer: D** — Symmetric encryption's core problem is key distribution. Both parties need the same secret key, but how do you securely send the key to someone without an attacker intercepting it? If the key is stolen, all communications are compromised. Asymmetric encryption solves this problem.

---

**23.** In Asymmetric (Public Key) Encryption, if Sender A wants to send an ENCRYPTED message to Receiver B that only B can read, which key does A use to encrypt?

- A) A's own private key
- B) A's own public key
- C) B's private key
- D) **B's PUBLIC key** ✅

> **Answer: D** — To send an encrypted message only B can read, A encrypts it with B's PUBLIC key (which is freely available). Only B's corresponding PRIVATE key (which only B possesses) can decrypt it. This solves the key distribution problem — no secret key ever needs to be shared.

---

**24.** In a Digital Signature system, a sender signs a message with their OWN PRIVATE key. What does this signing accomplish?

- A) It encrypts the message so the receiver is the only one who can read it
- B) It compresses the message for faster transmission over the network
- C) It generates a one-time password for the receiver to authenticate with
- D) **It provides proof of AUTHENTICITY and NON-REPUDIATION — only the sender has their private key, so only they could have created the signature** ✅

> **Answer: D** — Signing with your private key proves you created the message (authentication) and means you cannot later deny sending it (non-repudiation). Anyone can verify the signature using your public key. Note: this is NOT for confidentiality — it proves origin, not secrecy.

---

**25.** In the complete Digital Signature process, what is the CORRECT sequence of operations on the SENDER'S side?

- A) Encrypt with sender's private key → Sign with receiver's public key → Send
- B) Sign with receiver's public key → Encrypt with sender's private key → Send
- C) Encrypt with sender's public key → Sign with receiver's private key → Send
- D) **Sign the message with sender's OWN PRIVATE key → Encrypt the signed message with receiver's PUBLIC key → Send** ✅

> **Answer: D** — The correct sender sequence: (1) Sign with YOUR OWN private key (proves it came from you). (2) Encrypt with the RECEIVER'S public key (ensures only the receiver can decrypt it). On the receiver's side, they reverse it: decrypt with their own private key, then verify the signature using the sender's public key.

---
