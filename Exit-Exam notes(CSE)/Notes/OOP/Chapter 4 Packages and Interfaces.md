
## 4.1 What is an Interface?

An **interface** is a fully abstract contract that defines a set of method signatures and constants that implementing classes **must** provide. It specifies **what** a class must do, not **how** it does it.

java

```java
interface Drawable {
    int MAX_SIZE = 100;          // implicitly public static final
    void draw();                 // implicitly public abstract
}
```

### Interface vs. Abstract Class — Key Differences

|Feature|Interface|Abstract Class|
|---|---|---|
|Methods|All abstract (no bodies)|Can have both abstract and concrete methods|
|Variables|Constants only (`public static final`)|Any type of variable|
|Constructors|❌ None|✅ Can have constructors|
|Multiple inheritance|✅ A class can implement many|❌ A class can extend only one|
|When to use|When ALL features differ across classes|When SOME features are shared|

> ⭐ **Exam Tip:** Use an **abstract class** when there is shared code among related classes. Use an **interface** when unrelated classes need to agree on a common set of behaviors (a contract).

---

## 4.2 Why Use Interfaces?

|Reason|Explanation|
|---|---|
|**Enforce a contract**|Guarantees that every implementing class provides specific methods|
|**Achieve polymorphism**|Objects of different classes implementing the same interface can be treated uniformly|
|**Multiple inheritance**|A class can `implement` many interfaces, overcoming Java's single-class inheritance limit|
|**API design**|Lets you expose only the interface to external developers, hiding implementation details|

---

## 4.3 Interface Rules and Properties

java

```java
interface Vehicle {
    int MAX_SPEED = 200;              // public static final (implicitly)
    void accelerate();                // public abstract (implicitly)
    void brake();
}

class Car implements Vehicle {
    @Override
    public void accelerate() { System.out.println("Car accelerating"); }
    @Override
    public void brake() { System.out.println("Car braking"); }
}
```

### Complete Rules

- All methods are implicitly `public` and `abstract`
- All variables are implicitly `public`, `static`, and `final` (constants)
- ❌ Cannot be instantiated directly (`new Vehicle()` → error)
- ❌ Has no constructors
- A class using `implements` **must** provide bodies for **all** interface methods, or be declared `abstract`
- An interface can **extend** another interface (using `extends`)

---

## 4.4 Multiple Inheritance Through Interfaces

Java allows a class to implement **multiple interfaces**, achieving multiple inheritance of behavior without the Diamond Problem:

java

```java
interface Flyable {
    void fly();
}
interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {
    public void fly()  { System.out.println("Duck flying"); }
    public void swim() { System.out.println("Duck swimming"); }
}
```

> A class can only `extends` **one** class but can `implements` **many** interfaces.

---

## 4.5 Inner / Nested Classes

A **nested class** is a class defined inside another class. It groups logically related classes together, improving readability and encapsulation.

java

```java
class Outer {
    int x = 10;

    class Inner {                  // inner (non-static nested) class
        void display() {
            System.out.println("x = " + x);   // can access outer class members
        }
    }
}

// Accessing from outside:
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
inner.display();
```

> A **static nested class** does not need an outer class instance and is accessed as `Outer.StaticInner`.

---

## 4.6 Packages in Java

A **package** is a namespace that organizes related classes, interfaces, and sub-packages into a directory hierarchy — similar to folders on a file system.

### Benefits of Packages

|Benefit|Explanation|
|---|---|
|**Organization**|Groups related classes into logical units|
|**Namespace management**|Two classes can share the same name if in different packages|
|**Access control**|Works with access modifiers to control visibility|
|**Encapsulation**|Package-private classes are hidden from outside the package|

### Types of Packages

|Type|Description|Examples|
|---|---|---|
|**Pre-defined**|Built into the JDK|`java.lang`, `java.util`, `java.io`, `java.awt`|
|**User-defined**|Created by the programmer|`com.myapp.models`|
|**Third-party**|Provided by external vendors|`oracle.jdbc.driver.OracleDriver`|

> 📌 **Note:** `java.lang` is automatically imported into every Java program. You never need to import it explicitly.

---

## 4.7 Creating and Importing Packages

### Creating a Package

The `package` statement must be the **very first line** in the Java source file:

java

```java
package com.myapp.utilities;    // MUST be first line

public class MathHelper {
    // class body
}
```

> If the `package` statement is omitted, the class is placed in the unnamed **default package**.

### Importing a Package

java

```java
package com.myapp.main;           // package declaration (first)

import com.myapp.utilities.MathHelper;   // import specific class
import java.util.*;                       // import all classes in java.util

public class Main {
    // class body
}
```

**Import order in a Java file:**

1. `package` declaration (first)
2. `import` statements (second)
3. Class definition (last)

---

## 4.8 Access Protection — Complete Summary

|Modifier|Same Class|Same Package|Subclass (diff. package)|Anywhere|
|---|---|---|---|---|
|`private`|✅|❌|❌|❌|
|`default` (none)|✅|✅|❌|❌|
|`protected`|✅|✅|✅|❌|
|`public`|✅|✅|✅|✅|

> ⭐ **Exam Tip:** `protected` is the only modifier that allows access **outside the package but only through inheritance**. It **cannot** be applied to a top-level class.



# Chapter 4: Packages & Interfaces — 25 Questions 

---

### **1. What is an interface in Java?**

A) A class with full implementation  
B) A fully abstract contract  
C) A database structure  
D) A package type

**Answer: B**

**Explanation:**  
An interface defines _what a class must do_, not how it does it.

---

### **2. By default, interface methods are:**

A) private and static  
B) public and abstract  
C) protected and final  
D) static and final

**Answer: B**

**Explanation:**  
Interface methods are implicitly `public abstract`.

---

### **3. Interface variables are by default:**

A) private static  
B) public static final  
C) protected final  
D) abstract static

**Answer: B**

**Explanation:**  
All interface variables are constants.

---

### **4. Can an interface be instantiated?**

A) Yes  
B) No  
C) Only with abstract class  
D) Only in same package

**Answer: B**

**Explanation:**  
Interfaces cannot be instantiated directly.

---

### **5. Which keyword is used to implement an interface?**

A) extends  
B) inherit  
C) implements  
D) uses

**Answer: C**

**Explanation:**  
Classes use `implements` to follow an interface contract.

---

### **6. A class can implement how many interfaces?**

A) Only one  
B) Two  
C) Many  
D) None

**Answer: C**

**Explanation:**  
Java allows multiple interface inheritance.

---

### **7. A class can extend how many classes?**

A) One  
B) Two  
C) Many  
D) Unlimited

**Answer: A**

**Explanation:**  
Java supports single inheritance for classes only.

---

### **8. Why are interfaces useful?**

A) Faster execution  
B) Enforce contracts and polymorphism  
C) Memory saving  
D) File handling

**Answer: B**

**Explanation:**  
Interfaces ensure consistency across different implementations.

---

### **9. Which is TRUE about interfaces?**

A) They have constructors  
B) They can be instantiated  
C) They support multiple inheritance  
D) They have private methods only

**Answer: C**

**Explanation:**  
Interfaces allow multiple inheritance of behavior.

---

### **10. Which is NOT allowed in an interface?**

A) abstract methods  
B) constants  
C) constructors  
D) static final variables

**Answer: C**

**Explanation:**  
Interfaces cannot have constructors.

---

### **11. What does "implements" mean?**

A) Extending a class  
B) Following an interface contract  
C) Importing a package  
D) Creating a subclass

**Answer: B**

**Explanation:**  
It means the class must define interface methods.

---

### **12. What happens if a class does NOT implement all interface methods?**

A) Compiles normally  
B) Runtime error  
C) Must be abstract  
D) JVM ignores it

**Answer: C**

**Explanation:**  
The class must be declared abstract.

---

### **13. A package is used to:**

A) Run programs  
B) Organize classes  
C) Compile code  
D) Store memory

**Answer: B**

**Explanation:**  
Packages group related classes logically.

---

### **14. Which package is auto-imported?**

A) java.util  
B) java.io  
C) java.lang  
D) java.net

**Answer: C**

**Explanation:**  
`java.lang` is automatically imported in every Java program.

---

### **15. Package declaration must be:**

A) Last line  
B) First line  
C) Middle  
D) Optional always

**Answer: B**

**Explanation:**  
Package statement must be the first line in a file.

---

### **16. Correct import statement syntax is:**

A) include java.util  
B) import java.util.*  
C) using java.util  
D) package java.util

**Answer: B**

**Explanation:**  
`import` keyword is used to bring classes.

---

### **17. Default access modifier is:**

A) public  
B) private  
C) protected  
D) package-private

**Answer: D**

**Explanation:**  
No modifier = accessible only within same package.

---

### **18. Which modifier allows access in subclass across packages?**

A) private  
B) default  
C) protected  
D) static

**Answer: C**

**Explanation:**  
`protected` works across packages via inheritance.

---

### **19. A nested class is:**

A) A class inside a method  
B) A class inside another class  
C) A class in another file  
D) A package class

**Answer: B**

**Explanation:**  
Nested classes are defined inside another class.

---

### **20. Inner class can access:**

A) Only static members  
B) Only private methods  
C) Outer class members  
D) Nothing

**Answer: C**

**Explanation:**  
Inner class has full access to outer class members.

---

### **21. Static nested class:**

A) Needs outer object  
B) Cannot exist alone  
C) Does not need outer instance  
D) Cannot access anything

**Answer: C**

**Explanation:**  
Static nested class can be used without outer class object.

---

### **22. Which is TRUE about interface inheritance?**

A) Interfaces cannot inherit  
B) Interfaces can extend interfaces  
C) Interfaces extend classes  
D) Interfaces are final

**Answer: B**

**Explanation:**  
Interfaces can extend other interfaces.

---

### **23. Which is NOT a package type?**

A) Pre-defined  
B) User-defined  
C) Third-party  
D) Runtime-only

**Answer: D**

**Explanation:**  
Java has predefined, user-defined, and third-party packages.

---

### **24. Which keyword creates a package?**

A) package  
B) import  
C) include  
D) namespace

**Answer: A**

**Explanation:**  
`package` declares a namespace.

---

### **25. Which statement is TRUE?**

A) Interfaces have constructors  
B) Packages are optional always  
C) Interfaces support multiple inheritance  
D) Classes can extend multiple classes

**Answer: C**

**Explanation:**  
Interfaces allow multiple inheritance, unlike classes.

---
