
## 5.1 What is an Exception?

An **exception** is an abnormal event that disrupts the normal flow of a program during execution. Java's exception handling mechanism converts cryptic system error messages into meaningful, user-friendly responses — allowing the program to recover gracefully instead of crashing.

### Types of Errors in Java

|Error Type|When It Occurs|Detected By|Example|
|---|---|---|---|
|**Compile-time error**|Before the program runs|Java compiler|Missing semicolon, wrong syntax|
|**Run-time error**|During execution|JVM|Dividing by zero, null pointer|
|**Logic error**|During execution|Programmer|Wrong formula producing wrong result|

> 📌 **Note:** Exception handling deals specifically with **run-time errors** — the compiler catches compile-time errors before execution begins.

---

## 5.2 Types of Exceptions

### 5.2.1 Predefined (Built-in) Exceptions

Java's built-in exceptions fall under two major categories:

#### Asynchronous Exceptions — `java.lang.Error`

Hardware-level or JVM-level problems that are generally **unrecoverable**. Programs are not expected to handle these.

|Example|Cause|
|---|---|
|`OutOfMemoryError`|JVM runs out of heap memory|
|`StackOverflowError`|Infinite recursion exhausts the call stack|

#### Synchronous Exceptions — `java.lang.Exception`

Programmatic errors that **can and should** be handled. Divided into two subcategories:

|Type|When Checked|Must Be Handled?|Examples|
|---|---|---|---|
|**Checked Exception**|At compile-time|✅ Yes — compiler enforces it|`IOException`, `ClassNotFoundException`, `FileNotFoundException`|
|**Unchecked Exception**|At run-time|❌ Optional|`ArithmeticException`, `NullPointerException`, `ArrayIndexOutOfBoundsException`, `NumberFormatException`|

### 5.2.2 User-Defined (Custom) Exceptions

Exceptions explicitly created by the programmer to handle application-specific error conditions (covered in §5.7).

---

## 5.3 Exception Class Hierarchy

```
java.lang.Throwable
├── java.lang.Error                   ← Asynchronous, unrecoverable
│   ├── StackOverflowError
│   └── OutOfMemoryError
└── java.lang.Exception               ← Synchronous, recoverable
    ├── IOException                   ← Checked
    ├── ClassNotFoundException        ← Checked
    └── RuntimeException              ← Unchecked
        ├── ArithmeticException
        ├── NullPointerException
        ├── ArrayIndexOutOfBoundsException
        └── NumberFormatException
```

### Exception Message Format

When an unhandled exception occurs, the JVM prints a stack trace in this format:

```
ExceptionClassName: description of the error
    at ClassName.methodName(FileName.java:lineNumber)
```

> ⭐ **Exam Tip:** `Throwable` is the root of ALL exceptions and errors. `Error` and `Exception` are both direct subclasses of `Throwable`. `RuntimeException` is a subclass of `Exception` and represents all unchecked exceptions.

---

## 5.4 Exception Handling Keywords

Java uses **five keywords** for exception handling:

|Keyword|Purpose|
|---|---|
|`try`|Wraps the code that might throw an exception|
|`catch`|Handles the exception if one is thrown|
|`finally`|Executes cleanup code regardless of whether an exception occurred|
|`throw`|Explicitly throws an exception from within a method body|
|`throws`|Declares in a method signature that the method may throw an exception|

### 5.4.1 The `try` and `catch` Blocks

java

```java
try {
    // risky code — may throw an exception
    int result = 10 / 0;
} catch (ArithmeticException e) {
    // handle the exception — friendly message
    System.out.println("Error: Cannot divide by zero. " + e.getMessage());
}
```

**Key rules:**

- The `try` block must be immediately followed by at least one `catch` or a `finally` block
- If an exception is thrown, the JVM **immediately jumps** to the matching `catch` block
- Control **never returns** to the `try` block after jumping out
- The JVM passes an **exception object** to the `catch` block, which carries details about the error

### 5.4.2 The `finally` Block

java

```java
try {
    // open and use a file
} catch (IOException e) {
    System.out.println("File error: " + e.getMessage());
} finally {
    // ALWAYS runs — close the file here
    System.out.println("Closing resources...");
}
```

**Key rules:**

- `finally` **always executes** — whether or not an exception was thrown or caught
- Used to release resources: close files, database connections, network sockets
- Writing a `finally` block is **optional** but strongly recommended when using external resources
- Can contain nested `try-catch` blocks inside it

> ⭐ **Exam Tip:** `finally` runs even if there is a `return` statement inside the `try` or `catch` block. The only time `finally` does NOT run is if `System.exit()` is called.

---

## 5.5 Multiple and Nested Exceptions

### Multiple `catch` Blocks

A single `try` block can have multiple `catch` blocks to handle different exception types:

java

```java
try {
    int[] arr = new int[5];
    arr[10] = 100 / 0;           // two possible exceptions
} catch (ArithmeticException e) {
    System.out.println("Math error: " + e.getMessage());
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Array error: " + e.getMessage());
} catch (Exception e) {
    System.out.println("General error: " + e.getMessage());  // must be LAST
}
```

> ⭐ **Exam Tip — Ordering Rule:** `catch` blocks must be ordered from **most specific to most general**. If a general catch (e.g., `catch(Exception e)`) appears before a specific one (e.g., `catch(ArithmeticException e)`), the compiler throws an **unreachable code** error.

### Nested `try` Blocks

A `try` block inside another `try` block — used when different sections of code may throw different types of exceptions:

java

```java
try {
    // outer try — general operations
    try {
        // inner try — more specific risky operation
        int x = Integer.parseInt("abc");
    } catch (NumberFormatException e) {
        System.out.println("Inner: " + e.getMessage());
    }
} catch (Exception e) {
    System.out.println("Outer: " + e.getMessage());
}
```

---

## 5.6 The `throw` and `throws` Keywords

### `throw` — Explicitly Throwing an Exception

Used inside a method body to manually trigger an exception:

java

```java
void validateAge(int age) {
    if (age < 18) {
        throw new IllegalArgumentException("Age must be 18 or older.");
    }
}
```

### `throws` — Declaring an Exception in a Method Signature

Used to warn the **caller** that this method might throw a checked exception — making the caller responsible for handling it:

java

```java
void readFile(String path) throws IOException {
    // method body that may throw IOException
}
```

### `throw` vs. `throws` — Key Differences

| Feature                | `throw`                                              | `throws`                                    |
| ---------------------- | ---------------------------------------------------- | ------------------------------------------- |
| **Purpose**            | Explicitly throws an exception                       | Declares a potential exception              |
| **Location**           | Inside the method **body**                           | In the method **signature**                 |
| **Followed by**        | An exception **instance/object**                     | An exception **class name**                 |
| **Checked exceptions** | Cannot propagate alone — needs `throws` in signature | Propagates checked exceptions to the caller |
| **Multiple**           | Can only throw one at a time                         | Can declare multiple (comma-separated)      |

> 📌 **Note:** If a method body contains `throw new CheckedException(...)`, the method signature **must** declare `throws CheckedException` — otherwise it won't compile.

---

## 5.7 User-Defined (Custom) Exceptions

Programmers can create their own exception classes for domain-specific error conditions by following these steps:

1. Create a new class
2. Extend either `Exception` (for checked) or `RuntimeException` (for unchecked)
3. Add a parameterized constructor that accepts a `String` message
4. Call `super(message)` to pass the message to the parent `Exception` class

java

```java
// Define the custom exception
class InsufficientFundsException extends Exception {
    InsufficientFundsException(String message) {
        super(message);
    }
}

// Use the custom exception
class BankAccount {
    double balance = 500.0;

    void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException(
                "Withdrawal of " + amount + " exceeds balance of " + balance
            );
        }
        balance -= amount;
    }
}
```




# Chapter 5: Exception Handling — 25 Multiple Choice Questions

---

## 1. What is an exception in Java?

A. A syntax error detected by the compiler  
B. An abnormal event that disrupts normal program execution  
C. A package containing error-handling classes  
D. A method used to debug programs

**Answer:** B

**Explanation:**  
An exception is an abnormal event that occurs during program execution and interrupts the normal flow of a program.

---

## 2. Exception handling primarily deals with:

A. Compile-time errors  
B. Logic errors  
C. Run-time errors  
D. Syntax errors

**Answer:** C

**Explanation:**  
Exception handling is designed to handle run-time errors that occur while a program is executing.

---

## 3. Which type of error is detected by the Java compiler?

A. Run-time error  
B. Logic error  
C. Compile-time error  
D. Exception

**Answer:** C

**Explanation:**  
Compile-time errors such as syntax mistakes are detected before execution begins.

---

## 4. Which of the following is a logic error?

A. Missing semicolon  
B. NullPointerException  
C. Division by zero  
D. Using the wrong formula in a calculation

**Answer:** D

**Explanation:**  
Logic errors produce incorrect results even though the program runs successfully.

---

## 5. Which class represents unrecoverable JVM-level problems?

A. RuntimeException  
B. Throwable  
C. Error  
D. IOException

**Answer:** C

**Explanation:**  
`java.lang.Error` represents serious system-level problems that applications are generally not expected to handle.

---

## 6. Which of the following is an example of an Error?

A. IOException  
B. ArithmeticException  
C. OutOfMemoryError  
D. NumberFormatException

**Answer:** C

**Explanation:**  
`OutOfMemoryError` occurs when the JVM runs out of heap memory.

---

## 7. StackOverflowError is usually caused by:

A. Invalid file path  
B. Infinite recursion  
C. Division by zero  
D. Invalid array index

**Answer:** B

**Explanation:**  
Infinite recursive method calls eventually exhaust the call stack.

---

## 8. Checked exceptions are detected:

A. During execution only  
B. At compile time  
C. During installation  
D. During JVM startup

**Answer:** B

**Explanation:**  
The compiler checks checked exceptions and requires them to be handled or declared.

---

## 9. Which of the following is a checked exception?

A. ArithmeticException  
B. NullPointerException  
C. IOException  
D. NumberFormatException

**Answer:** C

**Explanation:**  
`IOException` is checked and must be handled or declared using `throws`.

---

## 10. Which of the following is an unchecked exception?

A. FileNotFoundException  
B. IOException  
C. ClassNotFoundException  
D. NullPointerException

**Answer:** D

**Explanation:**  
Unchecked exceptions belong to the `RuntimeException` family.

---

## 11. What is the root class of all exceptions and errors?

A. Object  
B. Exception  
C. RuntimeException  
D. Throwable

**Answer:** D

**Explanation:**  
`Throwable` is the parent class of both `Error` and `Exception`.

---

## 12. Which class is a direct subclass of Throwable?

A. Error  
B. RuntimeException  
C. IOException  
D. ArithmeticException

**Answer:** A

**Explanation:**  
`Throwable` has two major direct subclasses: `Error` and `Exception`.

---

## 13. Which keyword encloses code that may throw an exception?

A. throw  
B. throws  
C. try  
D. catch

**Answer:** C

**Explanation:**  
The `try` block contains code that might generate exceptions.

---

## 14. Which keyword handles an exception?

A. catch  
B. throw  
C. finally  
D. throws

**Answer:** A

**Explanation:**  
A `catch` block receives and handles exceptions thrown from the corresponding `try` block.

---

## 15. Which keyword always executes whether or not an exception occurs?

A. throw  
B. catch  
C. throws  
D. finally

**Answer:** D

**Explanation:**  
The `finally` block is used for cleanup operations and executes regardless of exceptions.

---

## 16. Which statement about finally is TRUE?

A. It runs only when an exception occurs  
B. It is mandatory after every try block  
C. It always executes except when System.exit() is called  
D. It cannot contain code

**Answer:** C

**Explanation:**  
`finally` runs in almost all circumstances except when the JVM terminates immediately using `System.exit()`.

---

## 17. After an exception occurs inside a try block, the JVM:

A. Restarts the try block  
B. Jumps to the matching catch block  
C. Ignores the exception  
D. Terminates immediately

**Answer:** B

**Explanation:**  
Control transfers directly to the appropriate catch block.

---

## 18. In multiple catch blocks, which catch must appear last?

A. ArithmeticException  
B. IOException  
C. RuntimeException  
D. Exception

**Answer:** D

**Explanation:**  
`Exception` is the most general exception type and must come after more specific exceptions.

---

## 19. What happens if catch(Exception e) appears before catch(ArithmeticException e)?

A. Program works normally  
B. Runtime error occurs  
C. Compiler reports unreachable code  
D. JVM ignores ArithmeticException

**Answer:** C

**Explanation:**  
The specific catch block becomes unreachable because the general catch already handles it.

---

## 20. A try block inside another try block is called:

A. Chained exception  
B. Recursive exception  
C. Nested try block  
D. Multiple exception

**Answer:** C

**Explanation:**  
Nested try blocks allow handling different exception types at different levels.

---

## 21. Which keyword is used to manually create and throw an exception?

A. throws  
B. catch  
C. try  
D. throw

**Answer:** D

**Explanation:**  
`throw` explicitly generates an exception object.

---

## 22. Which keyword declares that a method may throw an exception?

A. throw  
B. throws  
C. try  
D. catch

**Answer:** B

**Explanation:**  
`throws` informs the caller that the method may generate specified exceptions.

---

## 23. Which statement about throw is TRUE?

A. Used in method signature  
B. Followed by an exception object  
C. Declares multiple exceptions  
D. Handles exceptions

**Answer:** B

**Explanation:**  
`throw` must be followed by an exception instance such as `new IOException()`.

---

## 24. To create a custom checked exception, a class should extend:

A. Object  
B. Throwable  
C. Exception  
D. RuntimeException

**Answer:** C

**Explanation:**  
Custom checked exceptions are typically created by extending `Exception`.

---

## 25. Which statement about user-defined exceptions is TRUE?

A. They must extend either Exception or RuntimeException  
B. They cannot have constructors  
C. They are built into Java  
D. They cannot carry messages

**Answer:** A

**Explanation:**  
Custom exceptions are created by extending `Exception` (checked) or `RuntimeException` (unchecked), often with constructors that pass messages to `super()`.

---

## ⭐ High-Yield Exam Facts

- `Throwable` → Root of all errors and exceptions.
    
- `Error` → Unrecoverable JVM/system problems.
    
- `Exception` → Recoverable program-level problems.
    
- Checked exceptions → Compiler forces handling.
    
- Unchecked exceptions → Runtime exceptions.
    
- `try` → Risky code.
    
- `catch` → Handles exception.
    
- `finally` → Always executes.
    
- `throw` → Creates/throws exception.
    
- `throws` → Declares exception in method signature.
    
- Catch blocks must go from **specific → general**.
    
- `finally` executes even after `return`, except when `System.exit()` is called.
