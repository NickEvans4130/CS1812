# Searching & Hash Tables
**CS1812 – Object Oriented Programming II**
Tags: #java #hashtables #searching #algorithms #oop #cs1812

---

## Searching Overview

Most data structures support adding and removing data well, but how do we efficiently **find** a particular value?

---

## Linear Search — O(n)

Compare against every element in turn.

```java
static boolean linearSearch(int[] arr, int val) {
    for (int x : arr) {
        if (x == val) return true;
    }
    return false;
}
```

- **n** elements → on the order of **n** comparisons in the worst case
- Complexity: **O(n)** — linear

---

## Binary Search — O(log n)

Requires the array to be **sorted**. Halves the search space at each step.

**Idea:** compare against the middle element → value must be in the left or right half → recurse.

```java
static boolean binarySearch(int[] arr, int val, int start, int end) {
    if (start > end) return false;
    int middle = (start + end) / 2;
    if (arr[middle] == val) {
        return true;
    } else if (val < arr[middle]) {
        return binarySearch(arr, val, start, middle - 1);
    } else {
        return binarySearch(arr, val, middle + 1, end);
    }
}
```

- Each step halves the problem → at most $\log_2(n)$ steps
- Complexity: **O(log₂ n)** — logarithmic

---

## Lookup Tables — O(1)

If the range of values is small and known (e.g. 0–29), use a direct-index array:

- Create array of size = range
- At index `x`, store how many times `x` appears

```
Index:  0  1  2  3  4  5  6  7  8  ...  17  18  ...  24  ...  29
Value:  0  0  1  0  0  0  3  0  0  ...   2   0  ...   1  ...   1
```

- Lookup is a single array access — independent of how many elements are stored
- Complexity: **O(1)** — constant

---

## Hash Tables — O(1) (amortised)

Generalises lookup tables to **arbitrary values**.

### Core Idea

1. Create an array of fixed size **L** — the **hash table**
2. Define a **hash function** `h(k)` that maps any key `k` → integer in `[0, L-1]`
3. Insert/lookup `k` at position `h(k)`

Example: `h(k) = k mod 13`
- `h(42)` = 3, `h(1024)` = 10, `h(1337)` = 11

### Handling Collisions

Hash functions are **not injective** — two different keys can share the same hash value (a **collision**). This is normal and expected.

**Solution:** each array slot holds a **linked list (bucket)**:

```
Index 3:  [ 55 ] → [ 42 ] → null
Index 10: [ 1024 ] → null
Index 11: [ 1337 ] → null
```

- **Insert:** compute `h(k)`, prepend to the bucket list
- **Search:** compute `h(k)`, iterate through the bucket list comparing with `equals()`
- By keeping the table well-sized so lists stay short → operations are effectively **O(1)**

---

## Hash Functions

A hash function assigns every value an integer that "approximates" it for indexing purposes:

```
hash(23)  = 23
hash(35)  = 5
hash(60)  = 0
hash(65)  = 5   ← collision with hash(35)
```

- Collisions are normal and handled via buckets
- Real hash functions are more complex than simple modulo

---

## Java Hash Table Collections

### HashSet

Implements `Set` — stores **unique** elements, backed by a hash table.

```java
import java.util.HashSet;

HashSet<String> s = new HashSet<>();
s.add("Hello");
s.add("World");
s.add("!");
s.add("Hello");          // duplicate — ignored

System.out.println(s.size());           // 3
System.out.println(s.contains("Hello")); // true

s.remove("!");
System.out.println(s);                  // [Hello, World]

// Iteration (order not guaranteed)
for (String str : s) {
    System.out.println("Element: " + str);
}
```

### HashMap

Implements `Map<K,V>` — maps **unique keys** to values.

```java
import java.util.HashMap;

HashMap<String, Integer> numbers = new HashMap<>();
numbers.put("one",   1);
numbers.put("two",   2);
numbers.put("three", 3);

Integer n = numbers.get("two");   // returns null if key absent
if (n != null) {
    System.out.println("two = " + n);
}

if (numbers.containsKey("three")) {
    System.out.println("three = " + numbers.get("three"));
}
```

---

## hashCode() and equals()

Java's generic hash tables rely on two methods inherited from `java.lang.Object`:

| Method | Default Behaviour | Purpose |
|---|---|---|
| `hashCode()` | Returns memory address as int | Determines which bucket |
| `equals()` | Compares memory addresses | Identifies matching element within bucket |

### API Contract (must obey!)

- **Symmetry:** if `o1.equals(o2)` then `o2.equals(o1)`
- **Hash consistency:** if `o1.equals(o2)` then `o1.hashCode() == o2.hashCode()`

> [!important]
> If you override `equals()`, you **must** also override `hashCode()`. Failure to do so breaks hash-based collections.

### Example — StudentRecord

```java
public class StudentRecord {
    private long id;
    private String firstName, lastName;

    public boolean equals(Object o) {
        if (o == null) return false;
        if (this.getClass() != o.getClass()) return false;
        StudentRecord other = (StudentRecord) o;
        return id == other.id;   // equality based on id only
    }

    public int hashCode() {
        return (int)((id >> 32) ^ id);  // hash based on id only
    }

    public String toString() {
        return id + ": " + firstName + " " + lastName;
    }
}
```

```java
HashSet<StudentRecord> enrolled = new HashSet<>();
StudentRecord stu  = new StudentRecord(1234, "Jane", "Doe");
StudentRecord stu2 = new StudentRecord(2000, "John", "Foo");
enrolled.add(stu);
enrolled.add(stu2);

// Same id as stu2 → equals() returns true → NOT added
StudentRecord stu3 = new StudentRecord(2000, "Jack", "Black");
enrolled.add(stu3);
System.out.println(enrolled); // still just Jane and John
```

---

## DIY Multimap — Nested Collections

A `HashMap<String, Set<StudentRecord>>` maps each course code to a set of enrolled students.

```java
public class CourseRegister {
    private Map<String, Set<StudentRecord>> register = new HashMap<>();

    public void put(String courseCode, StudentRecord record) {
        Set<StudentRecord> enrolled = register.get(courseCode);
        if (enrolled == null) {
            enrolled = new HashSet<>();
            register.put(courseCode, enrolled);
        }
        enrolled.add(record);
    }

    public Set<StudentRecord> get(String courseCode) {
        return register.get(courseCode);
    }
}
```

---

## Complexity Summary

| Algorithm / Structure | Time Complexity | Requires |
|---|---|---|
| Linear Search | O(n) | Nothing |
| Binary Search | O(log n) | Sorted array |
| Lookup Table | O(1) | Small, bounded integer range |
| Hash Table (insert/search/remove) | O(1) amortised | Good hash function, proper sizing |

---

## When to Use HashSet vs HashMap

**HashSet** — when you need a set of unique elements and order doesn't matter.

**HashMap** — when you need to associate keys with values.

**Disadvantages of hash tables:**
- No guaranteed ordering (iteration order may change)
- Overhead not worth it for small collections
- Must implement `hashCode()` and `equals()` correctly for custom objects
