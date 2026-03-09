# Inheritance
**CS1812: Object Oriented Programming II**
Tags: #java #oop #inheritance #cs1812

---

## Relationships Between Types

In Java, types have hierarchical relationships:

- **Any class is an Object** — you can assign any object to an `Object` variable
- **An Object is not necessarily any specific class** — assigning downward requires a cast (or causes an error)
- **A subclass is an instance of its superclass** — e.g. `ArrayList` is a `List`

```java
String myString = "A String";
Object myObject = myString;        // OK — upcasting

String myString = myObject;        // ERROR — downcasting without cast

List<String> myList = new ArrayList<String>();  // OK — ArrayList implements List
```

---

## Subclasses

A class can **extend** another class to inherit its fields and methods.

```java
public class Animal {
    protected double weight;
    public Animal() { ... }
    public double getWeight() { ... }
}

public class Dog extends Animal {
    public Dog() {
        this.weight = 5.4;  // Inherited field
    }
}

public class Cat extends Animal {
    public Cat() {
        this.weight = 3.1;  // Inherited field
    }
}
```

> **UML arrow convention:** open triangle arrowhead pointing *upward* to the parent class.

All classes implicitly extend `Object` at the top of the hierarchy:
```
Object → Animal → Dog
                → Cat
```

---

## Constructor Chaining

- If a constructor **does not** explicitly call a superclass constructor, Java **automatically inserts** `super()` (the no-argument constructor of the superclass)
- Java creates objects by calling a chain of constructors **all the way up to** `java.lang.Object`
- The superclass constructor always runs **before** the rest of the subclass constructor
- So the **first constructor to actually execute** is always `Object()`

```java
public class Animal extends Object {
    public Animal() {
        super();  // Implicitly added if omitted
        // ...
    }
}

public class Dog extends Animal {
    public Dog() {
        super();  // Calls Animal()
        this.weight = 5.4;
    }
}
```

---

## Inheritance and Encapsulation

You can hide fields from subclasses using `private`, then expose them via constructors, getters/setters, or `protected` methods:

```java
public class Animal {
    private double weight;

    public Animal(double weight) {
        this.weight = weight;
    }

    public double getWeight() { return weight; }

    protected void gainWeight(double weight) {
        this.weight += weight;
    }
}

public class Cat extends Animal {
    public Cat(double weight) {
        super(weight);  // Animal has no default constructor — must call super explicitly
    }

    public void catchMouse() {
        gainWeight(0.02);  // Uses protected method, can't touch weight directly
    }
}
```

> ⚠️ If the superclass has **no default constructor**, the subclass **must** explicitly call `super(...)` with the required arguments.

---

## Overriding Methods

Subclasses can **override** inherited methods:

- The new method must have the **same signature** (same parameter types and return type — or a *subtype* of the return type)
- Use `@Override` annotation to make it explicit (causes a compile error if it doesn't actually override anything)
- The overridden parent method is still accessible via `super`

```java
public class Animal {
    public void printFoods() {
        for (String f : foods) {
            System.out.println(f);
        }
    }
}

public class Dog extends Animal {
    @Override
    public void printFoods() {
        System.out.println("The dog eats:");
        super.printFoods();  // Calls Animal.printFoods()
    }
}
```

### Key behaviour: Late Binding

> Only the **most derived** (latest) version of a method is active in an object — even when called from parent class code.

```java
public class Animal {
    public void printAllInformation() {
        System.out.println("Weight: " + weight);
        printFoods();  // Calls Dog.printFoods(), not Animal.printFoods()!
    }
}
```

**Call sequence for `d.printAllInformation()`:**
```
main → Dog() → Animal() → Animal.printAllInformation → Dog.printFoods → Animal.printFoods
```

---

## `final`: Controlling Inheritance

| Usage | Effect |
|---|---|
| `final` variable | Can only be set once (use for constants) |
| `final` method | Cannot be overridden by subclasses |
| `final` class | Cannot be extended at all |

```java
public static final int MAX_LEGS = 750;              // Constant

protected final void gainWeight(double w) { ... }    // Cannot override

public final class String { ... }                    // Cannot extend String
```

---

## Abstract Classes

Use when a supertype **makes sense conceptually but shouldn't be instantiated** (e.g. a raw `Animal` object):

- Declared with the `abstract` keyword
- **Cannot be instantiated**, only inherited from
- May contain **abstract methods** — declared without a body; any non-abstract subclass **must** implement them

```java
public abstract class Animal {
    protected double weight;

    public Animal() { this.weight = 0; }

    public abstract void makeSound();  // No body — subclass must implement
}

public class Dog extends Animal {
    public Dog() { this.weight = 5.4; }

    @Override
    public void makeSound() {
        System.out.println("woof");
    }
}
```

---

## Interfaces

- A class can only `extends` **one** superclass, but can `implements` **multiple** interfaces
- An interface specifies a list of **abstract public methods** that any implementing class must provide
- Interfaces have no fields (except `static` constants)

```java
public interface List<E> {
    public E get(int index);
    // ...
}

public interface Deque<E> {
    public void push(E e);
    // ...
}

public class LinkedList<E> implements List<E>, Deque<E> {
    public E get(int index) { ... }
    public void push(E e) { ... }
}
```

### Using Interface Types

Variables can be declared with an interface type — guaranteeing certain methods are available, while the concrete implementation can vary:

```java
protected List<String> foods;
// ...
foods = new ArrayList<String>();  // Must instantiate with a concrete class
```

### Defining an Interface

```java
public interface Animal {
    int getWeight();
    void makeSound();
}

public class Dog implements Animal {
    private double weight;
    public Dog() { weight = 5.4; }

    @Override
    public int getWeight() { return (int) weight; }

    @Override
    public void makeSound() { System.out.println("Woof"); }
}
```

---

## Abstract Class vs Interface — When to Use What

| Use | When |
|---|---|
| **Interface** | Several unrelated classes need to share the same set of method signatures; client code should work with them interchangeably |
| **Abstract class** | Several related classes share fields/code that can be written once and reused |

---

## Best Practices

- **Keep type declarations as general as possible** — makes changing the implementation easier later

```java
List<Integer> myList = new ArrayList<Integer>();
Collection<String> strings = new ArrayList<String>();
Map<String, Float> mp = new HashMap<String, Float>();
```

---

## `instanceof`

- `obj instanceof Type` → `true` if `obj` is an instance of `Type` or any subclass/interface of it
- Always `false` if `obj == null`
- Always `true` for `obj instanceof Object` (unless null)
- **Use sparingly** — needing `instanceof` frequently is a sign of poor design

---

## `instanceof` vs `.equals()` — Symmetry Problem

Using `instanceof` in `.equals()` can break **symmetry** when subclasses override equals:

```java
// StudentRecord.equals uses instanceof StudentRecord
sr.equals(esr);   // true  — esr IS a StudentRecord
esr.equals(sr);   // false — sr is NOT an ExtendedStudentRecord
```

> Prefer `getClass()` comparison in `.equals()` to maintain the symmetry contract.

---

## Food for Thought

Why is `ArrayList<String>` **not** a subclass of `ArrayList<Object>`, even though `String` is a subclass of `Object`?

```java
ArrayList<Object> items = new ArrayList<String>();  // COMPILE ERROR
```

> Hint: think about both *adding* and *retrieving* elements. If you could assign `ArrayList<String>` to `ArrayList<Object>`, you could then do `items.add(new Integer(5))` — but the underlying list only holds Strings. Java's type system prevents this (this is called **invariance** in generics).
