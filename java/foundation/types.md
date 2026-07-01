This is a **very common Java interview topic**, especially for **Senior Software Engineer** roles. Interviewers often combine questions about **primitive types**, **wrapper classes**, **autoboxing**, caching, equality, performance, and `NullPointerException` pitfalls.

---

# 1. Primitive Types

Java has **8 primitive data types**.

| Primitive | Size                         | Wrapper Class | Default Value (field) |
| --------- | ---------------------------- | ------------- | --------------------- |
| `byte`    | 8 bits                       | `Byte`        | `0`                   |
| `short`   | 16 bits                      | `Short`       | `0`                   |
| `int`     | 32 bits                      | `Integer`     | `0`                   |
| `long`    | 64 bits                      | `Long`        | `0L`                  |
| `float`   | 32 bits                      | `Float`       | `0.0f`                |
| `double`  | 64 bits                      | `Double`      | `0.0d`                |
| `char`    | 16 bits                      | `Character`   | `'\u0000'`            |
| `boolean` | JVM-dependent representation | `Boolean`     | `false`               |

Example:

```java
int age = 25;
double salary = 55000.75;
char grade = 'A';
boolean active = true;
```

---

# Primitive vs Object

Primitive:

```java
int x = 10;
```

Wrapper:

```java
Integer x = 10;
```

Memory (conceptually):

```text
Primitive

Stack
+------+
|  10  |
+------+

--------------------

Wrapper

Stack
+---------+
| ref ----|----+
+---------+    |
               ▼
Heap
+-------------+
| Integer(10) |
+-------------+
```

---

# Why Do Wrapper Classes Exist?

Collections can store **objects**, not primitives.

❌ Invalid

```java
List<int> numbers;
```

✔ Correct

```java
List<Integer> numbers = new ArrayList<>();
```

Other uses:

* Generic types
* `null` values
* Utility methods (`Integer.parseInt()`, etc.)

---

# Wrapper Classes

Each primitive has a corresponding wrapper.

```java
Integer
Long
Double
Boolean
Character
Byte
Short
Float
```

Example:

```java
Integer value = Integer.valueOf(100);

System.out.println(value.intValue());
```

---

# Autoboxing

Autoboxing is the automatic conversion from a primitive to its wrapper object.

```java
Integer number = 10;
```

Compiler converts it to approximately:

```java
Integer number = Integer.valueOf(10);
```

---

# Unboxing

Automatic conversion from a wrapper to a primitive.

```java
Integer number = 20;

int x = number;
```

Compiler converts it to approximately:

```java
int x = number.intValue();
```

---

# Example

```java
Integer a = 10;

Integer b = 20;

int sum = a + b;
```

Compiler roughly generates:

```java
int sum = a.intValue() + b.intValue();
```

---

# Integer Caching

One of the most common interview questions.

```java
Integer a = 100;
Integer b = 100;

System.out.println(a == b);
```

Output:

```text
true
```

Why?

`Integer.valueOf()` caches values in the range **-128 to 127**.

Memory:

```text
Integer Cache

-128 ... 127

      100
     /   \
    a     b
```

---

Now:

```java
Integer a = 200;
Integer b = 200;

System.out.println(a == b);
```

Output:

```text
false
```

Because 200 is **not** in the default cache range.

Two separate objects are created.

---

# Always Use `equals()`

```java
Integer a = 200;
Integer b = 200;

System.out.println(a.equals(b));
```

Output:

```text
true
```

Use `equals()` for value comparison.

---

# Autoboxing Pitfall #1: `NullPointerException`

```java
Integer x = null;

int y = x;
```

Compiler performs:

```java
int y = x.intValue();
```

Output:

```text
NullPointerException
```

This surprises many developers.

---

# Autoboxing Pitfall #2: Performance

```java
Long sum = 0L;

for (long i = 0; i < 1_000_000; i++) {

    sum += i;

}
```

Every iteration performs:

* Unboxing
* Addition
* Boxing

Equivalent idea:

```java
sum = Long.valueOf(sum.longValue() + i);
```

This creates unnecessary objects.

Better:

```java
long sum = 0;

for (long i = 0; i < 1_000_000; i++) {

    sum += i;

}
```

---

# Wrapper Immutability

```java
Integer x = 10;

x++;
```

This does **not** modify the existing object.

It effectively performs:

```java
x = Integer.valueOf(x.intValue() + 1);
```

A new `Integer` object is produced (or retrieved from the cache).

---

# Wrapper Classes are Immutable

```java
Integer x = 100;
```

Cannot modify:

```java
x.setValue(200);
```

No such method exists.

Instead:

```java
x = 200;
```

creates (or reuses) another wrapper object.

---

# Comparing Wrappers

```java
Integer a = 100;
Integer b = 100;

System.out.println(a == b);
```

Output:

```text
true
```

But:

```java
Integer a = 300;
Integer b = 300;

System.out.println(a == b);
```

Output:

```text
false
```

Reason: cache boundary.

---

# Mixing Primitive and Wrapper

```java
Integer a = 100;

int b = 100;

System.out.println(a == b);
```

Output:

```text
true
```

Why?

`a` is automatically unboxed to an `int`.

Comparison becomes:

```java
100 == 100
```

---

# Collections Example

```java
List<Integer> list = new ArrayList<>();

list.add(10);
```

Compiler performs autoboxing:

```java
list.add(Integer.valueOf(10));
```

Retrieving:

```java
int value = list.get(0);
```

Compiler performs unboxing:

```java
int value = list.get(0).intValue();
```

---

# Parsing Numbers

```java
int value = Integer.parseInt("100");
```

Returns a primitive `int`.

```java
Integer value = Integer.valueOf("100");
```

Returns an `Integer` object.

---

# Primitive vs Wrapper

| Primitive             | Wrapper                                     |
| --------------------- | ------------------------------------------- |
| Faster                | Slightly slower                             |
| No object allocation  | Object allocation (except cached instances) |
| Cannot be `null`      | Can be `null`                               |
| Used for calculations | Required for collections and generics       |
| Lower memory usage    | Higher memory usage                         |

---

# Real-world Example

Database entity:

```java
class Employee {

    private Integer id;
}
```

Why `Integer` instead of `int`?

Because before the entity is persisted, the ID may be unknown (`null`).

---

# Interview Questions

### Why are wrapper classes immutable?

Immutability makes them thread-safe, allows safe caching (such as the `Integer` cache), and simplifies reasoning about shared objects.

---

### Why does `Integer.valueOf()` cache values?

Small integers are used frequently. Reusing cached instances reduces object creation and memory usage.

Default cache range:

```text
-128 to 127
```

---

### Why should you avoid wrappers in performance-critical code?

Repeated boxing and unboxing create additional work and may allocate unnecessary objects, increasing CPU time and garbage collection pressure.

---

### Why does this throw an exception?

```java
Integer x = null;

int y = x;
```

Because Java performs unboxing:

```java
x.intValue();
```

Calling a method on `null` causes a `NullPointerException`.

---

### Why can't generics use primitives?

Java generics work only with reference types. Type parameters must be objects, so wrapper classes are required.

---

# Best Practices

* Prefer primitives for numeric computations and performance-sensitive code.
* Use wrappers when working with collections, generics, or nullable values.
* Compare wrapper values using `equals()`, not `==`.
* Be careful of automatic unboxing when a wrapper might be `null`.
* Avoid unnecessary boxing and unboxing in loops and hot code paths.

---

# Interview Cheat Sheet

| Concept          | Key Point                                        |
| ---------------- | ------------------------------------------------ |
| Primitive        | Stores the value directly                        |
| Wrapper          | Object representation of a primitive             |
| Autoboxing       | Primitive → Wrapper                              |
| Unboxing         | Wrapper → Primitive                              |
| Integer cache    | Default range `-128` to `127`                    |
| `==` on wrappers | Compares references (unless unboxed)             |
| `equals()`       | Compares values                                  |
| Wrappers         | Immutable                                        |
| Wrapper `null`   | Can cause `NullPointerException` during unboxing |
| Performance      | Prefer primitives in computation-heavy code      |

## Senior Interview Tip

A classic question is:

> **"What is the output?"**

```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);

Integer c = 128;
Integer d = 128;
System.out.println(c == d);
```

**Answer:**

```text
true
false
```

**Explanation:**

* `127` is within the default `Integer` cache (`-128` to `127`), so both variables reference the same cached object.
* `128` is outside the cache, so two distinct `Integer` objects are created.
* `==` compares object references, not numeric values. For value comparison, use `equals()`.
