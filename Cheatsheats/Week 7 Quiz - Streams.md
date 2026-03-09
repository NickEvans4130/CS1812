## 1. Functional Interfaces — The Big 4

> A **functional interface** = an interface with exactly **one abstract method**. Can be implemented with a lambda.

|Interface|Parameters|Returns|Abstract Method|
|---|---|---|---|
|`Runnable`|none|`void`|`void run()`|
|`Supplier<T>`|none|`T`|`T get()`|
|`Consumer<T>`|`T`|`void`|`void accept(T t)`|
|`Function<T,R>`|`T`|`R`|`R apply(T t)`|

### Memory Trick

```
Runnable   →  () -> void       "just runs, nothing in, nothing out"
Supplier   →  () -> value      "supplies something out of thin air"
Consumer   →  (x) -> void      "consumes input, gives nothing back"
Function   →  (x) -> value     "transforms input into output"
```

### Lambda Syntax Examples

```java
Runnable  r = () -> System.out.println("hello");   // no params, void
Supplier<String> s = () -> "hello";                // no params, returns value
Consumer<String> c = (x) -> System.out.println(x); // one param, void
Function<String, Integer> f = (x) -> x.length();   // one param, returns value
```

### Matching Lambdas to Interfaces — Exam Technique

```
Step 1: Count the parameters  →  0 params = Runnable or Supplier
Step 2: Check the return type →  void     = Runnable or Consumer
                                 value    = Supplier or Function

() -> void    = Runnable
() -> value   = Supplier
(x) -> void   = Consumer
(x) -> value  = Function
```

---

## 2. Stream Pipeline Structure

> A stream pipeline has exactly **3 parts**: source → intermediate(s) → terminal

```
list.stream()          →      .filter().map()...      →      .collect()
   SOURCE                     INTERMEDIATE(S)                 TERMINAL
 returns Stream<T>           returns Stream<T>            ends the stream
                              can chain many               only ONE allowed
```

### Full Example

```java
List<String> result = list.stream()              // source
    .filter(s -> s.length() > 3)                 // intermediate
    .map(s -> s.toUpperCase())                   // intermediate
    .sorted()                                    // intermediate
    .collect(Collectors.toList());               // terminal
```

---

## 3. Intermediate vs Terminal — Full Reference

> **Intermediate** = takes Stream, returns Stream (chainable) **Terminal** = takes Stream, returns something else (ends pipeline)

|Method|Type|Returns|Used For|
|---|---|---|---|
|`filter(Predicate)`|Intermediate|`Stream<T>`|Remove non-matching elements|
|`map(Function)`|Intermediate|`Stream<R>`|Transform each element|
|`sorted()`|Intermediate|`Stream<T>`|Sort elements|
|`limit(n)`|Intermediate|`Stream<T>`|Keep first n elements|
|`distinct()`|Intermediate|`Stream<T>`|Remove duplicates|
|`forEach(Consumer)`|**Terminal**|`void`|Do something with each element|
|`collect(Collector)`|**Terminal**|`List/Set/Map`|Gather into a collection|
|`min(Comparator)`|**Terminal**|`Optional<T>`|Find smallest|
|`max(Comparator)`|**Terminal**|`Optional<T>`|Find largest|
|`count()`|**Terminal**|`long`|Count elements|
|`findFirst()`|**Terminal**|`Optional<T>`|Get first element|

---

## 4. Key Operations Explained

### `filter()` — removes elements

```java
// keeps only elements where the condition is TRUE
stream.filter(x -> x > 3)

// Stream<String> → Stream<String> (same type, fewer elements)
Stream.of("hi", "apple", "banana").filter(s -> s.length() > 3)
// result: ["apple", "banana"]   ("hi" removed)
```

### `map()` — transforms elements

```java
// converts every element using a Function<T,R>
stream.map(x -> x * 2)

// Stream<String> → Stream<Integer> (TYPE CAN CHANGE, same number of elements)
Stream.of("hi", "apple", "banana").map(s -> s.length())
// result: [2, 5, 6]   (every element transformed)
```

### filter() vs map() — Key Difference

```
filter → same type, FEWER elements    (removes things)
map    → can change type, SAME count  (transforms things)
```

### `min()` / `max()` — returns `Optional<T>`

```java
// Returns Optional because the stream might be empty
Optional<Integer> smallest = Stream.of(3,1,4).min(Comparator.naturalOrder());
smallest.get();        // → 1
smallest.isPresent();  // → true

Stream.<Integer>empty().min(Comparator.naturalOrder()).isPresent(); // → false
```

### `forEach()` — runs a Consumer, returns void

```java
// Terminal — ends the stream, returns nothing
stream.forEach(x -> System.out.println(x));
stream.forEach(System.out::println);  // method reference shorthand
```

### `collect()` — gathers into a collection

```java
List<String> list   = stream.collect(Collectors.toList());
Set<String>  set    = stream.collect(Collectors.toSet());
String       joined = stream.collect(Collectors.joining(", "));
```

---

## 5. `Optional<T>` — 3 Key Methods

```java
optional.isPresent()     // true if a value exists, false if empty
optional.get()           // returns the value — CRASHES if empty!
optional.orElse(default) // returns value, or default if empty — SAFEST
```

```java
// Safe pattern:
Optional<Integer> result = stream.min(Comparator.naturalOrder());
if (result.isPresent()) {
    System.out.println(result.get());
}
// Or in one line:
System.out.println(result.orElse(-1));
```

---

## 6. Method References — Shorthand for Lambdas

```java
// Lambda              →   Method Reference
x -> System.out.println(x)  →  System.out::println
s -> s.toUpperCase()         →  String::toUpperCase
x -> new Foo(x)              →  Foo::new
```

---

## 7. Quick-Fire Exam Rules

```
✓ Runnable    = no args, void         ✓ Supplier  = no args, returns value
✓ Consumer    = one arg, void         ✓ Function  = one arg, returns value

✓ filter()    = intermediate (Stream → Stream)
✓ map()       = intermediate (Stream → Stream, type can change)
✓ forEach()   = terminal     (Stream → void)
✓ collect()   = terminal     (Stream → List/Set/Map)
✓ min()/max() = terminal     (Stream → Optional<T>)

✓ stream()    = SOURCE, not intermediate or terminal
✓ Optional    = wraps a value that might not exist — use .orElse() to be safe
✓ One terminal per pipeline — it ends the stream, you can't chain after it
```