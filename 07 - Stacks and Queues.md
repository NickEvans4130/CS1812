# Stacks and Queues
*CS1812: Object Oriented Programming II*

---

## Data Structures for Sets

**Motivation:** Algorithms often need to represent sets of values that support operations like add, search, delete, etc.

> A **data structure** is a particular way of representing a given kind of value.

### Classifying Data Structures

Data structures are distinguished by:
- Their **supported operations**
- The **complexity** (efficiency) of those operations

| Category | Examples |
|---|---|
| Fixed sets | objects, records, arrays |
| Dynamic sets | lists, trees, arrays, hash tables |

### Standard Operations

| Operation | Description |
|---|---|
| `find(k)` | Look for element `k` |
| `insert(k)` | Add element `k` |
| `remove(k)` | Delete element `k` |
| `min()` / `max()` | Return minimum/maximum element |
| `next(k)` / `previous(k)` | Return element after/before `k` |

---

## Stacks and Queues Overview

Stacks and queues are data structures **specialised for adding and removing elements** — they constrain the order in which this can be done.

Key use cases:
- **Stacks** → depth-first search (DFS)
- **Queues** → breadth-first search (BFS)

---

## Stack — LIFO

> **Last-In-First-Out** — the last element added is the first removed. Like a stack of cards.

| Operation | Description |
|---|---|
| `push(x)` | Add element to the **top** |
| `pop()` | Remove and return the **top** element |
| `peek()` | Read the top element **without** removing it (optional) |

### Array-Based Stack

Elements stay in place; only the `top` index changes.

```java
class StringStack {
    String[] arr;
    int top; // index of topmost element + 1

    public StringStack() {
        arr = new String[10];
        top = 0;
    }

    public boolean isEmpty() {
        return (top == 0);
    }

    public void push(String s) {
        arr[top] = s;
        top = top + 1;
    }

    public String pop() {
        top = top - 1;
        return arr[top];
    }
}
```

> [!warning]
> Array-based stacks have a **fixed size** — they can overflow if you push too many elements.

---

## Queue — FIFO

> **First-In-First-Out** — the first element added is the first removed. Like a queue of people.

| Operation | Description |
|---|---|
| `enqueue(x)` | Add element to the **back** |
| `dequeue()` | Remove and return element from the **front** |

### Array-Based Queue

Uses **wrap-around** indexing to avoid wasting space.

```java
class StringQueue {
    String[] arr;
    int front, back;

    public StringQueue() {
        arr = new String[10];
        front = 0;
        back = 0;
    }

    public void enqueue(String s) {
        arr[back] = s;
        back = back + 1;
        if (back == arr.length) { // wrap around
            back = 0;
        }
    }

    public String dequeue() {
        String s = arr[front];
        front = front + 1;
        if (front == arr.length) { // wrap around
            front = 0;
        }
        return s;
    }
}
```

---

## Lists Instead of Arrays

Array-based stacks/queues have a **fixed size** — they can overflow. Solutions:
- Detect overflow and throw an exception
- Allocate a new larger array and copy elements
- Use a **linked list**, which is designed to grow dynamically ✅

---

## Stack with Linked List

Each node holds a **payload** and a reference to the **next** node. The stack just tracks the `top` node.

```java
class StringNode {
    StringNode next;
    String payload;

    public StringNode(String s) {
        payload = s;
        next = null;
    }
}

public class StringListStack {
    StringNode top;

    public StringListStack() {
        top = null;
    }

    public void push(String s) {
        StringNode n = new StringNode(s);
        n.next = top; // new node links to old top
        top = n;      // new node is now the top
    }

    public String pop() {
        String s = top.payload;  // read top payload
        top = top.next;          // second node becomes top
        return s;
    }
}
```

---

## Queue with Linked List

**Problem:** A singly linked list tracks only `next`. For a queue we need to dequeue from the front — but how do we find the node *before* the front to update it?

**Solution: Doubly Linked List** — each node has both `next` and `prev` pointers.

```
Back ←→ [Node] ←→ [Node] ←→ [Node] → null  : Front
```

### Doubly Linked Node

```java
class StringNode {
    StringNode next;
    StringNode prev;
    String payload;
}
```

### Queue with Doubly Linked List

```java
public class StringListQueue {
    StringNode back;
    StringNode front;

    public StringListQueue() {
        back = null;
        front = null;
    }

    public void enqueue(String s) {
        StringNode n = new StringNode(s);
        if (back == null) {      // queue was empty
            front = n;
        } else {
            n.next = back;       // link new node to old back
            back.prev = n;       // tell old back what's behind it
        }
        back = n;                // new node is now at the back
    }

    public String dequeue() {
        String s = front.payload;  // read payload from front
        front = front.prev;        // second node becomes front
        if (front == null) {       // list is now empty
            back = null;
        } else {
            front.next = null;     // remove reference to old front
        }
        return s;
    }
}
```

---

## Summary: Stack vs Queue

| | Stack | Queue |
|---|---|---|
| Order | LIFO | FIFO |
| Add | `push` (top) | `enqueue` (back) |
| Remove | `pop` (top) | `dequeue` (front) |
| Array impl | Single `top` index | `front` and `back` indices with wrap-around |
| Linked list | Singly linked | **Doubly** linked (need `prev` for dequeue) |
| Use case | DFS, call stack, undo | BFS, scheduling, buffers |

---

## Tags
#cs1812 #java #oop #datastructures #stacks #queues #linkedlists
