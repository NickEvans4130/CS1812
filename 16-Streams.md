# Streams
**CS1812: Object Oriented Programming II**
*Prof Matthew Hague and Dr Matteo Sammartino*

---

## Tags
#cs1812 #java #oop #streams #functional-programming #collections

---

## Two Kinds of Streams in Java

| Type | Purpose |
|---|---|
| **IO Streams** (covered earlier) | Data flowing into/out of your program (`InputStream`, `OutputStream`) |
| **Stream API** (this lecture) | Internal data processing pipelines over collections |

This lecture is about the **Stream API** for internal calculations.

---

## Stream API Overview

A `Stream<T>` represents a **sequence of data items** to be processed in a pipeline.

### Pipeline Steps

1. **Source** — create a stream from a collection or other source
2. **Intermediate operations** — transform or filter the stream (lazy, chainable)
3. **Terminal operation** — produce a result or side-effect (triggers execution)

```
Source → [filter] → [map] → [filter] → Terminal
```

---

## The Marble Run Analogy

Imagine marbles flowing through a run:
- **Source:** bag of red/blue marbles
- **Filter:** remove blue marbles
- **Transform:** paint remaining marbles green
- **Aggregate:** collect in a bowl

```java
List<Marble> marbleRun(List<Marble> marbles) {
    return marbles.stream()
        .filter(marble -> marble.getColor() != Color.BLUE)
        .map(marble -> marble.withColor(Color.GREEN))
        .collect(Collectors.toList());
}
```

---

## Creating Streams

### From a Collection

Any `Collection<T>` can be turned into a `Stream<T>` with `.stream()`:

```java
List<Integer> myList = …;
myList.stream()

Set<String> mySet = …;
mySet.stream()
```

### From Literal Values

Useful for testing:

```java
Stream.of(1, 2, 3, 4)
```

---

## Terminal Operations

### Collect to a Collection

```java
import java.util.stream.Collectors;

List<String> puncts = Stream.of("!", "?", ":")
    .collect(Collectors.toList());

// Also available:
// Collectors.toSet()
```

### `forEach` — Run a Function on Each Item

```java
Stream.of(5, 2, 3, 5, 1, 6, 7, 87, 2, 3)
    .forEach(n -> {
        System.out.println("The next data item is:");
        System.out.println("Data: " + n);
    });
```

> `forEach` takes a `Consumer<T>` — a function that takes a value and returns nothing.

### Aggregate: `min` / `max`

These require a comparator — a function defining how to compare items.

```java
// For Strings, compare by length
String longest = Stream.of("one", "two", "three")
    .max((s1, s2) -> Integer.compare(s1.length(), s2.length()))
    .orElse("zero");

// For integers, use Integer::compare directly
int largest = Stream.of(1, 2, 3)
    .max(Integer::compare)
    .orElse(0);
```

#### The `Optional` Type

`min()` and `max()` return `Optional<T>` because the stream might be empty.

```java
Stream<Integer> s = Stream.of();
int x = s.max(Integer::compare).orElse(0); // returns 0 if empty
```

Use `.orElse(defaultValue)` to safely extract the result.

---

## Filtering

### `filter` — Keep Items Matching a Predicate

```java
// Stream<T> filter(Function<T, Boolean> predicate)

Stream<Integer> evens = Stream.of(1, 2, 3, 4, 5, 6)
    .filter(n -> n % 2 == 0);
// → 2, 4, 6
```

### Other Filtering Methods

| Method | Behaviour |
|---|---|
| `filter(pred)` | Keep items where predicate is `true` |
| `limit(n)` | Keep only the first `n` items |
| `takeWhile(pred)` | Take items until predicate fails, then drop the rest |
| `dropWhile(pred)` | Drop items until predicate passes, then take the rest |

---

## Transforming: `map`

`map` applies a function to every element, transforming each one:

```java
// Add 1 to each number
Stream.of(5, 2, 3, 1).map(n -> n + 1);

// Prepend "data: " to each number
Stream.of(5, 2, 3, 1).map(n -> "data: " + n);

// Create Student objects from names
Stream.of("alfred", "simon", "theodore")
    .map(name -> {
        String upperName = name.toUpperCase();
        return new Student(upperName);
    });
```

> 💡 Use curly braces `{ }` and `return` when your lambda has more than one line.

---

## Exercises

```java
// 1.
Stream.of(1,2,3,4,5,6)
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toSet());
// → {2, 4, 6}

// 2.
Stream.of(1,2,3,4,5,6,7,8,9,10)
    .filter(n -> 4 < n)
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());
// → [6, 8, 10]

// 3.
Stream.of(1,2,3,4,5,6,7,8,9,10)
    .limit(3)
    .filter(n -> n < 8)
    .collect(Collectors.toSet());
// → {1, 2, 3}

// 4.
Stream.of(1,2,3,4,5,6,7,8,9,10)
    .filter(n -> n % 2 == 1)
    .collect(Collectors.toList());
// → [1, 3, 5, 7, 9]

// 5.
Stream.of(1,2,3,4,5,6)
    .map(n -> n + 1)
    .filter(n -> n % 2 == 0)
    .max(Integer::compare)
    .orElse(0);
// → 6

// 6.
Stream.of("coding", "is", "fun")
    .map(String::length)
    .filter(n -> n % 2 == 0)
    .max(Integer::compare)
    .orElse(0);
// → 6

// 7.
Stream.of("coding", "is", "fun")
    .map(String::length)
    .filter(n -> n % 2 == 0)
    .map(n -> n - 1)
    .forEach(n -> System.out.println("Num: " + n));
// Prints: Num: 5  (length of "coding" is 6 → 6-1=5)
```

---

## Rules for Functions Used in Streams

Functions passed to `filter`, `map`, etc. must follow two rules:

### 1. Non-interfering
They must **not modify** the items in the stream.

```java
// ❌ BAD — modifies the student object directly
stream.map(student -> {
    student.name = "New name"; // interfering!
    return student;
});

// ✅ GOOD — creates a new object instead
stream.map(student -> new Student("New name"));
```

### 2. Stateless
They must **not depend on external mutable state**.

```java
// ❌ BAD — result depends on external counter state
Counter counter = new Counter();
counter.add();
Stream.of(1,2,3,4,5,6)
    .filter(n -> {
        counter.add();
        return counter.isEven(); // depends on external state!
    });
```

> These rules matter especially when streams are run in **parallel** — violating them causes unpredictable results.

---

## Summary

| Concept | Method(s) |
|---|---|
| Create stream | `.stream()`, `Stream.of(...)` |
| Filter | `.filter(pred)`, `.limit(n)`, `.takeWhile(pred)`, `.dropWhile(pred)` |
| Transform | `.map(fn)` |
| Terminate (collect) | `.collect(Collectors.toList())`, `.collect(Collectors.toSet())` |
| Terminate (aggregate) | `.max(comparator)`, `.min(comparator)` |
| Terminate (side-effect) | `.forEach(consumer)` |
| Handle empty result | `.orElse(default)` on `Optional` |

**Benefits of streams:**
- No need to write manual loops
- Compact, declarative code with clearer intent
- Potential for under-the-hood optimisation (e.g. parallel execution)

> 📎 Streams use **lambdas** and **functional interfaces** — see [[15-FunctionalProgramming]]
