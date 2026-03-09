# Java Collections
**CS1812 – Object Oriented Programming II**
Tags: #java #collections #oop #cs1812

---

## Overview

Java provides many built-in data structure implementations via the **Java Collections Framework**. All interfaces and implementations use generics.

Three categories:

| Category | Description |
|---|---|
| **Ordered Lists** | Insertion/removal in a specified order |
| **Maps (Dictionaries)** | Associated key–value pairs |
| **Unordered Lists (Sets)** | Unique elements, supports iteration |

---

## Key Interfaces

| Interface | Description |
|---|---|
| `List<T>` | Sequence of elements, freely accessible by index |
| `Queue<T>` | First-in, first-out |
| `Deque<T>` | Double-ended queue — acts as both stack and queue |
| `Set<T>` | Unique objects, no guaranteed order |
| `Map<K,V>` | Maps unique keys to (not necessarily unique) values |

---

## Key Implementations

| Class | Implements |
|---|---|
| `ArrayList<T>` | `List` |
| `Vector<T>` | `List` |
| `LinkedList<T>` | `List`, `Deque` |
| `ArrayDeque<T>` | `Deque` |
| `TreeSet<T>` | `Set` (sorted) |
| `HashSet<T>` | `Set` (hash-based) |
| `HashMap<K,V>` | `Map` (hash-based) |
| `TreeMap<K,V>` | `Map` (sorted) |

---

## ArrayList

Backed by a resizable array. Almost always preferable to raw arrays.

**Use raw arrays only when:**
- Size is known in advance
- Storing primitive types (`int`, `float`, etc.)
- Element position has semantic meaning

### Common Operations

```java
import java.util.ArrayList;

ArrayList<String> list = new ArrayList<String>();

list.add("Hello");           // append
list.add("World");
String s = list.get(1);      // direct access by index
list.set(1, "World!");       // replace element

list.remove("Hello");        // remove by value
String t = list.remove(0);   // remove by index (returns removed element)

list.size();                 // number of elements
list.contains(x);            // membership test
list.toArray(new T[list.size()]); // convert to array
System.out.println(list);    // toString() works properly
```

> [!note] `remove` Overloading
> `remove(int i)` removes by index; `remove(Object o)` removes by value. Be careful with `Integer` lists — use `remove(Integer.valueOf(i))` to remove by value, not index.

---

## LinkedList

Java's doubly-linked list (no built-in singly-linked list). Implements both `List` and `Deque`.

**vs ArrayList:**
- Insertion/removal at head/tail is more predictable (no shifting)
- Random access (`.get(i)`) is much slower — O(n)

```java
import java.util.LinkedList;

LinkedList<String> list = new LinkedList<String>();

list.add("Hello");       // add to tail
list.add("World");

// Stack operations (top = head)
list.push("Big");        // prepend
String s = list.pop();   // remove from head

// Access
String first = list.getFirst();
String last  = list.getLast();
```

---

## For-Each Loops

Works with **all Java collections and arrays**:

```java
// Verbose form
for (int i = 0; i < accounts.size(); i++) {
    BankAccount a = accounts.get(i);
    sum += a.getBalance();
}

// Equivalent for-each form
for (BankAccount a : accounts) {
    sum += a.getBalance();
}
```

---

## Selection Sort (ArrayList Example)

A practical demonstration of `ArrayList` methods.

**Algorithm:** repeatedly find the minimum element in the unsorted portion and swap it to the front.

```java
// Find index of minimum from position 'start' onwards
private static int findMinIndex(ArrayList<Integer> list, int start) {
    int minIdx = start;
    for (int i = start; i < list.size(); i++) {
        if (list.get(i) < list.get(minIdx)) {
            minIdx = i;
        }
    }
    return minIdx;
}

// Selection sort
public static void sort(ArrayList<Integer> list) {
    for (int i = 0; i < list.size(); i++) {
        int minIndex = findMinIndex(list, i);
        // swap
        Integer temp = list.get(i);
        list.set(i, list.get(minIndex));
        list.set(minIndex, temp);
    }
}
```

### Complexity Analysis

- `findMinIndex` from position `start` takes `n - start` steps
- Total steps across all iterations: $n + (n-1) + (n-2) + \ldots + 1 = \dfrac{n(n+1)}{2}$
- Grows proportionally to $n^2$ → **O(n²)** — quadratic

---

## Collection Hierarchy (Reference)

```
Iterable
 └── Collection
      ├── Set
      │    ├── SortedSet → NavigableSet
      │    └── (AbstractSet) → TreeSet, HashSet
      ├── List
      │    └── (AbstractList) → ArrayList, Vector → Stack
      └── Queue
           ├── Deque → (AbstractSequentialList) → LinkedList
           └── (AbstractQueue) → PriorityQueue

Map (separate hierarchy)
 ├── AbstractMap → HashMap → LinkedHashMap
 ├── AbstractMap → TreeMap
 └── Dictionary → Hashtable → Properties
```
