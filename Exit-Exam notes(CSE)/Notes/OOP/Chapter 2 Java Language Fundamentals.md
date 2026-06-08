
## **2.1 Identifiers and Keywords**

**Identifier:** A name given to a program element such as a variable, method, class, object, or array.

Rules for naming identifiers in Java:

Can contain letters, digits, underscores (_), and dollar signs ($).

Must begin with a letter, underscore (_), or dollar sign ($) — NOT a digit.

Cannot contain spaces or special characters (!, @, #, %, etc.).

Are case-sensitive — myVar and myvar are different identifiers.

Cannot be a Java keyword (reserved word).

**Keyword:** A reserved word with a predefined meaning in Java. Cannot be used as an identifier.

Common Java keywords: public, private, protected, static, void, class, interface, abstract, final, extends, implements, new, this, super, return, if, else, while, for, switch, try, catch, throw, throws, import, package.

_⭐ Exam Tip: 'goto' and 'const' are reserved keywords in Java but are NOT currently used. They are still illegal as identifiers._

## **2.2 Variables and Data Types**

A variable is a named memory location that stores a data value during program execution.

Declaration syntax:   dataType  variableName = value;

### **2.2.1 Kinds of Variables**

|   |   |
|---|---|
|**Instance Variables (Non-Static Fields)**|Declared inside a class, outside methods. Each object has its own copy. Default values are assigned automatically.|
|**Class Variables (Static Fields)**|Declared with the static keyword. One shared copy per class regardless of how many objects exist. Initialized once.|
|**Local Variables**|Declared inside a method or block. Only accessible within that block. NOT given default values — must be initialized before use.|
|**Parameters**|Variables in a method signature used to receive arguments passed into the method.|

### **2.2.2 Primitive Data Types**

|   |   |
|---|---|
|**Type**|**Description**|
|**boolean**|1-bit. Stores true or false.|
|**byte**|8-bit integer. Range: -128 to 127.|
|**short**|16-bit integer. Range: -32,768 to 32,767.|
|**int**|32-bit integer. Most common integer type. Range: ~-2.1B to 2.1B.|
|**long**|64-bit integer. Suffix L required (e.g., 100L).|
|**float**|32-bit floating-point. Suffix f required (e.g., 3.14f). ~7 decimal digits precision.|
|**double**|64-bit floating-point. Default for decimals. ~15 decimal digits precision.|
|**char**|16-bit Unicode character. Single quotes (e.g., 'A'). Range: 0 to 65,535.|

Reference Data Types store memory addresses pointing to objects in the heap: Object, String, Array, Class, Interface, Exception.

_��� Note: Local variables are NOT automatically initialized — the compiler will throw an error if you try to use an uninitialized local variable. Instance and class variables ARE given default values (0, false, null)._

## **2.3 Literals and Escape Sequences**

|   |   |
|---|---|
|**Literal Type**|**Example**|
|**Boolean**|true, false|
|**Integer (decimal)**|26, -100|
|**Integer (hex)**|0x1A (prefix 0x)|
|**Integer (binary)**|0b11010 (prefix 0b, since Java 7)|
|**Integer (octal)**|032 (prefix 0)|
|**Floating-point**|123.4, 1.234e2 (scientific notation)|
|**Character**|'A', '\n' (single quotes)|
|**String**|"Hello World" (double quotes)|
|**null**|null — reference types not pointing to any object|

Common escape sequences:

|   |   |
|---|---|
|**Escape Sequence**|**Meaning**|
|**\n**|Newline — moves to next line|
|**\t**|Tab — inserts horizontal tab|
|**\\**|Backslash — literal \|
|**\"**|Double quote — literal "|
|**\'**|Single quote — literal '|
|**\b**|Backspace|
|**\r**|Carriage return|

## **2.4 Comments**

|   |   |
|---|---|
|**Type**|**Syntax & Use**|
|**Single-line**|// This is a comment — from // to end of line|
|**Multi-line**|/* This spans multiple lines */|
|**Documentation**|/** Used by Javadoc tool to auto-generate HTML API docs */|

_��� Note: Comments are completely ignored by the Java compiler. They have NO effect on program execution._

## **2.5 Operators and Expressions**

|   |   |
|---|---|
|**Operator Type**|**Symbols & Notes**|
|**Arithmetic**|+  -  *  /  %  (modulo/remainder)|
|**Unary**|+  -  ++  --  !  (logical NOT)|
|**Relational**|==  !=  >  >=  <  <=|
|**Logical**|&&  (AND)     \|  (OR)|
|**Ternary (Conditional)**|condition ? valueIfTrue : valueIfFalse|
|**Assignment**|=  +=  -=  *=  /=  %=|
|**Bitwise**|&  \|  ^  ~  <<  >>  >>>|
|**Type Comparison**|instanceof — checks if object is instance of a class|

_⭐ Exam Tip: The ternary operator (?:) is a common exam question. It is a compact replacement for if-else: int max = (a > b) ? a : b;_

## **2.6 Control Flow Structures**

### **2.6.1 Conditional Statements**

|   |   |
|---|---|
|**Statement**|**Behavior**|
|**if / else if / else**|Evaluates boolean conditions in sequence. Executes the first block whose condition is true.|
|**switch**|Multi-way branch based on a variable's value. Supports byte, short, char, int, String, Enum. Uses break to prevent fall-through.|

### **2.6.2 Loops**

|   |   |
|---|---|
|**Loop**|**Behavior**|
|**while**|Pre-condition loop. Checks condition BEFORE executing body. May never execute.|
|**do-while**|Post-condition loop. Checks condition AFTER executing body. Guaranteed to execute at LEAST once.|
|**for**|Compact loop: initialization; condition; increment/decrement. Best for known iteration counts.|
|**for-each (enhanced for)**|Iterates over arrays and collections: for (Type var : collection). Read-only iteration.|

### **2.6.3 Jump Statements**

|   |   |
|---|---|
|**Statement**|**Effect**|
|**break**|Immediately terminates the nearest enclosing loop or switch statement.|
|**continue**|Skips the rest of the current loop iteration and moves to the next iteration.|
|**return**|Exits the current method and optionally returns a value to the caller.|

_⭐ Exam Tip: do-while is the ONLY loop that guarantees at least one execution. This is a frequently tested distinction._

## **2.7 Arrays**

An array is a fixed-size, ordered collection of elements of the same data type stored in contiguous memory locations.

**Declaration:** int[] arr = new int[5];   or   int[] arr = {1, 2, 3, 4, 5};

**Indexing:** Zero-based (first element is arr[0], last is arr[arr.length - 1]).

**Length:** arr.length gives the number of elements (note: no parentheses — it is a field, not a method).

**Multi-dimensional:** int[][] matrix = new int[3][4]; — a 3-row, 4-column 2D array.

Arrays are objects — created on the heap with the new keyword.

Useful Arrays class methods (java.util.Arrays):

|   |   |
|---|---|
|**Method**|**Description**|
|**Arrays.sort(arr)**|Sorts the array in ascending order.|
|**Arrays.binarySearch(arr, key)**|Searches a sorted array using binary search. Returns index or negative value.|
|**Arrays.equals(arr1, arr2)**|Compares two arrays element by element. Returns true if equal.|
|**Arrays.fill(arr, value)**|Fills all elements with the specified value.|
|**Arrays.copyOf(arr, newLength)**|Creates a copy of the array with a specified length.|

_��� Note: Arrays have a FIXED size once created. For a resizable array-like structure, use ArrayList from java.util._

## **2.8 Classes and Objects**

**Class:** A blueprint/template that defines the attributes (fields) and behaviors (methods) that its objects will have. Declared with the class keyword.

**Object:** An instance of a class. Each object has its own state (data) and shares behaviors (methods) defined by its class.

**Creating an object:** ClassName varName = new ClassName();

**Accessing members:** Use the dot operator (.)  e.g.,  myCar.color = "Red";  myCar.start();

**Memory:** Objects are created in the heap. The variable stores a reference (address) to the object.

## **2.9 Access Modifiers**

Access modifiers control the visibility (accessibility) of classes, methods, and fields.

|   |   |
|---|---|
|**Modifier**|**Accessibility**|
|**public**|Accessible from any class, any package. Widest scope.|
|**protected**|Accessible within the same package AND by subclasses in other packages.|
|**default (package-private)**|No keyword used. Accessible only within the same package.|
|**private**|Accessible only within the same class. Strictest scope.|

_⭐ Exam Tip: Scope order from widest to strictest: public > protected > default > private. Memorize this — it is tested constantly._

## **2.10 Methods and Method Overloading**

**Method:** A named block of code that performs a specific task. Must be declared inside a class.

Syntax:   accessModifier  returnType  methodName(parameterList)  {  body  }

**Method Overloading:** Defining multiple methods with the same name but different parameter lists (number, type, or order of parameters) within the same class. The return type alone is NOT sufficient to distinguish overloaded methods.

_��� Note: Method Overloading is a form of compile-time (static) polymorphism. The compiler determines which version to call based on the arguments provided._

## **2.11 The 'this' Keyword**

this refers to the current object instance within an instance method or constructor. Common uses:

|   |   |
|---|---|
|**Use**|**Example / Explanation**|
|**Disambiguate fields vs. parameters**|this.name = name; — when parameter and field share the same name.|
|**Call another constructor**|this(10); — must be the FIRST statement in a constructor.|
|**Return current object**|return this; — used for method chaining.|
|**Pass current object as argument**|someMethod(this);|
|**Access outer class from inner class**|Outer.this.field;|

_��� Note: this cannot be used inside static methods because static methods belong to the class, not to any instance._

## **2.12 Constructors**

A constructor is a special method used to initialize a newly created object.

|   |   |
|---|---|
|**Rule / Type**|**Explanation**|
|**Same name as class**|The constructor name MUST exactly match the class name.|
|**No return type**|Unlike methods, constructors have NO return type — not even void.|
|**Called with new**|Constructors are invoked automatically by the new keyword.|
|**Default constructor**|If no constructor is written, Java provides a no-argument default constructor automatically. If you write ANY constructor, the default is NOT provided.|
|**Parameterized constructor**|Accepts arguments to set initial field values at the time of object creation.|
|**Constructor overloading**|Multiple constructors with different parameter lists are allowed.|

_⭐ Exam Tip: If a class defines a parameterized constructor but no default (no-arg) constructor, calling new ClassName() will cause a compile error._

## **2.13 Static Members**

Static members belong to the CLASS itself, not to any specific object instance.

|   |   |
|---|---|
|**Member Type**|**Behavior**|
|**Static Fields (Class Variables)**|One copy shared by all objects of the class. Initialized once when the class is first loaded.|
|**Static Methods**|Can be called without creating an object: ClassName.methodName(). CANNOT access instance variables or use this/super.|
|**Static Blocks**|Code blocks executed ONCE when the class is first loaded into memory. Used for complex static initialization.|
|**Static Nested Class**|An inner class declared static. Can be instantiated without an outer class object: Outer.Inner obj = new Outer.Inner();|

 Note: static methods cannot be overridden (they can be hidden). They also cannot call non-static members directly because there is no object context._

_⭐ Exam Tip: A very common trap: trying to access an instance variable from a static method. This causes a compile error._


# **CHAPTER 2 — PRACTICE QUESTIONS**

_Java Language Fundamentals_

_25 Multiple Choice Questions  •  Correct answers highlighted in green_

**1.** **Which of the following is a VALID Java identifier?**

A.  3rdValue

B.  my-Variable

**C.  $totalAmount**

D.  float

**✔ Answer: C**   _$totalAmount is valid — identifiers can start with a dollar sign ($). '3rdValue' starts with a digit (invalid), 'my-Variable' contains a hyphen (invalid), and 'float' is a reserved keyword (invalid)._

**2.** **Which of the following identifiers would cause a compile-time error in Java?**

A.  _counter

B.  myClass

**C.  static**

D.  $price

**✔ Answer: C**   _'static' is a Java reserved keyword and cannot be used as an identifier. All other options (_counter, myClass, $price) follow valid naming rules._

**3.** **Java identifiers are case-sensitive. Which of the following would be treated as a DIFFERENT identifier from 'totalScore'?**

A.  totalScore

B.  totalscore

C.  TOTALSCORE

**D.  Both B and C**

**✔ Answer: D**   _Java is case-sensitive, so 'totalScore', 'totalscore', and 'TOTALSCORE' are all distinct identifiers. Both B and C differ from 'totalScore' in case._

**4.** **A variable declared with the 'static' keyword inside a class is called a:**

A.  Local variable

B.  Instance variable

**C.  Class variable**

D.  Parameter

**✔ Answer: C**   _Variables declared with 'static' are class variables (also called static fields). Only one copy exists per class, shared by all objects. Instance variables are non-static; local variables are inside methods._

**5.** **Which of the following statements about local variables in Java is TRUE?**

A.  Local variables are automatically initialized to 0 or null

B.  Local variables are accessible from any method in the class

**C.  Local variables must be initialized before they are used**

D.  Local variables are declared using the static keyword

**✔ Answer: C**   _Unlike instance and class variables, local variables are NOT automatically given default values. The compiler throws an error if you try to use an uninitialized local variable._

**6.** **What is the size and value range of the 'byte' primitive data type in Java?**

A.  16-bit; range -32,768 to 32,767

**B.  8-bit; range -128 to 127**

C.  32-bit; range ~-2.1 billion to 2.1 billion

D.  8-bit; range 0 to 255

**✔ Answer: B**   _The byte type is 8-bit signed integer with a range of -128 to 127. It is useful for saving memory in large arrays. Note: Java bytes are signed, not unsigned (0 to 255)._

**7.** **A developer stores a very large whole number: 9876543210L. Why is the 'L' suffix required?**

A.  It marks the number as a local variable

**B.  It tells the compiler to treat the literal as a long (64-bit) rather than an int (32-bit)**

C.  It converts the number to a floating-point value

D.  It is optional — Java infers the type automatically

**✔ Answer: B**   _Integer literals without a suffix are treated as int (32-bit) by default. 9876543210 exceeds int's range, so the 'L' suffix is required to tell the compiler it is a long (64-bit) literal._

**8.** **Which of the following correctly declares and initializes a double variable?**

A.  double pi = 3.14f;

**B.  double pi = 3.14;**

C.  double pi = 3.14L;

D.  double pi = '3.14';

**✔ Answer: B**   _Floating-point literals without a suffix are double by default. Option A uses 'f' which makes it a float. 'L' is for long integers. Single quotes denote a char literal, not a number._

**9.** **What is the default value of an instance variable of type 'boolean' in Java?**

A.  true

B.  null

C.  0

**D.  false**

**✔ Answer: D**   _Instance variables of type boolean are automatically initialized to false. Numeric types default to 0, and reference types default to null. Local variables have NO default value._

**10.** **Which of the following is the correct way to represent the integer 26 as a HEXADECIMAL literal in Java?**

A.  0b11010

B.  032

**C.  0x1A**

D.  26H

**✔ Answer: C**   _Hexadecimal literals use the prefix '0x' (e.g., 0x1A = 26). '0b11010' is binary (also equals 26). '032' is octal (equals 26). '26H' is not valid Java syntax._

**11.** **What does the escape sequence '\n' represent in a Java String?**

A.  A tab character

B.  A backslash followed by the letter n

**C.  A newline character that moves output to the next line**

D.  A null character

**✔ Answer: C**   _'\n' is the newline escape sequence. It moves the cursor to the beginning of the next line in output. '\t' is a tab, '\\' is a literal backslash, and '\0' is a null character._

**12.** **Which type of Java comment is processed by the Javadoc tool to generate HTML API documentation?**

A.  // Single-line comment

B.  /* Multi-line comment */

**C.  /** Documentation comment */**

D.  <!-- HTML comment -->

**✔ Answer: C**   _Documentation comments start with '/**' and end with '*/'. The Javadoc tool reads these to produce HTML documentation. '<!-- -->' is HTML syntax, not valid in Java._

**13.** **What is the result of the expression: 17 % 5 in Java?**

A.  3

**B.  2**

C.  3.4

D.  0

**✔ Answer: B**   _The % operator returns the remainder of integer division. 17 ÷ 5 = 3 remainder 2. So 17 % 5 = 2._

**14.** **Which operator in Java is used to test whether an object is an instance of a specific class or interface?**

A.  ==

B.  typeof

**C.  instanceof**

D.  isA

**✔ Answer: C**   _'instanceof' is Java's type-comparison operator. It returns true if an object is an instance of the specified class or a subclass/interface. 'typeof' is JavaScript, not Java._

**15.** **Given: int a = 5; What is the value of 'a' after executing: int b = a++;?**

A.  a = 6, b = 6

B.  a = 5, b = 5

**C.  a = 6, b = 5**

D.  a = 5, b = 6

**✔ Answer: C**   _Post-increment (a++) returns the CURRENT value first, then increments. So b gets the original value 5, and then a becomes 6. Pre-increment (++a) would increment first._

**16.** **Which control flow statement should be used when you need to execute different blocks of code based on the specific value of a variable, supporting multiple cases efficiently?**

A.  while loop

B.  if-else chain

**C.  switch statement**

D.  do-while loop

**✔ Answer: C**   _The switch statement is designed for multi-way branching based on a variable's value. While an if-else chain works, switch is cleaner and more efficient for many discrete values._

**17.** **Which loop is guaranteed to execute its body at LEAST once, regardless of whether the condition is true or false?**

A.  for loop

B.  while loop

**C.  do-while loop**

D.  enhanced for loop (for-each)

**✔ Answer: C**   _The do-while loop places the condition check AFTER the body. This means the body always executes at least once. All other loops check the condition before the first execution._

**18.** **What is the effect of the 'continue' statement inside a loop?**

A.  Terminates the loop entirely and exits to the code after the loop

B.  Exits the current method and returns a value

**C.  Skips the rest of the current iteration and jumps to the next loop cycle**

D.  Restarts the loop from the beginning including re-initialization

**✔ Answer: C**   _'continue' skips the remaining statements in the current iteration and proceeds to the next iteration of the loop. 'break' would exit the loop entirely._

**19.** **What is the index of the FIRST element in a Java array?**

A.  1

**B.  0**

C.  -1

D.  It depends on how the array was declared

**✔ Answer: B**   _Java arrays are always zero-indexed. The first element is at index 0, and the last element is at index (array.length - 1). Accessing a negative index throws ArrayIndexOutOfBoundsException._

**20.** **How do you correctly get the number of elements in a Java array named 'scores'?**

A.  scores.length()

B.  scores.size()

**C.  scores.length**

D.  length(scores)

**✔ Answer: C**   _'length' is a PUBLIC FIELD of arrays, not a method — so no parentheses. 'length()' is a method on String. ArrayList uses '.size()'. Using scores.length() would cause a compile error._

**21.** **Which access modifier allows a member to be accessed from any class in any package?**

A.  private

B.  protected

C.  default (no modifier)

**D.  public**

**✔ Answer: D**   _'public' provides the widest accessibility — any class from any package can access the member. Access from widest to narrowest: public > protected > default > private._

**22.** **A class has the following two methods. Is this valid Java code? void display(int x) { } void display(int x, int y) { }**

A.  No — you cannot have two methods with the same name in one class

B.  No — overloaded methods must have different return types

**C.  Yes — this is valid method overloading because the parameter lists differ**

D.  Yes — but only if one method is declared static

**✔ Answer: C**   _Method overloading is valid when methods share the same name but differ in parameter count, type, or order. These two methods have different numbers of parameters, so this is correct overloading._

**23.** **Inside an instance method, what does the 'this' keyword refer to?**

A.  The parent class of the current object

**B.  The current object (the instance the method was called on)**

C.  The class definition itself

D.  The first parameter passed to the method

**✔ Answer: B**   _'this' is a reference to the current object instance — the specific object on which the method was invoked. It is used to disambiguate instance variables from parameters with the same name._

**24.** **What is TRUE about Java constructors?**

A.  A constructor must have a return type of void

B.  If you define a parameterized constructor, Java still automatically provides a default (no-arg) constructor

**C.  A constructor name must exactly match the class name and has no return type**

D.  Constructors can be called directly by name at any time in the program

**✔ Answer: C**   _Constructors must have the exact same name as the class and NO return type (not even void). If you define any constructor, Java NO longer provides the default one automatically._

**25.** **Which of the following is TRUE about static methods in Java?**

A.  Static methods can directly access instance variables of the class

B.  Static methods can use the 'this' keyword to refer to the current object

**C.  Static methods can be called without creating an object of the class**

D.  Static methods are automatically overridden in subclasses

**✔ Answer: C**   _Static methods belong to the class and can be invoked as ClassName.methodName() without any object. They CANNOT access instance variables, use 'this', or be overridden (they can only be hidden)._