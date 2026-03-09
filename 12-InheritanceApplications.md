# Inheritance in Practice
**CS1812: Object Oriented Programming II**
*Prof Matthew Hague and Dr Matteo Sammartino*

---

## Tags
#cs1812 #java #oop #inheritance #interfaces #design-patterns

---

## The Observer Pattern

### Problem Scenario

Three actors need to observe a thermometer's temperature:
- **Runner 🏃** — needs current temperature to decide what to wear
- **Scientist 🧑‍🔬** — tracks the average temperature over time
- **Forecaster 🧑‍🏫** — tracks temperature changes to predict trends

### Bad Implementation ❌

```java
class Thermometer {
    Runner runner;
    Scientist scientist;
    Forecaster forecaster;

    public void setTemperature(int temperature) {
        runner.update(temperature);
        scientist.update(temperature);
        forecaster.update(temperature);
    }
}
```

**Problems:**
- The `Thermometer` must know about every observer upfront
- Adding a new watcher requires modifying the `Thermometer` class
- Cannot be reused in different situations

---

### Better Implementation: Observer Pattern ✅

**Core idea:** Define an `Observer` interface. The thermometer only needs to know about the interface, not specific classes.

```java
interface Observer {
    void update(int temperature);
}
```

#### Observer Implementations

```java
class Runner implements Observer {
    @Override
    public void update(int temperature) {
        if (temperature < 13)
            System.out.println("(runner) I need long sleeves");
        else
            System.out.println("(runner) I'll wear a t-shirt");
    }
}

class Scientist implements Observer {
    private int temperatureSum = 0;
    private int count = 0;

    @Override
    public void update(int temperature) {
        count += 1;
        temperatureSum += temperature;
        int mean = temperatureSum / count;
        System.out.println("(scientist) The mean is " + mean);
    }
}

class Forecaster implements Observer {
    private int previousTemperature = 0;
    private boolean previousSeen = false;

    @Override
    public void update(int temperature) {
        if (!previousSeen)
            System.out.println("(forecaster) Waiting for data");
        else if (previousTemperature < temperature)
            System.out.println("(forecaster) It will be warmer");
        else
            System.out.println("(forecaster) It will be colder");
        previousSeen = true;
        previousTemperature = temperature;
    }
}
```

#### The Thermometer (Observable)

> 💡 The `Thermometer` does **not** need to know anything specific about the observers.

```java
class Thermometer {
    private List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    public void setTemperature(int temperature) {
        System.out.println("The temperature is now " + temperature);
        for (Observer observer : observers)
            observer.update(temperature);
    }
}
```

> 📦 The `Observer` interface and `Thermometer` class can be reused in any project.

#### Main Method

```java
public class Main {
    public static void main(String[] args) {
        Runner runner = new Runner();
        Scientist scientist = new Scientist();
        Forecaster forecaster = new Forecaster();
        Thermometer thermometer = new Thermometer();

        thermometer.addObserver(runner);
        thermometer.addObserver(scientist);
        thermometer.addObserver(forecaster);

        thermometer.setTemperature(10);
        thermometer.setTemperature(15);
    }
}
```

**Output:**
```
The temperature is now 10
(runner) I need long sleeves
(scientist) The mean is 10
(forecaster) Waiting for data
The temperature is now 15
(runner) I'll wear a t-shirt
(scientist) The mean is 12
(forecaster) It will be warmer
```

---

## Using Inheritance to Avoid Code Duplication

### Problem: Adding a Wind Vane 🍃

If we add a `WindVane`, we'd duplicate the observer list logic:

```java
enum Direction { NORTH, SOUTH, EAST, WEST; }

class WindVane {
    private List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    public void setDirection(Direction direction) {
        for (Observer observer : observers)
            observer.update(direction);
    }
}
```

🔍 This is almost identical to `Thermometer` — a sign to use **inheritance**.

### Extracting a Common `Observable` Base Class

> 💡 Shared code is a sign to use inheritance — define a base class with the common features.

The `Observable` class should:
- Have a concept of an observer
- Maintain a list of observers
- Allow observers to be registered
- Notify all observers of new values

#### Generic Observer Interface

To handle both `int` and `Direction`, use **generics**:

```java
interface Observer<T> {
    void update(T value);
}
```

#### Generic Observable Base Class

```java
class Observable<T> {
    private List<Observer<T>> observers = new ArrayList<>();

    public void addObserver(Observer<T> observer) {
        observers.add(observer);
    }

    // Note: should this be protected rather than public?
    public void notifyObservers(T value) {
        for (Observer observer : observers)
            observer.update(value);
    }
}
```

> 🤔 Consider: should `Observable` be `abstract`? Should `notifyObservers` be `protected` (so only subclasses can call it)?

#### Refactored Weather Sensors

```java
class Thermometer extends Observable<Integer> {
    public void setTemperature(int temperature) {
        System.out.println("The temperature is now " + temperature);
        notifyObservers(temperature);
    }
}

class WindVane extends Observable<Direction> {
    public void setDirection(Direction direction) {
        System.out.println("The wind direction is now " + direction);
        notifyObservers(direction);
    }
}
```

No repeated code! ✅

---

## Caveats: Java's Observer in the API

> ⚠️ Java's built-in `Observable` class and `Observer` interface have been **deprecated** in favour of more robust alternatives.

### Single Inheritance Limitation

A key drawback: **Java only allows inheriting from one class**.

- If `Thermometer` already extends `WeatherEquipment`, it **cannot** also extend `Observable`.
- Solution: design the hierarchy so `WeatherEquipment` itself inherits from `Observable`.
- Class hierarchies must be designed carefully!

---

## Summary

| Concept | Purpose |
|---|---|
| `Observer` interface | Decouples observables from concrete observer classes |
| `Observable<T>` base class | Eliminates duplicate observer management code |
| Generics `<T>` | Allows the pattern to work for any value type |

- **Interfaces** → flexible, decoupled implementations
- **Inheritance** → avoid code repetition, provide shared functionality

> 📚 Revisit JavaFX widgets — they make heavy use of both inheritance and interfaces.
