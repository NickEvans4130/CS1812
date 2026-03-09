# Documentation and Conventions
**CS1812: Object Oriented Programming II**
Tags: #java #oop #documentation #conventions #javadoc #cs1812

---

## Why Documentation Matters

A program is not just meant to be **executed**. It is also meant to be:

- **Read** by other developers
- **Shared** across teams
- **Debugged** when things go wrong
- **Modified** as requirements change
- **Distributed** to users or contributors

> Documentation is not optional — it's a core part of professional software development.

---

## Javadoc

Java's built-in documentation system. Javadoc **generates a set of web pages** from specially formatted comments inside `.java` files.

- Javadoc comments begin with `/**` and end with `*/`
- They go **immediately before** the class, method, or field they document

### Class-Level Javadoc

```java
/**
 * @author Alice <alice@rhul.ac.uk>
 * @version 3.14
 */
public class Animal {
    // ...
}
```

### Method-Level Javadoc

```java
/**
 * Validates a chess move.
 *
 * Use {@link #doMove(int, int, int, int)} to move a piece.
 *
 * @param fromFile  file from which a piece is being moved
 * @param fromRank  rank from which a piece is being moved
 * @param toFile    file to which a piece is being moved
 * @param toRank    rank to which a piece is being moved
 * @return true if the move is valid, otherwise false
 */
boolean isValidMove(int fromFile, int fromRank, int toFile, int toRank) {
    // ...
}
```

Common Javadoc tags:

| Tag | Purpose |
|---|---|
| `@author` | Who wrote the class |
| `@version` | Version number |
| `@param` | Describes a method parameter |
| `@return` | Describes the return value |
| `@throws` / `@exception` | Describes exceptions thrown |
| `{@link #method}` | Inline link to another method |

---

## Coding Conventions

Java is flexible in how you format code, but teams need to **agree on a consistent style** so everyone can read each other's code.

Two widely used style guides:
- **Oracle Java Code Conventions**
- **Google Java Style Guide**

---

## Common Naming Conventions

| Item | Convention | Example |
|---|---|---|
| Package names | All lowercase | `uk.ac.rhul.cs.test` |
| Class names | `UpperCamelCase` noun | `StudentRecord`, `LinkedList` |
| Variable names | `lowerCamelCase` | `studentName`, `totalCount` |
| Method names | `lowerCamelCase` verb | `getWeight()`, `printFoods()` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_LEGS`, `DEFAULT_TIMEOUT` |

---

## Class Declaration Conventions

- Only **one top-level class** per file (the one matching the filename)
- Use `get`/`set` prefix for **accessor and mutator methods** when accessing fields directly
- Use `is` prefix for **boolean variables and methods** (e.g. `isValid()`, `isEmpty()`)
- **Class (static) variables should never be `public`**

---

## Google Style: Braces and Indentation

- Always use braces with `if`, `else`, `for`, `do`, and `while` — **even when the body is a single statement or empty**
- Use **Kernighan & Ritchie style** (opening brace on same line)
- Indent by **2 spaces** each time a new block opens
- Each statement on its own line

```java
if (condition()) {
  try {
    something();
  } catch (ProblemException e) {
    recover();
  }
}
```

---

## Why Braces Matter — Spot the Bug

### Version 1: No formatting (hard to read)

```java
while (true) {
fact = fact * i;
if (i > 1) i--;
else System.out.println(fact); break;  // break ALWAYS runs, not just in else!
}
```

### Version 2: Formatted but still broken

```java
while (true) {
    fact = fact * i;
    if (i > 1)
        i--;
    else
        System.out.println(fact);
    break;    // ← Still always runs — not inside the else block!
}
```

### Version 3: Braces added — bug now visible

```java
while (true) {
    fact = fact * i;
    if (i > 1) {
        i--;
    } else {
        System.out.println(fact);
    }
    break;    // ← Clearly wrong now — break should be inside else
}
```

### Fixed

```java
while (true) {
    fact = fact * i;
    if (i > 1) {
        i--;
    } else {
        System.out.println(fact);
        break;    // ✅ Now correctly inside else
    }
}
```

### Clean implementation (refactored)

```java
public class Factorial {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int i = in.nextInt();
        int fact = 1;
        for (; i > 1; i--) {
            fact = fact * i;
        }
        System.out.println(fact);
    }
}
```

> The `while(true)` loop was unnecessary — a `for` loop expresses the intent much more clearly.

---

## Key Takeaways

- Always use braces, even for single-line bodies — it prevents subtle bugs and makes refactoring safer
- Good formatting makes bugs **visible at a glance**
- Document your public API with Javadoc — your future self and teammates will thank you
- Follow a consistent naming convention throughout your project
