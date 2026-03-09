# Exceptions
*CS1812: Object Oriented Programming II*

---

## What is an Exception?

> "An exception is an event, which occurs during the execution of a program, that disrupts the normal flow of the program's instructions." — Oracle Java Tutorial

- Common concept across Java, Python, C++, etc.
- Mechanism for **signalling and handling errors**
	 
---

## Motivating Example

When a method throws a checked exception, every method in the call chain must either **catch** it or **declare** it with `throws`:

```java
static void accessFile() throws IOException {
    FileReader in = new FileReader("f.dat");
    in.close();
}

static void loadData() throws IOException {
    accessFile();
}

public static void main(String[] args) throws IOException {
    loadData();
}
```

If `f.dat` doesn't exist, Java prints a **stack trace** showing the call chain.

### Catching the Exception

```java
public static void main(String[] args) {
    try {
        loadData();
    } catch (IOException e) {
        System.out.println("Can't load: " + e.getMessage());
    }
}
// Output: Can't load: f.dat (No such file or directory)
```

---

## Exceptions and the Call Stack

When an exception is raised:
1. Java **exits the current method** (like an exceptional return)
2. Looks for an exception handler in the **calling method**
3. If no handler found → **stack unwinding** — keeps exiting methods up the call stack
4. If no handler found in `main` → program **terminates with a stack trace**

```
Throws exception  →  [Method where error occurred]
                              ↓ (no handler, looking...)
Forwards exception →  [Method without exception handler]
                              ↓ (no handler, looking...)
Catches exception  →  [Method with exception handler]
```

---

## Exception Handling Syntax

Three blocks: `try`, `catch`, `finally`

```java
try {
    FileReader in = new FileReader("f.dat");
} catch (FileNotFoundException e) {
    System.out.println("I caught an exception");
} finally {
    System.out.println("I always finish here");
}
```

| Block | Purpose | When it runs |
|---|---|---|
| `try` | Encloses code that may throw | Always |
| `catch` | Error handling code | Only when exception is thrown |
| `finally` | Cleanup code | **Always**, regardless of exception |

> [!note]
> A `try` block must be followed by **at least one** `catch` block, or a `finally` block (or both).
> Multiple `catch` blocks can be chained for different exception types.

---

## Kinds of Java Exceptions

```
Throwable
├── Error (Unchecked)
│   └── OutOfMemoryError, etc.
└── Exception
    ├── FileNotFoundException  ← Checked
    └── RuntimeException (Unchecked)
        ├── NullPointerException
        └── ArrayIndexOutOfBoundsException
```

| Kind | Examples | Rule |
|---|---|---|
| **Checked Exceptions** | `FileNotFoundException` | Must be caught (`try/catch`) or declared (`throws`) |
| **Runtime Exceptions** | `ArrayIndexOutOfBoundsException`, `NullPointerException` | Indicate programming errors — fix the source, don't catch |
| **Errors** | `OutOfMemoryError` | Fatal VM errors — unrecoverable |

> [!warning]
> Any `Throwable` can appear in a `throws` clause.

---

## Exceptions Are Objects

All exceptions are **objects** with `Throwable` as their superclass.

### Catching and Using Exception Objects

```java
try {
    FileReader in = new FileReader("f.dat");
} catch (FileNotFoundException e) {
    System.out.println("Error loading file: " + e.getMessage());
    e.printStackTrace();
}
```

Useful methods on exception objects:
- `e.getMessage()` — returns the error message string
- `e.printStackTrace()` — prints the full call stack trace

---

## Custom Exceptions

Extend `Exception` (for checked) or `RuntimeException` (for unchecked):

```java
class MyException extends Exception {
}

class Test {
    public void myMethod() throws MyException {
        // ...
        throw new MyException();
    }
}
```

You can add custom **fields and methods** to your exception classes for richer error information.

---

## Exceptions + File I/O (Full Example)

```java
public String getLine(String file) { // throws nothing
    BufferedReader reader = null;
    String result = null;
    try {
        reader = new BufferedReader(new FileReader(filename));
        result = reader.readLine();
    } catch (IOException e) {
        System.out.println("I/O failed: " + e.getMessage());
        result = "";
    } finally {
        if (reader != null) {
            try {
                reader.close(); // close() could also throw!
            } catch (IOException e) { /* do nothing */ }
        }
    }
    return result;
}
```

---

## When to Use Exceptions

### Advantages
- Clean **separation** of error handling from normal code
- **Propagation** of errors through the call stack without manual passing
- **Grouping** of errors that share the same handling logic
- Enables designing a **clean API**

### Caveats
- Exceptions are **slow** — only use for genuinely exceptional events
- **Unchecked exceptions** (RuntimeException, Error) terminate your program — don't throw them intentionally!

---

## Tags
#cs1812 #java #oop #exceptions #errorhandling
