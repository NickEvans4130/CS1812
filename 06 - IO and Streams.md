# I/O and Streams
*CS1812: Object Oriented Programming II*

---

## Terminal I/O

A program communicates via input and output.

**Output:**
```java
System.out.println("This is an output");
```

**Input (from terminal):**
```java
import java.util.Scanner;

Scanner input = new Scanner(System.in);
String s = input.nextLine();
```

---

## What is a Stream?

> A **stream** is a sequence of data items made available over time.

### Input Stream Examples
- User typing in a terminal
- Receiving from the network
- Reading a file
- Getting data from another process

### Output Stream Examples
- Printing to the terminal
- Sending to the network
- Writing a file
- Sending data to another process

The `java.io` package contains all I/O stream classes.

---

## Java Byte Streams

`InputStream` and `OutputStream` are the base classes for all **byte-wise** I/O.

| Class | Purpose |
|---|---|
| `InputStream` | Reads raw bytes from some input |
| `OutputStream` | Writes raw bytes to some output |

Both provide:
- `read()` / `write()` for **single bytes**
- Methods for reading/writing **arrays of bytes**

### Example — Reading a Binary File

```java
FileInputStream in = new FileInputStream("rhul-logo.png");
int val;
while ((val = in.read()) != -1) {
    if (val >= 0x20 && val < 0x7F) { // Is printable ASCII?
        System.out.print((char) val);
    }
}
in.close();
```

---

## Java Stream Hierarchy

Streams are designed to be **wrapped around each other** (decorator pattern).

- **Filter streams** (e.g. `FilterInputStream`, `FilterOutputStream`) accept other streams in their constructors
- Any class accepting an `InputStream`/`OutputStream` will automatically work with any subclass

### PrintStream

`PrintStream` extends `FilterOutputStream`:
- Constructor takes an existing `OutputStream`
- Defines `print()` and `println()` methods
- **`System.out` is a `PrintStream` instance**

```java
import java.io.*;

PrintStream out = new PrintStream(new FileOutputStream("output.txt"));
out.println("My output");
out.close();
```

---

## Character Streams

When working with **text**, use character streams instead of byte streams.

| Base Class | Direction |
|---|---|
| `Reader` | Character input |
| `Writer` | Character output |

Examples: `FileReader`, `FileWriter`, `BufferedReader`, `BufferedWriter`

Specialised for text because they handle:
- **Unicode** (international character sets)
- **Platform-dependent newlines** (`'\n'` vs `"\r\n"`)

---

## Encoding and Decoding

- **Output** must be encoded (e.g. `toString()`)
- **Input** must be decoded/parsed (e.g. `Scanner`)

Common formats:
- **Text-based:** XML, JSON (web APIs)
- **Binary:** JPEG, MP3, Java serialisation

---

## Buffering

### Why Buffer?
- Each `read()`/`write()` may cause a **system call** (expensive!)
- Buffers allow reading/writing **large blocks** in one system call
- Program then accesses data from fast **memory**

### BufferedInputStream Example

```java
BufferedInputStream b = new BufferedInputStream(new FileInputStream("rhul-logo.png"));
int val;
while ((val = b.read()) != -1) {
    if (val >= 0x20 && val < 0x7F) {
        System.out.print((char) val);
    }
}
b.close();
```

### BufferedReader

- Especially useful for character streams
- Without buffering, you can't predict how many bytes to read for a full line
- `readLine()` scans the internal buffer and returns characters up to the first newline

---

## Buffering Performance Comparison

**Unbuffered file copy:**
```java
Reader inputStream = new FileReader("words.txt");
Writer outputStream = new FileWriter("characteroutput.txt");
int c;
while ((c = inputStream.read()) != -1) {
    outputStream.write(c);
}
// Time: ~6.6 seconds
```

**Buffered file copy:**
```java
Reader inputStream = new BufferedReader(new FileReader("words.txt"));
Writer outputStream = new BufferedWriter(new FileWriter("characteroutput.txt"));
int c;
while ((c = inputStream.read()) != -1) {
    outputStream.write(c);
}
// Time: ~3.7 seconds  (nearly 2× faster)
```

---

## Reading Entire Input at Once

- Read all input in **large chunks** to minimise system calls
- Store everything in a single `String`

> [!warning]
> **Never** use `String +=` for concatenation in a loop — it creates a new object and copies both strings on every iteration. Use `StringBuilder` instead!

```java
char[] block = new char[8192];
while (reader.read(block) >= 0) {
    // add block to internal data structure
}
```

### Full Example with StringBuilder

```java
import java.io.*;

public class ReadTest {
    public static void main(String[] args) throws IOException {
        FileReader reader = new FileReader("longfile.txt");
        StringBuilder buf = new StringBuilder(); // buffers whole file
        char[] block = new char[8192];           // buffers one read op
        while (reader.read(block) >= 0) {
            buf.append(block);
        }
        reader.close();
        System.out.println(buf.toString());
    }
}
```

> No `BufferedReader` needed here — we're already reading large chunks manually.

---

## Flushing Buffers

With buffered output, data may not be written immediately.

| Method | Description |
|---|---|
| `flush()` | Force data to be written now |
| `close()` | Automatically flushes before closing |

> [!warning]
> If you forget to close a `FileOutputStream`, the file may **never get written**!

`PrintStream` can also be configured to **auto-flush on every newline**.

---

## I/O Exception Handling Patterns

### ❌ Bad — Reader not closed on exception

```java
public String getLine(String file) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(file));
    String result = reader.readLine(); // if this throws, close() is never called!
    reader.close();
    return result;
}
```

### ✅ Pattern 1 — Pass exceptions to caller

```java
public String getLine(String file) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(file));
    String result = "";
    try {
        result = reader.readLine();
    } finally {
        reader.close(); // always runs
    }
    return result;
}
```

### ✅ Pattern 2 — Handle all exceptions internally

```java
public String getLine(String file) { // throws nothing
    BufferedReader reader = null;
    String result = null;
    try {
        reader = new BufferedReader(new FileReader(file));
        result = reader.readLine();
    } catch (IOException e) {
        System.err.println("I/O failed: " + e.getMessage());
        result = "";
    } finally {
        if (reader != null) {
            try {
                reader.close();
            } catch (IOException e) { /* do nothing */ }
        }
    }
    return result;
}
```

### ✅ Pattern 3 — Try-with-resources (modern, preferred)

```java
public String getLine(String file) { // throws nothing
    String result = null;
    try (BufferedReader reader = new BufferedReader(new FileReader(file))) {
        result = reader.readLine();
    } catch (IOException e) {
        System.err.println("I/O failed: " + e.getMessage());
        result = "";
    }
    return result;
}
```

> [!tip]
> **Try-with-resources** automatically closes streams whether or not an exception is thrown. This is the cleanest approach — use it when possible.

---

## Tags
#cs1812 #java #oop #io #streams #fileio #buffering
