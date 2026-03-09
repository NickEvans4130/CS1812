# Functional Programming
**CS1812: Object Oriented Programming II**
*Prof Matthew Hague and Dr Matteo Sammartino*

---

## Tags
#cs1812 #java #oop #functional-programming #lambdas #interfaces

---

## The `Runnable` Interface

A foundational interface: an object that can be *run*.

```java
interface Runnable {
    void run();
}
```

This is the simplest form of a **functional interface** — representing a piece of executable behaviour with no inputs and no return value.

---

## Motivating Example: A Thermostat

**Requirements:**
- Has a set ideal temperature
- Receives updates on current temperature
- When too hot → performs a "cool down" action
- When too cold → performs a "heat up" action
- The exact cooling/heating method should be **configurable**

### Initial Outline

```java
class Thermostat {
    private int idealTemp;

    public Thermostat(int idealTemp) {
        this.idealTemp = idealTemp;
    }

    public void updateTemperature(int curTemperature) {
        if (curTemperature > idealTemp) {
            // too hot: cool down
        } else if (curTemperature < idealTemp) {
            // too cold: warm up
        }
    }
}
```

### With `Runnable` Actions

```java
class Thermostat {
    private int idealTemp;
    private Runnable actionCool;
    private Runnable actionWarm;

    public Thermostat(int idealTemp, Runnable actionCool, Runnable actionWarm) {
        this.idealTemp = idealTemp;
        this.actionCool = actionCool;
        this.actionWarm = actionWarm;
    }

    public void updateTemperature(int curTemperature) {
        if (curTemperature > idealTemp) {
            actionCool.run();
        } else if (curTemperature < idealTemp) {
            actionWarm.run();
        }
    }
}
```

---

## Three Ways to Provide a `Runnable`

### Option 1: Named Classes

```java
class OfficeCooler implements Runnable {
    public void run() { /* turn up air-conditioning */ }
}

class OfficeWarmer implements Runnable {
    public void run() { /* turn down air-conditioning */ }
}

Thermostat office = new Thermostat(21, new OfficeCooler(), new OfficeWarmer());
```

| Pros | Cons |
|---|---|
| Reusable | Requires a new class (and possibly a new file) |
| Logic kept separate | Fragments code — bad if the task is small or context-dependent |

---

### Option 2: Anonymous Classes

Define the class inline without naming it:

```java
Thermostat office = new Thermostat(
    21,
    new Runnable() {
        public void run() { /* turn up air conditioning */ }
    },
    new Runnable() {
        public void run() { /* turn down air conditioning */ }
    }
);
```

| Pros | Cons |
|---|---|
| No need to name the class | Thermostat creation code gets messier |
| Can capture surrounding context (see closures) | Cannot reuse elsewhere |

#### Anonymous Classes with Context (Closures)

Anonymous classes can capture variables from the surrounding scope:

```java
List log = new ArrayList<>();
Thermostat office = new Thermostat(
    21,
    new Runnable() {
        public void run() {
            log.append("cool");
            /* turn up air conditioning */
        }
    },
    new Runnable() {
        public void run() {
            log.append("heat");
            /* turn down air conditioning */
        }
    }
);
office.updateTemperature(18);
System.out.println("Action log: " + log);
```

> ⚠️ This is known as a **closure** — a complex topic. Java has strict rules about which variables can be captured this way (they must be *effectively final*).

---

### Option 3: Lambda Expressions (Function Objects) ✅

Instead of defining a full `Runnable` object, just provide the `run` function directly:

```java
Thermostat office = new Thermostat(
    21,
    () -> { /* cool office */ },
    () -> { /* heat office */ }
);
```

**Lambda syntax:**
```java
// No arguments
() -> { System.out.println("Hello"); }

// Two arguments
(arg1, arg2) -> { System.out.println("Arg 1 is " + arg1); }
```

#### Using Existing Methods (Method References)

```java
class MyClass {
    public void myMethod() { … }
    public static void myStaticMethod() { … }
}

MyClass myObject = new MyClass();

Thermostat office = new Thermostat(
    21,
    myObject::myMethod,       // instance method reference
    MyClass::myStaticMethod   // static method reference
);
```

---

## Functional Interfaces

A **functional interface** is an interface with **exactly one abstract method**. Lambdas can be used wherever a functional interface is expected.

```java
@FunctionalInterface
public interface Runnable {
    public void run();
}
```

The key insight: because there's only one method to implement, Java knows which method a lambda is providing.

---

## Common Functional Interfaces in Java

| Interface | Signature | Purpose |
|---|---|---|
| `Runnable` | `void run()` | Execute an action with no input/output |
| `Consumer<T>` | `void accept(T value)` | Take a value, do something, return nothing |
| `Supplier<T>` | `T get()` | Return a value, take nothing |
| `Function<T, R>` | `R apply(T value)` | Take a value, return a result |

### `Consumer<T>`

```java
Consumer<Integer> intPrinter = new Consumer<>() {
    public void accept(int value) {
        System.out.println(value);
    }
};
// Or as a lambda:
Consumer<Integer> intPrinter = value -> System.out.println(value);
```

### `Supplier<T>`

```java
Supplier<Integer> randomNumbers = new Supplier<>() {
    public int get() {
        return 9; // chosen by fair dice roll
    }
};
// Or as a lambda:
Supplier<Integer> randomNumbers = () -> 9;
```

### `Function<T, R>`

```java
Function<Integer, String> bigOrSmall = new Function<>() {
    public String apply(int value) {
        if (value > 10) return "Big number";
        else return "Small number";
    }
};
// Or as a lambda:
Function<Integer, String> bigOrSmall = value -> value > 10 ? "Big number" : "Small number";
```

---

## What is Functional Programming?

> Functions are no longer just text in a class definition — they become **pieces of data** that can be stored in variables, passed as arguments, and returned from methods.

```java
Runnable myVariable = myObject::myMethod;
Thermostat thermo = new Thermostat(21, myVariable, myVariable);
```

This is the core idea behind functional programming.

### Where Java Uses Functional Programming

- **Streams** (see [[15-Streams]]) — processing collections of data
- **Threads** — running tasks in parallel
- **`ScheduledExecutorService`** — running tasks after a time delay

### Java vs Purpose-Built Functional Languages

| Language | Notes |
|---|---|
| Java | Functional features **retrofitted** onto OOP |
| Scala | Functional + OOP by design, runs on JVM |
| Haskell | Purely functional by design |

> 📚 For deeper study: **CS3510 Functional Programming and Applications** (third year option)

---

## Summary

| Concept | Key Point |
|---|---|
| `Runnable` | Simplest functional interface — no args, no return |
| Anonymous classes | Inline class definition, can capture context |
| Lambda expressions | Concise syntax for single-method interfaces |
| Method references | `object::method` or `Class::staticMethod` |
| Functional interfaces | Exactly one abstract method — enables lambda use |
| Functional programming | Functions as first-class data |
