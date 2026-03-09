# Generics
**CS1812 – Object Oriented Programming II**
Tags: #java #generics #oop #cs1812

---

## Polymorphism

Polymorphism = operations that apply to values of multiple types.

Two kinds in Java:

| Type | Description |
|---|---|
| **Ad-hoc** | Different version defined per type (overloading) |
| **Parametric** | Single definition works for any type (generics) |

### Ad-hoc Polymorphism (Overloading)

```java
public static void printQuoted(String s) { ... }
public static void printQuoted(int i)    { ... }
```

The compiler picks the right version based on argument type.

---

## Parametric Polymorphism (Generics)

Introduced in **Java 1.5**. Allows classes and methods to be parameterised by a type `T` chosen at usage time.

**Syntax:** type parameters go in angle brackets `<T>`, multiple are comma-separated `<T, U, V>`.

### The Problem Without Generics

Pre-generics approach: store everything as `Object`.

```java
class ListNode {
    public Object payload;  // no type safety
    public ListNode next;
}
// requires explicit cast — can throw ClassCastException at runtime
String s = (String) n.payload;
```

Problems:
- No type information at compile time
- Users must manually track what type is stored
- Easy to accidentally mix types → runtime errors

### Generic List Node

```java
class ListNode<T> {
    public T payload;
    public ListNode<T> next;

    public ListNode(T o) {
        payload = o;
        next = null;
    }
}
```

Usage:
```java
ListNode<String> n = new ListNode<String>("Hello");
n.next = new ListNode<String>("World");
String s = n.payload;  // no cast needed — compiler enforces type
```

---

## Multiple Type Parameters

```java
public class Pair<L, R> {
    private L left;
    private R right;

    public Pair(L left, R right) { ... }

    public L getLeft()  { return left;  }
    public R getRight() { return right; }
}
```

---

## Generics and Primitive Types

Generics **cannot** be used with primitive types (`int`, `char`, `float`, `double`, etc.).

```java
ListNode<int> n = new ListNode<int>(); // COMPILE ERROR
```

### Wrapper Classes

Use the corresponding wrapper class instead:

| Primitive | Wrapper |
|---|---|
| `int` | `Integer` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

```java
ListNode<Integer> n = new ListNode<Integer>();

Integer a = Integer.valueOf(1);  // recommended (valueOf, not new Integer())
int x = a.intValue();
```

> [!warning] Wrapper Equality
> Wrapper objects are objects, not primitives. Use `.equals()` not `==`.
> ```java
> Integer a = new Integer(1);
> Integer b = new Integer(1);
> System.out.println(a == b);       // false (different objects)
> System.out.println(a.equals(b));  // true
> ```

### Deprecation

`new Integer(1)` is **deprecated** — prefer `Integer.valueOf(1)` which can reuse cached objects and is more efficient.

---

## Autoboxing / Autounboxing

Java **automatically converts** between primitives and wrappers where needed.

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(5);        // autoboxing:   int → Integer
int x = list.get(0); // unboxing: Integer → int
```

> [!danger] Autoboxing Pitfall
> Autoboxing can hide subtle bugs with object identity:
> ```java
> ListStack<Integer> n = new ListStack<>();
> n.push(12345);
> n.push(12345);
> System.out.println(n.pop() == n.pop()); // may be false!
> ```
> Whether the result is `true` or `false` depends on JVM caching. Always use `.equals()`.

---

## Generic Methods

A method can declare its own type parameter, separate from the enclosing class's.

```java
// Standalone generic method — <T> declared in signature
public static <T> T getFirst(ListNode<T> list) {
    return list.payload;
}

// Method using the class's type parameter — no <T> in signature
class ListNode<T> {
    public T getPayload() { return this.payload; }
}
```

---

## Bounded Generics

Constrain the type parameter with `extends`:

```java
// T must be a subclass of Animal
public static <T extends Animal> void soundFirst(ListNode<T> n) {
    n.payload.sound();  // safe — Animal guarantees sound() exists
}
```

---

## Summary & Caveats

- ✅ Avoids code duplication (no need for `IntList`, `StringList`, etc.)
- ✅ Compiler enforces type safety
- ❌ Cannot use primitive types — use wrappers
- ❌ Cannot create generic arrays: `T[] arr = new T[size]` — **compile error**
- There is more: wildcards (`?`), covariance, contravariance (beyond this module)
