
## 3.1 What is Inheritance?

Inheritance is a core OOP mechanism that allows a **new class** (child/subclass) to acquire the properties and behaviors of an **existing class** (parent/superclass). It promotes code reuse and models real-world hierarchical relationships.

> 🔑 **Key Rule:** Every class in Java implicitly inherits from `java.lang.Object` — the root superclass of all Java classes.

### Terminology

|Term|Synonym(s)|
|---|---|
|Existing class|Superclass · Base class · Parent class|
|New class|Subclass · Derived class · Child class|

### The IS-A Relationship

Inheritance should only be used when a genuine **IS-A** relationship exists:

- ✅ `Car` IS-A `Vehicle` → valid inheritance
- ✅ `Dog` IS-A `Animal` → valid inheritance
- ❌ `Engine` IS-A `Car` → wrong — this is a HAS-A (use aggregation instead)

### Benefits of Inheritance

|Benefit|Explanation|
|---|---|
|**Code Reusability**|Subclasses automatically inherit superclass fields and methods — no need to rewrite them|
|**Code Maintenance**|Hierarchical structure makes code easier to manage and update|
|**Reliability**|Reusing well-tested superclass code reduces the chance of bugs|
|**Real-World Modeling**|Maps naturally to how entities are organized in the real world|

---

## 3.2 Declaring Subclasses

The `extends` keyword establishes an inheritance relationship in Java.

java

```java
class SubclassName extends SuperclassName {
    // additional fields and methods
}
```

**Example:**

java

```java
class Animal {
    String name;
    void eat() { System.out.println("Eating..."); }
}

class Dog extends Animal {           // Dog IS-A Animal
    void bark() { System.out.println("Barking..."); }
}
```

> A `Dog` object can call both `bark()` (its own method) and `eat()` (inherited from `Animal`).

---

## 3.3 Types of Inheritance

|Type|Description|Java Support|
|---|---|---|
|**Single**|One subclass, one superclass|✅|
|**Hierarchical**|One superclass, many subclasses|✅|
|**Multi-Level**|A → B → C (chain of inheritance)|✅|
|**Multiple**|One class extends two or more classes|❌|
|**Hybrid / Multi-path**|Combination involving multiple class inheritance|❌|

> ⭐ **Exam Tip:** Java **does NOT support multiple class inheritance** to avoid the **Diamond Problem** — ambiguity when two parent classes have a method or variable with the same name. Java resolves this through **interfaces**, which support multiple inheritance of behavior.

---

## 3.4 The `protected` Modifier

The `protected` keyword fills the gap between `public` and `private` for inherited members:

|Modifier|Same Class|Same Package|Subclass (other package)|Anywhere|
|---|---|---|---|---|
|`private`|✅|❌|❌|❌|
|`default`|✅|✅|❌|❌|
|`protected`|✅|✅|✅|❌|
|`public`|✅|✅|✅|✅|

- `public` members are inherited but break encapsulation by exposing data to everyone.
- `private` members are fully encapsulated but **not inherited** by subclasses.
- `protected` is the ideal choice for superclass members that subclasses should access but external code should not.

---

## 3.5 The `super` Keyword

`super` is used inside a subclass to refer directly to its superclass. It has **three uses**:

### 1. Access a Hidden Superclass Variable

When a subclass declares a field with the same name as the superclass field:

java

```java
super.variableName = value;
```

### 2. Call a Superclass Constructor

Must be the **very first statement** inside the subclass constructor:

java

```java
super(arg1, arg2);   // calls matching superclass constructor
```

### 3. Call a Superclass Method

Useful when overriding a method but still needing the parent's logic:

java

```java
super.methodName();
```

> 📌 **Note:** If a subclass constructor does not explicitly call `super(...)`, Java automatically inserts `super()` (the no-arg superclass constructor call) as the first statement.

---

## 3.6 The `final` Keyword in Inheritance

The `final` keyword prevents modification or extension:

|Applied To|Effect|
|---|---|
|`final` **variable**|Value cannot be changed after initialization (constant)|
|`final` **method**|Cannot be overridden by any subclass|
|`final` **class**|Cannot be extended (subclassed). All its methods are implicitly final|

java

```java
final class ImmutableClass { }       // cannot be extended
// class Child extends ImmutableClass { }  ← compile error

class Parent {
    final void show() { }            // cannot be overridden
}
```

> ⭐ **Exam Tip:** A class **cannot be both `abstract` and `final`** — `abstract` requires a subclass to complete it, but `final` prevents subclassing. These two keywords directly contradict each other.

---

## 3.7 Aggregation — The HAS-A Relationship

**Aggregation** models a **HAS-A** (whole/part) relationship between two classes. Instead of inheriting, one class holds a **reference** to another class as a field.

- **Inheritance (IS-A):** `Dog extends Animal` → Dog IS-A Animal
- **Aggregation (HAS-A):** `Student` has an array of `Subject` objects → Student HAS-A Subject

java

```java
class Subject {
    String name;
}

class Student {
    String studentName;
    Subject[] subjects;    // Student HAS-A Subject (aggregation)
}
```

> Use **inheritance** for IS-A relationships and **aggregation** for HAS-A relationships. Choosing the wrong one is a common design mistake.

---

## 3.8 Polymorphism

Polymorphism (Greek: "many forms") allows the **same method name** to behave differently depending on the object or context. A **polymorphic reference** is a variable that can refer to objects of different types at different times.

### 3.8.1 Static Polymorphism — Method Overloading

|Feature|Detail|
|---|---|
|**When resolved**|Compile-time (static binding)|
|**Where it occurs**|Same class|
|**How it works**|Multiple methods with the same name but **different parameter lists** (number, type, or order)|
|**Purpose**|Perform a similar operation on different types of data|

java

```java
int add(int a, int b) { return a + b; }
double add(double a, double b) { return a + b; }   // overloaded
```

### 3.8.2 Dynamic Polymorphism — Method Overriding

|Feature|Detail|
|---|---|
|**When resolved**|Runtime (dynamic binding)|
|**Where it occurs**|Parent class and subclass|
|**How it works**|Subclass method has the **exact same name and signature** as a parent class method|
|**Purpose**|Define a specialized behavior for a subclass|

java

```java
class Animal {
    void sound() { System.out.println("Some sound"); }
}
class Dog extends Animal {
    @Override
    void sound() { System.out.println("Woof"); }  // overrides parent
}
```

### Overloading vs. Overriding — Quick Comparison

|Feature|Overloading|Overriding|
|---|---|---|
|Location|Same class|Parent & child class|
|Signature|**Different** parameters|**Identical** parameters|
|Binding|Compile-time (static)|Runtime (dynamic)|
|Return type|Can differ|Must be same (or covariant)|
|`@Override`|Not used|Recommended|

> ⭐ **Exam Tip:** This table is one of the most tested topics in OOP exit exams. Memorize every row.

---

## 3.9 Abstract Classes and Methods

### Definitions

|Term|Definition|
|---|---|
|**Concrete method**|A regular method with a full body/implementation|
|**Abstract method**|A method declared **without** a body — only a signature|
|**Abstract class**|A class declared with `abstract` that contains zero or more abstract methods|

java

```java
abstract class Shape {
    abstract double area();          // no body — subclass MUST implement
    void display() {                 // concrete method — subclass inherits it
        System.out.println("Shape");
    }
}

class Circle extends Shape {
    double radius;
    @Override
    double area() { return Math.PI * radius * radius; }  // must implement
}
```

### Rules for Abstract Classes

- ❌ Cannot be **instantiated** (`new Shape()` → compile error)
- ✅ Can have **both** abstract and concrete methods
- ✅ Can have **constructors**, fields, and static methods
- A subclass **must implement all abstract methods**, or itself be declared `abstract`
- ✅ An abstract class **reference** can point to a concrete subclass object:

java

```java
  Shape s = new Circle();   // valid — polymorphic reference
```

- ❌ Cannot be declared `final` (contradicts the need for subclassing)

> ⭐ **Exam Tip:** An abstract class with **zero** abstract methods is still valid — it simply cannot be instantiated directly.



#  Chapter 3: Inheritance — 25 Questions 

---

### **1. What is inheritance mainly used for in Java?**

A) Faster execution  
B) Code reuse and hierarchy modeling  
C) Memory allocation  
D) File handling

**Answer: B**

**Explanation:**  
Inheritance allows one class to reuse properties and methods of another class, reducing duplication and modeling real-world hierarchies like Animal → Dog.

---

### **2. Every class in Java implicitly extends which class?**

A) String  
B) Object  
C) System  
D) Class

**Answer: B**

**Explanation:**  
All Java classes automatically inherit from `java.lang.Object`, which is the root of all classes.

---

### **3. Inheritance represents which relationship?**

A) HAS-A  
B) IS-A  
C) USES-A  
D) PART-OF

**Answer: B**

**Explanation:**  
IS-A means a subclass is a type of superclass (Dog IS-A Animal).

---

### **4. Which is NOT a valid IS-A relationship?**

A) Dog IS-A Animal  
B) Car IS-A Vehicle  
C) Engine IS-A Car  
D) Student IS-A Person

**Answer: C**

**Explanation:**  
Engine is part of Car, not a type of Car → it’s HAS-A (aggregation).

---

### **5. Which keyword is used for inheritance in Java?**

A) inherit  
B) extends  
C) implements  
D) super

**Answer: B**

**Explanation:**  
`extends` creates an inheritance relationship between classes.

---

### **6. Main advantage of inheritance is:**

A) Faster execution  
B) Code reusability  
C) Memory saving  
D) Security improvement

**Answer: B**

**Explanation:**  
Inheritance avoids rewriting common code across multiple classes.

---

### **7. Which type of inheritance is NOT supported in Java classes?**

A) Single  
B) Multilevel  
C) Hierarchical  
D) Multiple

**Answer: D**

**Explanation:**  
Java avoids multiple inheritance with classes to prevent ambiguity (diamond problem).

---

### **8. Java does not support multiple inheritance because of:**

A) Security issues  
B) Diamond problem  
C) Performance issues  
D) Compilation error limits

**Answer: B**

**Explanation:**  
If two parents define the same method, Java cannot decide which one to use.

---

### **9. Which keyword refers to parent class members?**

A) this  
B) super  
C) base  
D) parent

**Answer: B**

**Explanation:**  
`super` is used to access parent class variables, methods, and constructors.

---

### **10. If a constructor does not call super(), what happens?**

A) Compile error  
B) JVM crashes  
C) Java inserts super() automatically  
D) Nothing happens

**Answer: C**

**Explanation:**  
Java automatically inserts a call to the no-argument constructor of the parent class.

---

### **11. Which statement about protected is TRUE?**

A) Only same class  
B) Only same package  
C) Accessible in subclasses even in different packages  
D) Not inherited

**Answer: C**

**Explanation:**  
`protected` allows access in subclasses across packages.

---

### **12. Most restrictive access modifier is:**

A) public  
B) protected  
C) default  
D) private

**Answer: D**

**Explanation:**  
`private` is only accessible inside the same class.

---

### **13. Which keyword prevents method overriding?**

A) static  
B) final  
C) abstract  
D) super

**Answer: B**

**Explanation:**  
A `final` method cannot be overridden by subclasses.

---

### **14. Which class cannot be inherited?**

A) abstract class  
B) final class  
C) public class  
D) interface

**Answer: B**

**Explanation:**  
Final classes cannot be extended.

---

### **15. Aggregation represents:**

A) IS-A relationship  
B) HAS-A relationship  
C) inheritance  
D) overriding

**Answer: B**

**Explanation:**  
One class contains another as a field (Student HAS-A Subject).

---

### **16. Which is an example of aggregation?**

A) Dog extends Animal  
B) Car extends Vehicle  
C) Student has Subjects  
D) Cat extends Mammal

**Answer: C**

**Explanation:**  
Subjects exist independently but are used inside Student.

---

### **17. Polymorphism means:**

A) One form  
B) Many forms  
C) No forms  
D) Hidden form

**Answer: B**

**Explanation:**  
Same method behaves differently depending on object type.

---

### **18. Method overloading is:**

A) Runtime polymorphism  
B) Compile-time polymorphism  
C) Inheritance  
D) Encapsulation

**Answer: B**

**Explanation:**  
Overloading is resolved during compilation based on parameters.

---

### **19. Method overriding occurs in:**

A) Same class  
B) Parent-child classes  
C) Interfaces only  
D) Constructors

**Answer: B**

**Explanation:**  
Subclass provides a new version of parent method.

---

### **20. Method overloading requires:**

A) Same parameters  
B) Different method names  
C) Different parameters  
D) Inheritance

**Answer: C**

**Explanation:**  
Same method name but different parameter list.

---

### **21. Method overriding requires:**

A) Different parameters  
B) Same signature  
C) Different return type always  
D) Same class only

**Answer: B**

**Explanation:**  
Method name + parameters must match exactly.

---

### **22. Method overriding is resolved at:**

A) Compile time  
B) Runtime  
C) Link time  
D) Build time

**Answer: B**

**Explanation:**  
JVM decides at runtime which method to call.

---

### **23. Method overloading is resolved at:**

A) Runtime  
B) Compile time  
C) Execution time  
D) Memory time

**Answer: B**

**Explanation:**  
Compiler selects correct method based on arguments.

---

### **24. Abstract method is:**

A) Method with body  
B) Method without body  
C) Static method  
D) Final method

**Answer: B**

**Explanation:**  
Only declaration exists; implementation is in subclass.

---

### **25. Which is TRUE about abstract classes?**

A) Cannot have methods  
B) Can be instantiated  
C) Can contain abstract and concrete methods  
D) Must be final

**Answer: C**

**Explanation:**  
Abstract classes can mix both implemented and unimplemented methods.

---

