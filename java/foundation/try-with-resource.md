This is one of the most frequently asked **Java 8+ interview topics**, especially for **Senior/Staff Engineer** roles. Interviewers want to know whether you understand **resource management**, not just the syntax.

---

# What is try-with-resources?

**try-with-resources** is a feature introduced in **Java 7** that automatically closes resources when execution leaves the `try` block.

Instead of manually calling `close()`, Java does it for you.

Before Java 7:

```java
BufferedReader reader = null;

try {
    reader = new BufferedReader(new FileReader("test.txt"));
    System.out.println(reader.readLine());
} finally {
    if (reader != null) {
        reader.close();
    }
}
```

Problems:

* Verbose
* Easy to forget `close()`
* Prone to resource leaks
* Nested `try-finally` blocks

---

# Using try-with-resources

```java
try (BufferedReader reader =
         new BufferedReader(new FileReader("test.txt"))) {

    System.out.println(reader.readLine());

}
```

No `finally` block is needed.

Java automatically executes

```java
reader.close();
```

---

# What Resources Can Be Used?

Only objects implementing the `AutoCloseable` interface.

Examples include:

* `BufferedReader`
* `FileReader`
* `InputStream`
* `OutputStream`
* `Scanner`
* `Connection`
* `Statement`
* `ResultSet`

---

# How Does It Work?

```java
try (Scanner scanner = new Scanner(System.in)) {

    System.out.println(scanner.nextLine());

}
```

Compiler roughly transforms it into:

```java
Scanner scanner = new Scanner(System.in);

try {

    System.out.println(scanner.nextLine());

}
finally {

    if (scanner != null)
        scanner.close();

}
```

The compiler generates the cleanup code automatically.

---

# Multiple Resources

Multiple resources can be declared, separated by semicolons.

```java
try (
    FileInputStream input =
        new FileInputStream("input.txt");

    FileOutputStream output =
        new FileOutputStream("output.txt")
) {

    // copy data

}
```

Resources are closed automatically.

---

# Closing Order

Resources are closed in **reverse order** (Last In, First Out).

```java
try (

    A a = new A();
    B b = new B();
    C c = new C()

) {

}
```

Closing sequence:

```text
C.close()

B.close()

A.close()
```

This is important when one resource depends on another.

---

# What is `AutoCloseable`?

`AutoCloseable` is the interface that enables try-with-resources.

Definition:

```java
public interface AutoCloseable {

    void close() throws Exception;

}
```

If a class implements this interface, it can be used in a try-with-resources statement.

---

# Creating a Custom AutoCloseable

```java
class DatabaseConnection implements AutoCloseable {

    public DatabaseConnection() {
        System.out.println("Connected");
    }

    @Override
    public void close() {
        System.out.println("Disconnected");
    }
}
```

Usage:

```java
public class Main {

    public static void main(String[] args) {

        try (DatabaseConnection db =
                 new DatabaseConnection()) {

            System.out.println("Working");

        }

    }

}
```

Output:

```text
Connected
Working
Disconnected
```

---

# Exception Handling

```java
try (
    BufferedReader reader =
        new BufferedReader(new FileReader("abc.txt"))
) {

    throw new RuntimeException("Main Exception");

}
```

During cleanup,

```java
reader.close()
```

may also throw an exception.

Which exception is thrown?

The **exception from the `try` block** is the primary exception.

The exception thrown while closing is attached as a **suppressed exception**.

---

# Suppressed Exceptions

Example:

```java
class Test implements AutoCloseable {

    @Override
    public void close() {

        throw new RuntimeException("Close Exception");

    }
}
```

```java
try (Test t = new Test()) {

    throw new RuntimeException("Main Exception");

}
```

Output:

```text
Main Exception

Suppressed:
Close Exception
```

Retrieve suppressed exceptions:

```java
catch (Exception e) {

    for (Throwable t : e.getSuppressed()) {

        System.out.println(t);

    }
}
```

---

# `AutoCloseable` vs `Closeable`

| `AutoCloseable`                     | `Closeable`                    |
| ----------------------------------- | ------------------------------ |
| Introduced in Java 7                | Older interface                |
| `close()` can throw any `Exception` | `close()` throws `IOException` |
| General-purpose                     | Primarily for I/O resources    |
| Parent interface                    | Extends `AutoCloseable`        |

Relationship:

```text
AutoCloseable
      ▲
      │
 Closeable
```

---

# Java 9 Enhancement

Before Java 9:

```java
try (BufferedReader reader =
         new BufferedReader(new FileReader("test.txt"))) {

}
```

Java 9 allows an effectively final resource declared earlier:

```java
BufferedReader reader =
    new BufferedReader(new FileReader("test.txt"));

try (reader) {

    System.out.println(reader.readLine());

}
```

This reduces duplication.

---

# Real-world JDBC Example

Without try-with-resources:

```java
Connection connection = null;
PreparedStatement statement = null;

try {

    connection = dataSource.getConnection();
    statement = connection.prepareStatement(sql);

}
finally {

    if (statement != null)
        statement.close();

    if (connection != null)
        connection.close();
}
```

With try-with-resources:

```java
try (
    Connection connection = dataSource.getConnection();
    PreparedStatement statement =
        connection.prepareStatement(sql)
) {

    // Execute query

}
```

This is the preferred approach in production code.

---

# Interview Questions

### Why is try-with-resources better than `finally`?

It automatically closes resources, reduces boilerplate code, prevents resource leaks, handles exceptions correctly, and supports multiple resources with deterministic cleanup.

---

### What happens if both the `try` block and `close()` throw exceptions?

The exception from the `try` block is propagated, while exceptions thrown during `close()` are stored as **suppressed exceptions** and can be retrieved with `getSuppressed()`.

---

### Can any object be used in try-with-resources?

No. Only objects implementing `AutoCloseable` (or its subinterface `Closeable`) can be used.

---

### In what order are resources closed?

Resources are closed in **reverse order of declaration (LIFO)**.

Example:

```java
try (
    A a = new A();
    B b = new B();
    C c = new C()
) {
}
```

Closing order:

```text
C → B → A
```

---

### When should you implement `AutoCloseable`?

Implement it when your class manages resources that require deterministic cleanup, such as:

* Database connections
* File handles
* Network sockets
* Locks
* Native resources
* Thread pools or other components that need explicit shutdown

---

# Best Practices

* Prefer try-with-resources over manual `try-finally`.
* Keep the scope of resources as small as possible.
* Implement `AutoCloseable` for custom resource-managing classes.
* Make `close()` idempotent (safe to call more than once) when practical.
* Avoid throwing unnecessary exceptions from `close()`, as they can complicate error handling.
* Inspect suppressed exceptions when diagnosing failures involving resource cleanup.

---

# Interview Cheat Sheet

| Concept            | Key Point                                            |
| ------------------ | ---------------------------------------------------- |
| try-with-resources | Automatically closes resources                       |
| Requirement        | Resource must implement `AutoCloseable`              |
| Multiple resources | Declare with semicolons                              |
| Close order        | Reverse order (LIFO)                                 |
| Primary exception  | Exception from the `try` block                       |
| Cleanup exception  | Stored as a suppressed exception                     |
| `AutoCloseable`    | General cleanup interface                            |
| `Closeable`        | I/O-specific interface extending `AutoCloseable`     |
| Java 9 feature     | Reuse effectively final resources in `try(resource)` |

**Senior interview takeaway:** try-with-resources is about **deterministic resource management**. Understanding how Java generates cleanup code, how suppressed exceptions work, and when to implement `AutoCloseable` demonstrates a solid grasp of production-quality Java.
