These are very common **Senior Java interview questions**. Let's cover them with examples and interview points.

---

# 1. Checked Exceptions

A **checked exception** is an exception that the compiler forces you to handle.

If you don't handle it, the code **won't compile**.

Examples:

* IOException
* SQLException
* ClassNotFoundException

Example:

```java
import java.io.*;

public class Demo {

    public static void main(String[] args) throws IOException {

        FileReader reader = new FileReader("test.txt");

    }

}
```

Without

```java
throws IOException
```

or

```java
try {
   ...
} catch(IOException e) {
   ...
}
```

the compiler gives an error.

### Why?

Because these exceptions are usually recoverable.

Examples

* File may not exist
* Network unavailable
* Database connection failure

The programmer is expected to recover.

---

# 2. Unchecked Exceptions

Unchecked exceptions occur during runtime.

Compiler does **not** force you to handle them.

They extend

```
RuntimeException
```

Examples

* NullPointerException
* ArithmeticException
* ArrayIndexOutOfBoundsException
* IllegalArgumentException

Example

```java
public class Demo {

    public static void main(String[] args) {

        String s = null;

        System.out.println(s.length());

    }

}
```

Output

```
NullPointerException
```

Compiler allows this because it assumes it is a programming bug.

---

# Checked vs Unchecked

| Checked           | Unchecked                |
| ----------------- | ------------------------ |
| Compile time      | Runtime                  |
| Must handle       | Optional                 |
| Extends Exception | Extends RuntimeException |
| Recoverable       | Programming errors       |
| Compiler checks   | Compiler ignores         |

---

# Exception Hierarchy

```
Throwable
   |
   +-- Error
   |
   +-- Exception
          |
          +-- RuntimeException
          |
          +-- IOException
          +-- SQLException
```

Everything under RuntimeException is unchecked.

Everything else under Exception is checked.

---

# When to Use Which?

Use checked exceptions when

* File missing
* Invalid input file
* Database unavailable
* API temporarily unavailable

Use unchecked exceptions when

* Programmer mistake
* Invalid arguments
* Null values
* Bug in code

Example

```java
public void setAge(int age) {

    if(age < 0)
        throw new IllegalArgumentException("Age cannot be negative");

}
```

No need to force callers to catch it.

---

# 3. Exception Chaining

Exception chaining means **wrapping one exception inside another while preserving the original cause**.

Instead of losing the original exception, we keep it.

Example

Suppose

Database Layer

↓

Service Layer

↓

Controller

The database throws

```
SQLException
```

The service layer wraps it into

```
ApplicationException
```

---

Without chaining

```java
catch(SQLException e){
    throw new RuntimeException("Database Error");
}
```

Stack trace

```
RuntimeException
```

Original exception lost.

Hard to debug.

---

With chaining

```java
catch(SQLException e){

    throw new RuntimeException("Database Error", e);

}
```

Now the original exception is preserved.

---

Example

```java
try {

    FileReader reader = new FileReader("abc.txt");

}
catch(IOException e){

    throw new RuntimeException("Unable to process file", e);

}
```

Output

```
RuntimeException
    Caused by:
        FileNotFoundException
```

You can clearly see the root cause.

---

# Getting Original Exception

```java
try{

}
catch(Exception e){

    Throwable cause = e.getCause();

    System.out.println(cause);

}
```

Methods

```java
getCause()

initCause()

printStackTrace()
```

---

# Custom Exception Chaining

```java
class PaymentException extends Exception{

    public PaymentException(String message, Throwable cause){

        super(message, cause);

    }

}
```

Usage

```java
try{

    processPayment();

}
catch(IOException e){

    throw new PaymentException("Payment Failed", e);

}
```

---

# Real-world Layered Architecture

```
Controller
     |
Service
     |
Repository
     |
Database
```

Repository

```java
catch(SQLException e){

    throw new RepositoryException("DB Error", e);

}
```

Service

```java
catch(RepositoryException e){

    throw new ServiceException("Unable to fetch user", e);

}
```

Controller

```java
catch(ServiceException e){

    log.error(e.getMessage(), e);

}
```

Stack trace

```
ServiceException

Caused by

RepositoryException

Caused by

SQLException
```

This makes debugging much easier.

---

# Interview Questions

### Q1. Why are checked exceptions considered controversial?

Many developers find them verbose because they force callers to catch or declare exceptions even when they cannot meaningfully recover. This can lead to excessive `try-catch` blocks or `throws` declarations. Others argue they improve API design by making recoverable failure modes explicit.

---

### Q2. Why does Java have unchecked exceptions?

Unchecked exceptions represent programming errors (such as null dereferences or invalid arguments) that should generally be fixed in the code rather than recovered from at runtime.

---

### Q3. Why is exception chaining important?

It preserves the original failure while adding higher-level context. This makes debugging easier because you can trace the complete chain of failures across application layers.

---

### Q4. How do you create exception chaining?

Use a constructor that accepts both a message and a cause:

```java
throw new RuntimeException("Service failed", e);
```

or in a custom exception:

```java
public MyException(String message, Throwable cause) {
    super(message, cause);
}
```

---

### Q5. When should you wrap exceptions?

Wrap exceptions when crossing architectural boundaries (for example, Repository → Service → Controller) to provide business-specific context while preserving the original cause with exception chaining.

---

## Best Practices

* Catch the most specific exception possible.
* Do not catch `Exception` unless you have a good reason.
* Preserve the original cause by using exception chaining.
* Use checked exceptions for recoverable conditions and unchecked exceptions for programming errors.
* Log exceptions at the appropriate boundary (often the application entry point), avoiding duplicate logging at every layer unless additional context is needed.

These concepts are frequently discussed in interviews for senior Java roles because they demonstrate good API design, error handling strategy, and debugging practices.
