
## 6.1 What are Streams?

Java uses an abstraction called a **stream** to handle all input and output operations. A stream is a sequential flow of data between a **source** and a **destination**.

```
Source  →  [ Input Stream ]  →  Program  →  [ Output Stream ]  →  Destination
(file, keyboard, network)                    (file, screen, network)
```

- **Input Stream** — reads data **into** the program (from keyboard, file, network)
- **Output Stream** — writes data **out of** the program (to screen, file, network)

Java's I/O classes live primarily in the **`java.io`** package and the newer **`java.nio`** (New I/O) package.

---

## 6.2 Byte Streams vs. Character Streams

Java handles I/O in two fundamental formats:

|Feature|Byte Streams|Character (Text) Streams|
|---|---|---|
|**Data unit**|Raw binary bytes (8-bit)|Characters (16-bit Unicode)|
|**Encoding**|No character encoding|Handles encodings like UTF-8|
|**Best for**|Images, audio, video, executables, binary files|Plain text files, logs, web pages, console input|
|**Abstract base (input)**|`InputStream`|`Reader`|
|**Abstract base (output)**|`OutputStream`|`Writer`|

> ⭐ **Exam Tip:** Always match the stream type to your data. Use **byte streams** for binary data and **character streams** for human-readable text.

---

## 6.3 Byte Stream Classes

|Class|Purpose|
|---|---|
|`FileInputStream`|Reads raw bytes directly from a file on disk|
|`FileOutputStream`|Writes raw bytes directly to a file on disk|
|`BufferedInputStream`|Wraps a `FileInputStream`; uses an internal buffer for significantly faster reads|
|`BufferedOutputStream`|Wraps a `FileOutputStream`; buffers data before writing for faster performance|
|`SequenceInputStream`|Concatenates two or more input streams into one sequential stream|
|`ByteArrayInputStream`|Treats an in-memory **byte array** as an input stream|
|`ByteArrayOutputStream`|Captures output into an in-memory **byte array**|

### Why Use Buffered Streams?

Without buffering, every `read()` or `write()` call makes a direct system call to the disk — which is slow. A `BufferedInputStream` reads a large chunk of data into memory at once, and subsequent reads come from the fast in-memory buffer.

---

## 6.4 Character Stream Classes

|Class|Purpose|
|---|---|
|`BufferedReader`|Reads text efficiently line-by-line using a buffer. Key method: `readLine()`|
|`BufferedWriter`|Writes text efficiently using a buffer. Key method: `newLine()`|
|`CharArrayReader`|Reads from an in-memory **char array** as a stream|
|`CharArrayWriter`|Writes to an in-memory **char array** as a stream|
|`PrintWriter`|Formats and prints text to console or a text file. Key methods: `print()`, `println()`|

---

## 6.5 `FileInputStream` vs. `FileReader` — When to Use Which

|Feature|`FileInputStream`|`FileReader`|
|---|---|---|
|**Stream type**|Byte stream|Character stream|
|**Data read**|Raw binary bytes|Characters (decoded text)|
|**Encoding**|No encoding awareness|Uses platform default encoding|
|**Best for**|Images, audio, video, any binary file|Text files, logs, configuration files|

java

```java
// Reading a text file — use FileReader
FileReader fr = new FileReader("notes.txt");
BufferedReader br = new BufferedReader(fr);   // wrap in BufferedReader for efficiency
String line = br.readLine();

// Reading an image — use FileInputStream
FileInputStream fis = new FileInputStream("photo.jpg");
int byteData = fis.read();
```

---

## 6.6 Object Serialization and Deserialization

### Serialization

**Serialization** converts an object's entire state (all its instance variables) into a sequence of bytes so it can be:

- Saved to a file
- Stored in a database
- Sent over a network

java

```java
// A class must implement Serializable to be serialized
import java.io.Serializable;

class Student implements Serializable {
    String name;
    int age;
    static String school = "ABC";    // NOT serialized — static
    transient String password;       // NOT serialized — transient
}

// Serializing (saving) an object
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("student.dat"));
oos.writeObject(new Student());
```

### Deserialization

**Deserialization** reconstructs the object from the byte stream back into a live Java object:

java

```java
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("student.dat"));
Student s = (Student) ois.readObject();
```

### Key Rules for Serialization

|Rule|Detail|
|---|---|
|**`Serializable` interface**|Class must implement `java.io.Serializable` — it is a **marker interface** (no methods)|
|**`static` variables**|❌ NOT serialized — belong to the class, not the object|
|**`transient` variables**|❌ NOT serialized — explicitly excluded (used for sensitive data like passwords)|
|**`NotSerializableException`**|Thrown if you try to serialize a class that doesn't implement `Serializable`|
|**Writing**|Use `ObjectOutputStream.writeObject()`|
|**Reading**|Use `ObjectInputStream.readObject()`|

> ⭐ **Exam Tip:** The `Serializable` interface is called a **marker interface** or **tagging interface** because it contains **no methods** — it simply signals to the JVM that objects of this class are safe to serialize.

---

## 6.7 The `File` Class

`java.io.File` represents a **file or directory path** on the file system. It does NOT read or write file content — it only inspects and manipulates the file/directory metadata.

java

```java
File f = new File("report.txt");
```

### Common `File` Methods

|Method|Return Type|Description|
|---|---|---|
|`exists()`|`boolean`|`true` if the file/directory physically exists|
|`isFile()`|`boolean`|`true` if the path points to a file|
|`isDirectory()`|`boolean`|`true` if the path points to a directory|
|`canRead()`|`boolean`|`true` if the program has read permission|
|`canWrite()`|`boolean`|`true` if the program has write permission|
|`canExecute()`|`boolean`|`true` if the program can execute the file|
|`getName()`|`String`|Returns just the file/directory name|
|`getParent()`|`String`|Returns the parent directory name|
|`getPath()`|`String`|Returns the relative path|
|`getAbsolutePath()`|`String`|Returns the full path from the root|
|`length()`|`long`|Returns the file size in bytes|
|`delete()`|`boolean`|Deletes the file or empty directory|
|`createNewFile()`|`boolean`|Creates a new empty file if it doesn't exist|

> 📌 **Note:** The `File` class can only inspect and manipulate file **metadata**. To read or write the actual file **content**, you need to use streams (`FileInputStream`, `FileReader`, etc.).


# Chapter 6: Java I/O Streams and File Handling — 25 Multiple Choice Questions

---

## 1. What is a stream in Java?

A. A collection of classes  
B. A sequential flow of data between a source and destination  
C. A type of file  
D. A package for networking

**Answer:** B

**Explanation:**  
A stream represents the flow of data between a source (keyboard, file, network) and a destination (screen, file, network).

---

## 2. An Input Stream is used to:

A. Write data out of a program  
B. Delete files  
C. Read data into a program  
D. Create objects

**Answer:** C

**Explanation:**  
Input streams bring data from sources such as files or keyboards into the program.

---

## 3. An Output Stream is used to:

A. Read data into a program  
B. Write data out of a program  
C. Create directories  
D. Handle exceptions

**Answer:** B

**Explanation:**  
Output streams send data from the program to destinations such as files or screens.

---

## 4. Most Java I/O classes are found in:

A. java.util  
B. java.net  
C. java.io  
D. java.awt

**Answer:** C

**Explanation:**  
The majority of Java's I/O functionality is provided by the `java.io` package.

---

## 5. Which stream type is best for images and videos?

A. Character Stream  
B. Byte Stream  
C. PrintWriter  
D. Reader

**Answer:** B

**Explanation:**  
Images, videos, and other binary files should use byte streams because they contain raw bytes.

---

## 6. Which stream type is best for text files?

A. Byte Stream  
B. Binary Stream  
C. Character Stream  
D. Object Stream

**Answer:** C

**Explanation:**  
Character streams handle Unicode text and character encoding.

---

## 7. What is the abstract base class for byte input streams?

A. Reader  
B. Writer  
C. InputStream  
D. FileInputStream

**Answer:** C

**Explanation:**  
`InputStream` is the parent class of all byte input stream classes.

---

## 8. What is the abstract base class for character output streams?

A. OutputStream  
B. Writer  
C. Reader  
D. PrintWriter

**Answer:** B

**Explanation:**  
`Writer` is the parent class of all character output stream classes.

---

## 9. Which class reads raw bytes directly from a file?

A. FileReader  
B. BufferedReader  
C. FileInputStream  
D. CharArrayReader

**Answer:** C

**Explanation:**  
`FileInputStream` reads binary data byte-by-byte from a file.

---

## 10. Which class writes raw bytes directly to a file?

A. FileOutputStream  
B. PrintWriter  
C. FileWriter  
D. BufferedWriter

**Answer:** A

**Explanation:**  
`FileOutputStream` writes binary data directly to files.

---

## 11. Why are buffered streams faster?

A. They compress data  
B. They store data permanently  
C. They reduce direct disk access by using memory buffers  
D. They encrypt data

**Answer:** C

**Explanation:**  
Buffered streams reduce the number of expensive system calls to the disk.

---

## 12. Which class is commonly used to read text line-by-line?

A. FileInputStream  
B. BufferedReader  
C. OutputStream  
D. ByteArrayInputStream

**Answer:** B

**Explanation:**  
`BufferedReader` provides the convenient `readLine()` method.

---

## 13. Which method is commonly associated with BufferedReader?

A. print()  
B. println()  
C. readLine()  
D. writeObject()

**Answer:** C

**Explanation:**  
`readLine()` reads one line of text at a time.

---

## 14. Which class provides the methods print() and println()?

A. BufferedWriter  
B. FileWriter  
C. PrintWriter  
D. Reader

**Answer:** C

**Explanation:**  
`PrintWriter` is designed for formatted text output.

---

## 15. Which class should be used for reading a text file?

A. FileInputStream  
B. FileReader  
C. ByteArrayInputStream  
D. ObjectInputStream

**Answer:** B

**Explanation:**  
`FileReader` is a character stream specifically designed for text files.

---

## 16. Which class should be used for reading an image file?

A. FileReader  
B. BufferedReader  
C. FileInputStream  
D. PrintWriter

**Answer:** C

**Explanation:**  
Images contain binary data, making byte streams the correct choice.

---

## 17. What is serialization?

A. Deleting objects from memory  
B. Converting an object into a sequence of bytes  
C. Creating multiple objects  
D. Reading text from a file

**Answer:** B

**Explanation:**  
Serialization converts an object's state into bytes for storage or transmission.

---

## 18. Which interface must a class implement to support serialization?

A. Cloneable  
B. Runnable  
C. Serializable  
D. Comparable

**Answer:** C

**Explanation:**  
Only classes implementing `Serializable` can be serialized.

---

## 19. Serializable is known as a:

A. Utility interface  
B. Marker interface  
C. Functional interface  
D. Nested interface

**Answer:** B

**Explanation:**  
It contains no methods and simply marks a class as serializable.

---

## 20. Which variables are NOT serialized?

A. Instance variables  
B. Local variables  
C. static and transient variables  
D. Final variables only

**Answer:** C

**Explanation:**  
`static` belongs to the class and `transient` is explicitly excluded from serialization.

---

## 21. Which exception occurs if a class does not implement Serializable but is serialized?

A. IOException  
B. ClassNotFoundException  
C. NotSerializableException  
D. ArithmeticException

**Answer:** C

**Explanation:**  
The JVM throws `NotSerializableException`.

---

## 22. Which method is used to serialize an object?

A. readObject()  
B. writeObject()  
C. readLine()  
D. print()

**Answer:** B

**Explanation:**  
`ObjectOutputStream.writeObject()` writes an object to a stream.

---

## 23. Which method is used to deserialize an object?

A. writeObject()  
B. write()  
C. readObject()  
D. readLine()

**Answer:** C

**Explanation:**  
`ObjectInputStream.readObject()` reconstructs an object from serialized data.

---

## 24. What is the primary purpose of the File class?

A. Reading file contents  
B. Writing file contents  
C. Managing file and directory metadata  
D. Serializing objects

**Answer:** C

**Explanation:**  
The `File` class represents file paths and metadata but does not read or write content.

---

## 25. Which File method returns the file size in bytes?

A. getName()  
B. getPath()  
C. exists()  
D. length()

**Answer:** D

**Explanation:**  
`length()` returns the size of a file measured in bytes.

---

# ⭐ High-Yield Exit Exam Facts

|Topic|Key Fact|
|---|---|
|Input Stream|Reads data into a program|
|Output Stream|Writes data out of a program|
|Byte Streams|Used for binary files (images, audio, video)|
|Character Streams|Used for text files|
|InputStream|Parent of byte input streams|
|OutputStream|Parent of byte output streams|
|Reader|Parent of character input streams|
|Writer|Parent of character output streams|
|BufferedReader|Uses `readLine()`|
|PrintWriter|Uses `print()` and `println()`|
|Serialization|Object → Bytes|
|Deserialization|Bytes → Object|
|Serializable|Marker interface (no methods)|
|static|Not serialized|
|transient|Not serialized|
|File Class|Handles metadata, not file content|
|length()|Returns file size|
|exists()|Checks existence|
|createNewFile()|Creates a new file|
|delete()|Deletes a file or empty directory|

🔥 **Most-tested exam traps:**

1. `FileInputStream` vs `FileReader`
    
2. Byte Stream vs Character Stream
    
3. `Serializable` = Marker Interface
    
4. `static` and `transient` are NOT serialized
    
5. `File` class does NOT read/write file contents
    
6. `BufferedReader` → `readLine()`
    
7. `PrintWriter` → `print()` / `println()`