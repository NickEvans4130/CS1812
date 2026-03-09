# Packaging
**CS1812: Object Oriented Programming II**
Tags: #java #oop #packaging #namespaces #scope #cs1812

---

## What is Packaging?

Writing software isn't just about algorithms — it's also about **organising code well**:

- Code organisation
- Code reuse
- Modularity
- Namespace management

Java's main tools for this (so far): **Classes**, **Inheritance**, and **Packages**.

---

## Namespaces

A **namespace** is a set of identifiers. Multiple namespaces allow the same identifier to refer to different things in different contexts.

```java
class Test1 {
    public static void main(String[] args) {
        int x = 0;
    }
}

class Test2 {
    public static void main(String[] args) {
        String x = "Hello";
    }
}
```

`x`, `main`, and `args` are reused across both classes — they refer to different things.

> In Java, **methods**, **classes**, and **packages** are the three primary namespace management mechanisms.

---

## Packages

Java lets you group related classes into a **package** using the `package` keyword at the top of a file:

```java
package uk.ac.rhul.cs.test;  // Package names are all lower case

class Record {
    private static int totalNumber;
    public String name;
    int x;
    // ...
}
```

### Package Rules

| Rule | Detail |
|---|---|
| A file can be in only **one** package | |
| **Several files** can be in the same package | |
| A file can have many classes, but only **one `public` class** | |
| **Default package** | No `package` declaration → class is in the unnamed default package |

---

## Java Package Conventions

- **Naming:** Often starts with a reversed internet domain to ensure uniqueness
  - e.g. `com.google.common.io`, `uk.ac.rhul.cs.StringQueue`
- The **fully qualified name** of a class includes the package:
  - e.g. `java.io.BufferedReader`, `uk.ac.rhul.cs.StringQueue`

---

## Package Directory Structure

The **file path must match the package name**:

```
uk/ac/rhul/cs/StringQueue.java
```

Compiling and running:
```bash
cd myproject
javac uk/ac/rhul/cs/StringQueue.java
java uk.ac.rhul.cs.StringQueue
```

---

## Imports

`import` opens the namespace of a package so you don't need fully qualified names:

```java
import java.io.*;                   // Import entire package
import java.io.BufferedReader;      // Import just one class

new BufferedReader(...);            // Can use short name after import
new java.io.BufferedReader(...);    // Without import, must use full name
```

- Within the **same package**, you can use just the class name directly
- `java.lang` is **always implicitly imported** (contains `String`, `NullPointerException`, etc.)

---

## Java API

Organised into many packages of related classes. Key ones:

| Package | Contents |
|---|---|
| `java.lang` | Always imported; `String`, `Object`, `NullPointerException`, etc. |
| `java.io` | Input/output |
| `java.util` | Collections, `ArrayList`, `HashMap`, etc. |
| `java.util.concurrent` | Concurrency utilities |
| `java.math` | Big numbers, math functions |

📖 Docs: https://docs.oracle.com/javase/21/docs/api/index.html

---

## Variables and Scope

Three kinds of variables in Java:

| Type | Where declared |
|---|---|
| **Local variables / parameters** | Inside a method or constructor |
| **Instance variables (fields)** | Inside a class, outside any method |
| **Class variables (static fields)** | Inside a class with `static` keyword |

- **Scope** — the portion of the program where the variable is accessible
- **Lifetime** — the period of execution time where the variable exists in memory

> ⚠️ Knowing the scope and lifetime of all your variables is critical!

---

## Local Variables

Defined inside a method, constructor, or block (including parameters).

- **Scope:** only within the block `{}` that defined them
- **Lifetime:** created when the block is entered, deleted when it exits

```java
public void hello(int i) {       // i is a local variable
    int x = 0;                   // x is a local variable
    if (x < i) {
        int y = 0;               // y only exists inside this if-block
        System.out.println("Hello " + y);
    }
    // y is no longer accessible here
}
// x and i are no longer accessible here
```

---

## Instance Variables (Fields)

Declared inside a class, but **outside** any method or constructor. Visibility can be `public`, `protected`, `private`, or default (package-private).

```java
class Record {
    public String name;   // Public instance variable
    int x;                // Default visibility (package-private)
}
```

- **Lifetime:** tied to the object — exists from constructor call until the object is garbage collected
- **Naming (from inside the class):** use `x` directly, or `this.x` if shadowed by a local variable
- **Naming (from outside):** `myRecord.x`

### Visibility Table

| Modifier | Same Class | Same Package | Subclass | Other Package |
|---|---|---|---|---|
| `public` | ✅ | ✅ | ✅ | ✅ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| default | ✅ | ✅ | ❌ | ❌ |
| `private` | ✅ | ❌ | ❌ | ❌ |

---

## Class Variables (Static Fields)

Declared with the `static` keyword — attached to the **class**, not any individual object.

```java
class Record {
    private static int totalNumber;  // Class variable
    public String name;              // Instance variable

    public Record(String s) {
        name = s;
        totalNumber++;               // Shared across all instances
    }
}
```

- **Lifetime:** entire program runtime (from start to termination)
- **Naming:** `Record.totalNumber` from outside; just `totalNumber` from inside (unless shadowed)
- **Scope:** same visibility rules as instance variables

---

## Static Methods (Reminder)

Declared with `static` — attached to the class, not an object.

**Limitations:**
- Can only access **static fields** and call other **static methods**
- No `this` reference

**When to make a method static:**
- If it can run without an actual instance of the class
- If it has no meaningful connection to a particular object's state (if so, consider whether it even belongs in this class)

---

## Nested Classes

Classes defined **inside another class**. Allowed modifiers: `public`, `protected`, `private`, `static`.

> ✅ **Best practice:** Use only `static` nested classes unless you know what you're doing.

```java
public class StringStack {
    private static class ListNode {  // Private static nested class
        // ...
    }

    private ListNode top;

    public void push(String s) {
        ListNode n = new ListNode();
        // ...
    }
}
```
