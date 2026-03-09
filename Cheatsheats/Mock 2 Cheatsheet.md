## 1. Dynamic Dispatch (Polymorphism)

> When a subclass overrides a method, Java **always calls the actual object's version** — not the variable type's version.

### Key Syntax

```java
// Variable type = Base, but actual object = Sub
Base x = new Sub();   // ← the object IS a Sub
x.build();            // ← calls Sub's methods, NOT Base's
```

```java
class Base {
    protected static int c = 0;          // static = shared across ALL objects
    String build() { return "[" + part() + "]"; }
    String part()  { return "B" + c; }
}

class Sub extends Base {
    @Override                             // ← always write this when overriding
    String part() {
        Base.c++;                         // modifies the SHARED static variable
        return "S" + Base.c;
    }
}
```

### Rules to Remember

|Situation|What runs?|
|---|---|
|`Base x = new Sub()` then `x.someMethod()`|**Sub's version** (if overridden)|
|`static` variable|**Shared** — one copy for all objects|
|`Base.c++` then `return "S" + Base.c`|Increment happens **before** the return reads it|

### Exam Trick

```
x.build() → calls part() inside Base's build()
           → BUT part() is overridden in Sub
           → Java picks Sub.part() at runtime  ← this is dynamic dispatch
```

---

## 2. Inheritance — `super` keyword

> Use `super` to call the **parent class's constructor or methods** from the child class.

### Constructor Syntax

```java
class Parent {
    private String title;   // private = child CANNOT access directly
    private String author;

    public Parent(String title, String author) {
        this.title = title;
        this.author = author;
    }

    public String getSummary() {
        return title + " by " + author;
    }
}

class Child extends Parent {
    private int durationMinutes;   // Child's OWN field

    public Child(String title, String author, int durationMinutes) {
        super(title, author);              // ← MUST be first line; calls Parent constructor
        this.durationMinutes = durationMinutes;
    }

    @Override
    public String getSummary() {
        return super.getSummary()          // ← reuse parent's method output
               + " [Audio, " + durationMinutes + " min]";
    }
}
```

### Rules to Remember

|Rule|Detail|
|---|---|
|`super(...)`|Must be the **very first line** in the constructor|
|`super.method()`|Calls the **parent's version** of an overridden method|
|`private` fields|Child **cannot** access them directly — must use `super(...)` or getters|
|`@Override`|Not required, but always include it — catches typos|

### Exam Template

```java
// Child constructor:
public ChildClass(/* all params */) {
    super(/* parent's params only */);   // step 1: init parent
    this.ownField = ownField;            // step 2: init child's own fields
}

// Child getSummary():
public String getSummary() {
    return super.getSummary() + " [extra info]";
}
```

---

## 3. Linked List Traversal

> Walk through a linked list by starting at `head` and following `.next` until `null`.

### Node Structure (given)

```java
class Node<K, V> {
    K key;
    V value;
    Node<K, V> next;   // ← pointer to the next node
}
```

### The Universal Traversal Pattern

```java
// ⭐ Memorise this pattern — it appears in get(), contains(), print(), etc.
Node<K, V> current = head;          // 1. start at the beginning
while (current != null) {           // 2. keep going until end of list
    if (current.key.equals(key)) {  // 3. check this node (use .equals() NOT ==)
        return current.value;       // 4a. found it → return/act
    }
    current = current.next;         // 4b. not found → move to next node
}
return null;                        // 5. fell off the end → not found
```

### Full `get()` Implementation

```java
public V get(K key) {
    Node<K, V> current = head;
    while (current != null) {
        if (current.key.equals(key)) {
            return current.value;
        }
        current = current.next;
    }
    return null;
}
```

### Rules to Remember

|Rule|Detail|
|---|---|
|`==` vs `.equals()`|Always use `.equals()` for objects (String, K, V) — `==` compares memory addresses|
|Loop condition|`current != null` — not `current.next != null` (that misses the last node)|
|Return type|`get()` returns `V` (the generic value type), or `null` if not found|
|Generics syntax|`Node<K, V>` — both type params travel together|

---

## Quick Reference — Syntax Patterns

```java
// 1. Polymorphism
ParentType x = new ChildType();   // child object, parent variable

// 2. Override
@Override
ReturnType methodName() { ... }

// 3. Call parent constructor (FIRST LINE of child constructor)
super(param1, param2);

// 4. Call parent method
super.methodName();

// 5. Static field access
ClassName.staticField++;

// 6. Linked list traversal
Node<K,V> current = head;
while (current != null) {
    // do something with current.key / current.value
    current = current.next;
}
```