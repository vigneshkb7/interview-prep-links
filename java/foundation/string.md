This is one of the **top 10 Java interview topics**. Interviewers often ask **multiple questions around Strings**, including how they're stored, why they're immutable, and when to use `StringBuilder` vs `StringBuffer`.

---

# 1. What is a String?

A `String` is an object representing a sequence of characters.

```java
String s = "Hello";
```

Unlike primitive types, a `String` is an object.

Internally (modern JDKs), strings store their character data efficiently while exposing an immutable API.

---

# 2. What is the String Pool?

The **String Pool** (also called the **String Constant Pool**) is a special memory area where Java stores **string literals**.

Instead of creating duplicate string objects, Java reuses existing ones.

Example:

```java
String s1 = "Java";
String s2 = "Java";
```

Memory:

```text
        String Pool

      +-----------+
      |  "Java"   |
      +-----------+
         ↑     ↑
        s1     s2
```

Both variables reference the **same object**.

---

# Using `new String()`

```java
String s1 = "Java";
String s2 = new String("Java");
```

Memory:

```text
String Pool

+-----------+
| "Java"    |
+-----------+
      ↑
      s1

Heap

+-----------+
| "Java"    |
+-----------+
      ↑
      s2
```

`new String()` always creates a **new object** on the heap, even if an identical literal already exists in the pool.

---

# Comparing Strings

```java
String s1 = "Java";
String s2 = "Java";

System.out.println(s1 == s2);
```

Output:

```text
true
```

Because both references point to the same pooled object.

Now:

```java
String s3 = new String("Java");

System.out.println(s1 == s3);
```

Output:

```text
false
```

Different objects.

Use `equals()` for content comparison:

```java
System.out.println(s1.equals(s3));
```

Output:

```text
true
```

---

# `intern()`

The `intern()` method returns the pooled instance of a string.

```java
String s1 = new String("Java");

String s2 = s1.intern();

String s3 = "Java";

System.out.println(s2 == s3);
```

Output:

```text
true
```

If the string already exists in the pool, `intern()` returns that instance; otherwise, it adds it to the pool.

---

# Why Does the String Pool Exist?

Benefits:

* Reduces memory usage
* Avoids duplicate string objects
* Improves performance
* Makes string literals efficient

Without pooling:

```java
String a = "Java";
String b = "Java";
String c = "Java";
```

Three identical objects would be created.

With pooling:

```text
        "Java"
       /   |   \
      a    b    c
```

Only one object is shared.

---

# 3. Why is `String` Immutable?

Once created, a string's value **cannot be changed**.

Example:

```java
String s = "Java";

s.concat(" Programming");

System.out.println(s);
```

Output:

```text
Java
```

`concat()` returns a **new** string.

Correct usage:

```java
s = s.concat(" Programming");
```

Output:

```text
Java Programming
```

---

# Memory Illustration

```java
String s = "Java";

s = s + " 17";
```

Memory:

```text
Before

s ---> "Java"

After

"Java"

s ---> "Java 17"
```

The original `"Java"` remains unchanged.

---

# Why is `String` Immutable?

## 1. Security

Strings are widely used for:

* File paths
* Database URLs
* Network addresses
* Class names

If strings were mutable:

```java
login("admin");
```

could become:

```text
"hacker"
```

after validation, creating serious security risks.

---

## 2. Thread Safety

Multiple threads can safely share the same string.

```java
String name = "Java";
```

All threads can read it without synchronization because it never changes.

---

## 3. String Pool

Pooling is safe only because strings cannot change.

Imagine:

```java
String s1 = "Java";
String s2 = "Java";
```

If:

```java
s1.replace(...)
```

modified the object in place, `s2` would unexpectedly change too.

Immutability prevents this.

---

## 4. HashMap Performance

Strings are commonly used as keys.

```java
HashMap<String, String> map = new HashMap<>();
```

If a string key could change after insertion, its hash code could change, making lookups unreliable.

Because strings are immutable, their hash code remains stable.

---

# How is `hashCode()` Optimized?

The hash code is computed once and cached.

Simplified idea:

```java
private int hash;
```

First call:

```java
hashCode();
```

computes and stores the value.

Subsequent calls:

```java
hashCode();
```

return the cached value.

This is safe because the string never changes.

---

# 4. `StringBuilder`

`StringBuilder` is a **mutable** sequence of characters.

```java
StringBuilder sb = new StringBuilder();

sb.append("Java");
sb.append(" Programming");

System.out.println(sb);
```

Output:

```text
Java Programming
```

No new object is created for each append.

---

# Memory

```text
StringBuilder

+----------------------+
| Java Programming     |
+----------------------+
```

The same object is modified.

---

# 5. `StringBuffer`

`StringBuffer` is also mutable.

```java
StringBuffer sb = new StringBuffer();

sb.append("Java");
```

Difference:

`StringBuffer` is **thread-safe** because its methods are synchronized.

---

# `StringBuilder` vs `StringBuffer`

| `StringBuilder`                   | `StringBuffer`                                   |
| --------------------------------- | ------------------------------------------------ |
| Not synchronized                  | Synchronized                                     |
| Faster                            | Slightly slower                                  |
| Not thread-safe                   | Thread-safe                                      |
| Introduced in Java 5              | Introduced in Java 1.0                           |
| Preferred in single-threaded code | Use when multiple threads modify the same buffer |

---

# Performance Example

```java
String s = "";

for (int i = 0; i < 10000; i++) {

    s += i;

}
```

This creates thousands of temporary string objects.

Better:

```java
StringBuilder sb = new StringBuilder();

for (int i = 0; i < 10000; i++) {

    sb.append(i);

}
```

This is much more efficient.

---

# String vs `StringBuilder`

| `String`                              | `StringBuilder`                 |
| ------------------------------------- | ------------------------------- |
| Immutable                             | Mutable                         |
| Creates new objects on modification   | Modifies existing object        |
| Thread-safe due to immutability       | Not thread-safe                 |
| Good for constants and read-only text | Good for repeated modifications |

---

# Real-world Example

Bad:

```java
String result = "";

for (String word : words) {

    result += word;

}
```

Each iteration creates a new string.

Good:

```java
StringBuilder result = new StringBuilder();

for (String word : words) {

    result.append(word);

}
```

---

# Interview Questions

### Why is `String` immutable?

Main reasons:

* Security
* Thread safety
* Safe string pooling
* Stable hash codes for hash-based collections
* Simpler design

---

### Why is `StringBuilder` faster than `String` concatenation?

`StringBuilder` modifies a single object, while repeated `String` concatenation creates many intermediate immutable string objects.

> **Note:** The Java compiler already optimizes simple concatenations like `"Hello " + name` into `StringBuilder` (or equivalent mechanisms in newer JDKs). The performance concern mainly applies to concatenation inside loops or repeated dynamic operations.

---

### When should you use `StringBuffer`?

Use it only when **multiple threads** need to modify the **same** mutable character sequence and synchronization is required.

In most modern applications, `StringBuilder` is preferred because synchronization is often handled at a higher level.

---

### Why can we compare string literals using `==`?

Because string literals are interned in the String Pool.

```java
String a = "Java";
String b = "Java";
```

Both reference the same pooled object.

However:

```java
String a = new String("Java");
String b = new String("Java");
```

`a == b` is `false`.

Always use `equals()` for content comparison.

---

### Where is the String Pool stored?

In modern JVMs (Java 7 and later), the String Pool resides on the **heap**. Prior to Java 7, it was stored in the **PermGen** space.

---

# Best Practices

* Use string literals whenever possible to benefit from the String Pool.
* Use `equals()` for comparing string contents.
* Use `StringBuilder` for repeated string modifications.
* Use `StringBuffer` only when shared mutable buffers require thread safety.
* Avoid repeated `+` concatenation inside loops.
* Don't call `intern()` unless you have a measured need for canonicalizing many duplicate strings.

---

# Interview Cheat Sheet

| Concept                  | Key Point                                           |
| ------------------------ | --------------------------------------------------- |
| String Pool              | Reuses identical string literals                    |
| `new String()`           | Always creates a new heap object                    |
| `intern()`               | Returns the pooled instance                         |
| `String`                 | Immutable                                           |
| Benefits of immutability | Security, thread safety, pooling, stable hash codes |
| `StringBuilder`          | Mutable and fast, not thread-safe                   |
| `StringBuffer`           | Mutable and synchronized                            |
| `==`                     | Compares object references                          |
| `equals()`               | Compares string contents                            |

## Senior Interview Tip

A classic question is:

> **"Why does `StringBuilder` outperform `String` in loops?"**

Because each `String` concatenation creates a **new immutable object**, resulting in many allocations and copies. `StringBuilder` maintains a **resizable internal buffer**, appending characters in place and only expanding its capacity when necessary. This dramatically reduces object creation and copying, making it the preferred choice for repeated string construction.
