# Enumerated Types
*CS1812: Object Oriented Programming II*

---

## Types Recap

Every variable has a type. The type determines possible values and applicable operations:

| Type | Possible Values | Operations/Methods |
|---|---|---|
| `int` | `{..., -1, 0, 1, 2, ...}` | `+ - * /` |
| `boolean` | `{true, false}` | `&& \|\| ! & \|` |
| `double` | `{..., -1.32e4, 3.14e0, ...}` | `+ - * /` |
| `String` | `{..., "a", "b", ...}` | `.append()`, `.indexOf()` |
| `int[2]` | `{..., [-4, 2], ...}` | `[]`, `.length` |

---

## Enum: Definition

An **enum type** `E` is a type that corresponds to a **set of constants** $S_E$. A variable of type `E` can only hold values from $S_E$.

```java
public enum Day {
    MON, TUE, WED, THU, FRI, SAT, SUN;
}

public enum Direction {
    NORTH, EAST, WEST, SOUTH;
}
```

**Usage:**
```java
Day today = Day.TUE;
Direction dest = Direction.NORTH;
```

### Conventions
- Capitalise the **type name**, like a class → `Day`, `Direction`
- Write **members in ALL_CAPS**, like constants → `MON`, `NORTH`

---

## Using Enums in Switch Statements

```java
public static void feeling(Day day) {
    switch (day) {
        case MON:
            System.out.println("I hate Mondays.");
            break;
        case FRI:
            System.out.println("Fridays are better.");
            break;
        case SAT: case SUN:
            System.out.println("Weekends are best.");
            break;
        default:
            System.out.println("Midweek days are so-so.");
            break;
    }
}
```

---

## Advantages of Enums

- **Separate namespaces** → `Day.SUN` vs `Star.SUN` — no conflicts
- **Type safety** → cannot pass invalid values like `feeling(new Day("Octidi"))`
- **Precise type checking** → `feeling(Direction.NORTH)` would be a compile error
- **Unique members** → unlike static objects, each constant is guaranteed unique

---

## Enum Types Are Classes

Enum members are **instances** of their type. E.g., `NORTH`, `SOUTH`, `EAST`, `WEST` are instances of `Direction`.

You can add **methods and fields** to enums:

```java
public enum Direction {
    NORTH, SOUTH, EAST, WEST;

    public String toString() {
        return "Direction: " + name(); // name() is a built-in method
    }

    public boolean isCold() {
        return this == NORTH;
    }
}
```

---

## Built-in Enum Methods

### Static Methods
| Method | Description |
|---|---|
| `values()` | Returns an array of all enum members |
| `valueOf(String s)` | Returns the member with name `s` |

### Instance Methods
| Method | Description |
|---|---|
| `name()` | Returns the declared name of this member |
| `ordinal()` | Returns a unique integer for this member (position) |

---

## Custom Constructors

Enums can have **custom constructors** with additional arguments to initialise extra fields.

> [!note] Syntax
> Fields and constructors of enums are **private**. Use `NAME(arg)` syntax when declaring members.

```java
enum Animal {
    DOG("Woof"),
    CAT("Meow"); // invokes constructor with argument

    private String sound;

    private Animal(String s) {
        sound = s;
    }

    public String toString() {
        return sound;
    }
}

public class AnimalTest {
    public static void main(String[] args) {
        Animal a = Animal.DOG;
        System.out.println(a); // prints: Woof
    }
}
```

---

## Tags
#cs1812 #java #oop #enums #datatypes
