
## 7.1 Introduction to GUIs

A **Graphical User Interface (GUI)** provides a visual, window-based way for users to interact with an application — using buttons, menus, text fields, and more — instead of a text command line.

### AWT vs. Swing

Java provides two GUI toolkits:

|Feature|AWT (Abstract Window Toolkit)|Swing|
|---|---|---|
|**Platform dependency**|Platform-dependent (uses native OS components)|Platform-independent (draws its own components)|
|**Component weight**|Heavyweight|Lightweight|
|**Look and feel**|Fixed — uses the OS style|Pluggable — can be changed programmatically|
|**Components**|Fewer, basic components|Rich set: tables, trees, color choosers, scroll panes|
|**Architecture**|Does not follow MVC|Follows **MVC (Model-View-Controller)** architecture|
|**Package**|`java.awt`|`javax.swing`|

> ⭐ **Exam Tip:** Swing is built **on top of** AWT — it extends and enhances it. All Swing component names start with **"J"** (e.g., `JButton`, `JFrame`, `JLabel`).

---

## 7.2 Swing Component Hierarchy

```
java.awt.Component          ← base class — defines paint(), repaint(), setSize(), etc.
└── java.awt.Container      ← can hold other components
    └── javax.swing.JComponent   ← superclass of most Swing components
        ├── JButton
        ├── JLabel
        ├── JTextField
        ├── JTextArea
        ├── JPanel
        └── ... (most Swing components)

Top-level containers (do NOT extend JComponent):
    JFrame, JDialog, JWindow, JApplet
```

**Key concepts:**

- A **Component** is any visual element (button, label, text field)
- A **Container** is a component that can hold other components
- **Top-level containers** (`JFrame`, `JDialog`) sit at the top of every GUI window

---

## 7.3 `JFrame` — The Main Application Window

`JFrame` is the primary window that houses your entire GUI application. It includes a title bar, borders, and window controls (minimize, maximize, close).

### The Content Pane

You **never add components directly to a JFrame**. Instead, JFrame automatically creates an inner container called the **content pane** — all components are added to this pane.

### Steps to Create a JFrame Window

java

```java
import javax.swing.*;

public class MyWindow {
    public static void main(String[] args) {

        JFrame frame = new JFrame();                          // 1. Create the JFrame
        frame.setSize(400, 300);                              // 2. Set width and height (pixels)
        frame.setTitle("My Application");                     // 3. Set title bar text
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 4. Close app when X is clicked
        frame.setVisible(true);                               // 5. Make it visible (LAST)
    }
}
```

> 📌 **Note:** Always call `setVisible(true)` **last** — after all components have been added and all properties set.

---

## 7.4 Layout Managers

A **Layout Manager** automatically controls the size and position of components within a container. Without a layout manager, you would have to manually position every component in pixels.

### 7.4.1 FlowLayout

- Arranges components **left to right**, wrapping to the next row when space runs out
- Each component gets exactly the size it needs
- ✅ **Default layout for `JPanel`**

java

```java
panel.setLayout(new FlowLayout());
panel.add(new JButton("OK"));
panel.add(new JButton("Cancel"));
```

### 7.4.2 BorderLayout

- Divides the container into **5 named regions**: `NORTH`, `SOUTH`, `EAST`, `WEST`, `CENTER`
- Each region holds **only one component**
- The `CENTER` region expands to fill any unused space
- ✅ **Default layout for `JFrame`'s content pane**

java

```java
frame.getContentPane().add(new JButton("Top"),    BorderLayout.NORTH);
frame.getContentPane().add(new JButton("Bottom"), BorderLayout.SOUTH);
frame.getContentPane().add(new JButton("Left"),   BorderLayout.WEST);
frame.getContentPane().add(new JButton("Right"),  BorderLayout.EAST);
frame.getContentPane().add(new JButton("Middle"), BorderLayout.CENTER);
```

### 7.4.3 GridLayout

- Arranges components in a **uniform rectangular grid** of equal-sized cells
- Components fill cells in left-to-right, top-to-bottom order
- All cells are exactly the same size

java

```java
// 3 rows, 2 columns, 5px horizontal gap, 5px vertical gap
setLayout(new GridLayout(3, 2, 5, 5));
```

### Layout Manager Summary

|Layout|Behavior|Default For|
|---|---|---|
|`FlowLayout`|Left-to-right, wraps on overflow|`JPanel`|
|`BorderLayout`|5 fixed regions (N/S/E/W/Center)|`JFrame` content pane|
|`GridLayout`|Equal-sized grid cells|—|
|`GridBagLayout`|Most flexible — cell sizes can vary|—|
|`BoxLayout`|Single row or column|—|
|`CardLayout`|Stacks components like cards, one visible at a time|—|

---

## 7.5 Basic Swing Components

### `JPanel`

A general-purpose container for grouping components. It is the building block of complex layouts — you typically nest multiple JPanels with different layout managers inside a JFrame.

java

```java
JPanel panel = new JPanel();
panel.setLayout(new FlowLayout());
frame.getContentPane().add(panel);
```

### `JLabel`

Displays a non-editable text message or image. Used for instructions and field labels.

java

```java
JLabel label = new JLabel("Enter your name:");
label.setToolTipText("Type your full name here");
```

|Useful Methods|Description|
|---|---|
|`setText(String)`|Changes the label's text|
|`getText()`|Returns the label's current text|
|`setToolTipText(String)`|Shows a tooltip on hover|

### `JTextField`

A single-line text input field. Fires an `ActionEvent` when the user presses **Enter**.

java

```java
JTextField nameField = new JTextField(20);     // 20 columns wide
String input = nameField.getText();            // retrieve user input
nameField.setText("Default text");            // set text programmatically
```

### `JTextArea`

A multi-line text input area. Often wrapped in a `JScrollPane` to add scrollbars.

java

```java
JTextArea textArea = new JTextArea(5, 30);     // 5 rows, 30 columns
JScrollPane scrollPane = new JScrollPane(textArea);
```

### `JButton`

A clickable button that triggers an action. Fires an `ActionEvent` when clicked.

java

```java
JButton btn = new JButton("Submit");
btn.addActionListener(e -> System.out.println("Button clicked!"));
```

---

## 7.6 Advanced Swing Components

### `JComboBox` — Drop-down List

Lets the user select one option from a drop-down list. Generates an `ItemEvent`.

java

```java
String[] options = {"Red", "Green", "Blue"};
JComboBox<String> combo = new JComboBox<>(options);
String selected = (String) combo.getSelectedItem();
```

### `JCheckBox` — Independent Toggle

An on/off toggle box. Multiple checkboxes are **independent** of each other.

java

```java
JCheckBox check = new JCheckBox("I agree to the terms");
boolean isChecked = check.isSelected();
```

### `JRadioButton` — Mutually Exclusive Options

Used for selecting **one option from a group**. Must be added to a `ButtonGroup` to enforce mutual exclusivity.

java

```java
JRadioButton male   = new JRadioButton("Male");
JRadioButton female = new JRadioButton("Female");

ButtonGroup group = new ButtonGroup();   // ensures only one can be selected
group.add(male);
group.add(female);
```

> ⭐ **Exam Tip:** `JRadioButton` without a `ButtonGroup` behaves like checkboxes — they will NOT enforce mutual exclusivity. The `ButtonGroup` is essential.

### `JList` — Scrollable List

Displays a list of selectable items. Uses `DefaultListModel` to manage its data.

java

```java
DefaultListModel<String> model = new DefaultListModel<>();
model.addElement("Option 1");
model.addElement("Option 2");
JList<String> list = new JList<>(model);
```

### `JTable` — Data Grid

Displays data in rows and columns. Should always be wrapped in a `JScrollPane`.

java

```java
String[] columns = {"Name", "Age", "Grade"};
Object[][] data   = {{"Alice", 20, "A"}, {"Bob", 22, "B"}};
JTable table = new JTable(data, columns);
frame.add(new JScrollPane(table));
```

### Menus

Built from three classes that work together:

java

```java
JMenuBar  menuBar  = new JMenuBar();          // horizontal bar at the top
JMenu     fileMenu = new JMenu("File");        // drop-down menu
JMenuItem openItem = new JMenuItem("Open");   // clickable item inside the menu

fileMenu.add(openItem);
menuBar.add(fileMenu);
frame.setJMenuBar(menuBar);
```

|Class|Role|
|---|---|
|`JMenuBar`|The top horizontal bar that holds menus|
|`JMenu`|A drop-down menu (e.g., File, Edit, View)|
|`JMenuItem`|An individual selectable item inside a menu|

---

## 7.7 Colors in Swing

The `java.awt.Color` class is used to apply colors to GUI components.

### Predefined Colors

Java provides 13 predefined color constants:

`Color.red` · `Color.blue` · `Color.green` · `Color.yellow` · `Color.orange` · `Color.pink` · `Color.cyan` · `Color.magenta` · `Color.white` · `Color.black` · `Color.gray` · `Color.lightGray` · `Color.darkGray`

### Custom Colors (RGB)

Mix your own color using Red, Green, Blue values (each 0–255):

java

```java
Color customColor = new Color(30, 90, 120);   // R=30, G=90, B=120
```

### Applying Colors

java

```java
panel.setBackground(Color.yellow);           // background color
label.setForeground(Color.red);             // text/foreground color
button.setBackground(new Color(0, 128, 0)); // custom dark green
```

|Method|Effect|
|---|---|
|`setBackground(Color)`|Sets the background color of the component|
|`setForeground(Color)`|Sets the text/foreground color of the component|


# Chapter 7: Java GUI Programming (Swing) — 25 Multiple Choice Questions

---

## 1. What is the primary purpose of a GUI?

A. Execute SQL queries  
B. Provide a visual way for users to interact with applications  
C. Increase CPU speed  
D. Store files permanently

**Answer:** B

**Explanation:**  
A GUI (Graphical User Interface) allows users to interact through windows, buttons, menus, and text fields instead of command-line commands.

---

## 2. Which Java GUI toolkit is platform-independent?

A. AWT  
B. Swing  
C. JDBC  
D. JavaFX Script

**Answer:** B

**Explanation:**  
Swing draws its own components and does not rely on native OS components.

---

## 3. Which GUI toolkit uses native operating system components?

A. Swing  
B. JavaFX  
C. AWT  
D. Servlet

**Answer:** C

**Explanation:**  
AWT uses the operating system's native GUI controls, making it platform-dependent.

---

## 4. Swing components are generally:

A. Heavyweight  
B. Lightweight  
C. Platform-dependent  
D. Text-based

**Answer:** B

**Explanation:**  
Swing components are lightweight because they are drawn by Java rather than the operating system.

---

## 5. Which package contains Swing classes?

A. java.awt  
B. java.io  
C. javax.swing  
D. java.net

**Answer:** C

**Explanation:**  
All Swing GUI components are located in the `javax.swing` package.

---

## 6. Which statement about Swing component names is TRUE?

A. They begin with "A"  
B. They begin with "S"  
C. They begin with "J"  
D. They begin with "W"

**Answer:** C

**Explanation:**  
Examples include `JButton`, `JFrame`, `JLabel`, and `JTextField`.

---

## 7. Which class is the superclass of most Swing components?

A. JFrame  
B. JComponent  
C. JPanel  
D. Container

**Answer:** B

**Explanation:**  
Most Swing components inherit from `JComponent`.

---

## 8. What is a Container?

A. A database object  
B. A component that can hold other components  
C. A file object  
D. A layout manager

**Answer:** B

**Explanation:**  
Containers such as JPanel and JFrame can contain other GUI components.

---

## 9. Which of the following is a top-level container?

A. JButton  
B. JLabel  
C. JFrame  
D. JTextField

**Answer:** C

**Explanation:**  
`JFrame` serves as the main application window.

---

## 10. What is the primary purpose of a JFrame?

A. Store data  
B. Create menus only  
C. Act as the main application window  
D. Manage databases

**Answer:** C

**Explanation:**  
A JFrame provides the main window with title bar and controls.

---

## 11. Components should be added to a JFrame's:

A. Border  
B. MenuBar  
C. Content Pane  
D. Constructor

**Answer:** C

**Explanation:**  
Components are added to the content pane rather than directly to the JFrame.

---

## 12. Which method should generally be called LAST when creating a GUI window?

A. setSize()  
B. setTitle()  
C. setVisible(true)  
D. setLayout()

**Answer:** C

**Explanation:**  
The window should become visible only after all components are added.

---

## 13. Which layout manager is the default for JPanel?

A. GridLayout  
B. BorderLayout  
C. FlowLayout  
D. CardLayout

**Answer:** C

**Explanation:**  
JPanel uses FlowLayout by default.

---

## 14. FlowLayout arranges components:

A. In equal-sized grid cells  
B. Left to right, wrapping when needed  
C. In five regions  
D. Randomly

**Answer:** B

**Explanation:**  
FlowLayout places components horizontally and wraps to a new row when space runs out.

---

## 15. Which layout manager is the default for a JFrame's content pane?

A. GridLayout  
B. BorderLayout  
C. FlowLayout  
D. BoxLayout

**Answer:** B

**Explanation:**  
BorderLayout is the default layout manager for JFrame content panes.

---

## 16. How many regions does BorderLayout have?

A. 3  
B. 4  
C. 5  
D. 6

**Answer:** C

**Explanation:**  
The regions are NORTH, SOUTH, EAST, WEST, and CENTER.

---

## 17. Which BorderLayout region expands to fill unused space?

A. NORTH  
B. SOUTH  
C. CENTER  
D. EAST

**Answer:** C

**Explanation:**  
CENTER automatically expands to occupy remaining space.

---

## 18. GridLayout arranges components:

A. Left-to-right only  
B. In equal-sized rows and columns  
C. In five regions  
D. In overlapping cards

**Answer:** B

**Explanation:**  
Every cell in a GridLayout has identical dimensions.

---

## 19. Which component is commonly used to group other components?

A. JLabel  
B. JPanel  
C. JButton  
D. JTextField

**Answer:** B

**Explanation:**  
JPanel acts as a container for organizing components.

---

## 20. Which Swing component displays non-editable text?

A. JTextField  
B. JTextArea  
C. JLabel  
D. JTable

**Answer:** C

**Explanation:**  
JLabel is used to display text or images.

---

## 21. Which component accepts single-line text input?

A. JTextArea  
B. JTextField  
C. JLabel  
D. JTable

**Answer:** B

**Explanation:**  
JTextField is designed for single-line input.

---

## 22. Which component is designed for multi-line text input?

A. JTextArea  
B. JTextField  
C. JLabel  
D. JComboBox

**Answer:** A

**Explanation:**  
JTextArea allows multiple lines of text.

---

## 23. Which component allows users to select one option from a drop-down list?

A. JCheckBox  
B. JRadioButton  
C. JComboBox  
D. JTable

**Answer:** C

**Explanation:**  
JComboBox displays a drop-down selection list.

---

## 24. What is required to make JRadioButtons mutually exclusive?

A. JPanel  
B. JMenuBar  
C. ButtonGroup  
D. GridLayout

**Answer:** C

**Explanation:**  
Without a ButtonGroup, radio buttons behave like checkboxes.

---

## 25. Which class represents the top horizontal menu bar in a Swing application?

A. JMenu  
B. JMenuItem  
C. JMenuBar  
D. JToolBar

**Answer:** C

**Explanation:**  
JMenuBar sits at the top of the window and contains menus such as File and Edit.

---

# ⭐ High-Yield Exit Exam Facts

|Topic|Important Fact|
|---|---|
|Swing|Platform-independent|
|AWT|Platform-dependent|
|Swing Components|Usually start with "J"|
|JFrame|Main application window|
|JComponent|Parent of most Swing components|
|JPanel|Default layout = FlowLayout|
|JFrame Content Pane|Default layout = BorderLayout|
|BorderLayout|5 regions|
|Center Region|Expands automatically|
|GridLayout|Equal-sized cells|
|JLabel|Displays text/images|
|JTextField|Single-line input|
|JTextArea|Multi-line input|
|JButton|Generates ActionEvent|
|JComboBox|Drop-down list|
|JCheckBox|Independent selections|
|JRadioButton|Requires ButtonGroup|
|JList|Uses DefaultListModel|
|JTable|Usually wrapped in JScrollPane|
|JMenuBar|Holds menus|
|JMenu|Drop-down menu|
|JMenuItem|Selectable menu option|
|setBackground()|Changes background color|
|setForeground()|Changes text color|
|setVisible(true)|Usually called last|

## 🔥 Most Common Exam Traps

1. **Default layout of JPanel = FlowLayout**
    
2. **Default layout of JFrame content pane = BorderLayout**
    
3. **BorderLayout has exactly 5 regions**
    
4. **ButtonGroup is required for radio buttons**
    
5. **Swing is lightweight, AWT is heavyweight**
    
6. **JTable should be placed inside JScrollPane**
    
7. **Components are added to the content pane, not directly to JFrame**
    
8. **setVisible(true) should be called last**
    
9. **Swing follows MVC architecture**
    
10. **All Swing component names start with "J"**

