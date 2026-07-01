Enums are a favorite topic in **Senior Java interviews** because they're much more powerful than just a list of constants. An enum in Java is actually a **special class** that can have fields, methods, constructors, and even implement interfaces.

---

# What is an Enum?

An enum represents a fixed set of constants.

Instead of

```java
public static final int RED = 1;
public static final int GREEN = 2;
public static final int BLUE = 3;
```

Use

```java
enum Color {
    RED,
    GREEN,
    BLUE
}
```

Usage

```java
Color color = Color.RED;

if (color == Color.RED) {
    System.out.println("Red");
}
```

Enums are **type-safe**.

You cannot do

```java
Color color = "RED";    // ❌ Compile Error
```

---

# Enum is Actually a Class

This surprises many interview candidates.

```java
enum Day {
    MONDAY,
    TUESDAY
}
```

Internally it behaves like

```java
final class Day extends Enum<Day> {
    public static final Day MONDAY;
    public static final Day TUESDAY;
}
```

Every enum implicitly extends `Enum<E>`.

Since Java doesn't support multiple inheritance, an enum **cannot extend another class**, but it **can implement interfaces**.

---

# Enum with Fields

Enums can have instance variables.

Example

```java
enum Status {

    SUCCESS(200),
    NOT_FOUND(404),
    ERROR(500);

    private int code;

    Status(int code) {
        this.code = code;
    }

    public int getCode() {
        return code;
    }
}
```

Usage

```java
System.out.println(Status.SUCCESS.getCode());
```

Output

```text
200
```

---

# Enum Constructors

Constructors in enums are always **private** (explicitly or implicitly).

You cannot create enum objects using `new`.

```java
enum Direction {

    NORTH,
    SOUTH;

    Direction() {
        System.out.println("Created");
    }
}
```

Java creates the enum instances only once when the enum class is initialized.

---

# Enum Methods

Enums can define methods.

```java
enum TrafficLight {

    RED,
    GREEN,
    YELLOW;

    public void action() {

        switch(this){

            case RED:
                System.out.println("Stop");
                break;

            case GREEN:
                System.out.println("Go");
                break;

            case YELLOW:
                System.out.println("Slow");
                break;
        }
    }
}
```

Usage

```java
TrafficLight.GREEN.action();
```

Output

```text
Go
```

---

# Enum with Abstract Methods

Each constant can provide its own implementation.

```java
enum Operation {

    ADD {
        public int apply(int a, int b) {
            return a + b;
        }
    },

    SUBTRACT {
        public int apply(int a, int b) {
            return a - b;
        }
    };

    public abstract int apply(int a, int b);
}
```

Usage

```java
System.out.println(Operation.ADD.apply(10, 20));
```

Output

```text
30
```

This is a clean alternative to large `switch` statements.

---

# Useful Built-in Methods

```java
enum Color {

    RED,
    GREEN,
    BLUE
}
```

### `values()`

```java
for(Color c : Color.values()){

    System.out.println(c);

}
```

Output

```text
RED
GREEN
BLUE
```

---

### `valueOf()`

```java
Color c = Color.valueOf("GREEN");
```

Returns

```text
GREEN
```

Throws `IllegalArgumentException` if the name doesn't match exactly.

---

### `ordinal()`

```java
System.out.println(Color.RED.ordinal());
```

Output

```text
0
```

Avoid using `ordinal()` for business logic because changing the enum declaration order changes the values.

---

### `name()`

```java
System.out.println(Color.RED.name());
```

Output

```text
RED
```

---

# Implementing Interfaces

```java
interface Printable {

    void print();
}

enum DocumentType implements Printable {

    PDF,
    WORD;

    @Override
    public void print() {
        System.out.println(name());
    }
}
```

---

# EnumSet

`EnumSet` is a specialized `Set` implementation designed exclusively for enum types.

It is much faster and more memory-efficient than a general-purpose `HashSet` because it uses bit vectors internally.

Example

```java
enum Day {

    MON,
    TUE,
    WED,
    THU,
    FRI,
    SAT,
    SUN
}
```

```java
EnumSet<Day> workingDays = EnumSet.of(
    Day.MON,
    Day.TUE,
    Day.WED,
    Day.THU,
    Day.FRI
);

System.out.println(workingDays);
```

Output

```text
[MON, TUE, WED, THU, FRI]
```

---

## Common Factory Methods

### `allOf()`

```java
EnumSet.allOf(Day.class)
```

Returns all enum constants.

---

### `noneOf()`

```java
EnumSet.noneOf(Day.class)
```

Creates an empty `EnumSet`.

---

### `range()`

```java
EnumSet.range(Day.MON, Day.FRI)
```

Returns

```text
MON TUE WED THU FRI
```

---

### `complementOf()`

```java
EnumSet weekends =
        EnumSet.complementOf(workingDays);
```

Output

```text
SAT SUN
```

---

# EnumMap

`EnumMap` is a specialized `Map` whose keys are enum constants.

It is typically faster and more memory-efficient than a `HashMap` when the keys are enums because it stores values in an array indexed by the enum's ordinal.

```java
EnumMap<Day, String> schedule =
        new EnumMap<>(Day.class);

schedule.put(Day.MON, "Meeting");
schedule.put(Day.TUE, "Coding");

System.out.println(schedule.get(Day.MON));
```

Output

```text
Meeting
```

---

# Why Use EnumMap Instead of HashMap?

| EnumMap                    | HashMap                   |
| -------------------------- | ------------------------- |
| Enum keys only             | Any object keys           |
| Array-based implementation | Hash table implementation |
| Faster for enum keys       | General-purpose           |
| Lower memory overhead      | Higher memory overhead    |

---

# Real-world Example

```java
enum Role {

    ADMIN,
    USER,
    GUEST
}

EnumMap<Role, String> permissions =
        new EnumMap<>(Role.class);

permissions.put(Role.ADMIN, "ALL");
permissions.put(Role.USER, "LIMITED");
permissions.put(Role.GUEST, "READ");
```

This avoids string-based keys and improves type safety.

---

# Enum vs Constants Class

Instead of

```java
class Status {

    public static final int OPEN = 1;
    public static final int CLOSED = 2;
}
```

Use

```java
enum Status {

    OPEN,
    CLOSED
}
```

Benefits

* Type-safe
* Readable
* Supports fields and methods
* Prevents invalid values
* Easier to maintain

---

# Common Interview Questions

### Why are enums better than `public static final` constants?

Enums provide compile-time type safety, can encapsulate data and behavior, support methods and interfaces, and prevent invalid values from being assigned.

---

### Why is the enum constructor private?

Java guarantees that each enum constant is a singleton instance. A private constructor prevents external code from creating additional instances.

---

### Why are `EnumSet` and `EnumMap` faster?

They exploit the fixed, known set of enum constants:

* `EnumSet` uses a bit vector internally for set operations.
* `EnumMap` uses an array indexed by each enum constant's ordinal.

This makes them more efficient than hash-based collections for enum types.

---

### Should you use `ordinal()`?

Generally, **no**. `ordinal()` depends on the declaration order, so inserting or reordering enum constants changes the values and can break persisted data or business logic. If you need a stable identifier, define an explicit field:

```java
enum Priority {
    LOW(1),
    MEDIUM(2),
    HIGH(3);

    private final int level;

    Priority(int level) {
        this.level = level;
    }

    public int getLevel() {
        return level;
    }
}
```

---

# Interview Cheat Sheet

| Feature          | Description                                           |
| ---------------- | ----------------------------------------------------- |
| Enum             | A special class representing a fixed set of constants |
| Fields           | Store metadata for each constant                      |
| Methods          | Add behavior directly to enum constants               |
| Constructor      | Always private (explicitly or implicitly)             |
| Abstract methods | Allow constant-specific behavior                      |
| `EnumSet`        | High-performance `Set` optimized for enums            |
| `EnumMap`        | High-performance `Map` optimized for enum keys        |
| `values()`       | Returns all enum constants                            |
| `valueOf()`      | Converts a string to an enum constant                 |
| `ordinal()`      | Zero-based position (avoid for business logic)        |
| `name()`         | Returns the declared name of the constant             |

**Senior interview takeaway:** Treat enums as rich domain objects rather than simple constants. When you need collections keyed by or containing enums, prefer `EnumMap` and `EnumSet` over `HashMap` and `HashSet` for better performance and clearer intent.
