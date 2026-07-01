This is one of the **most frequently asked Java interview questions** for **Senior Software Engineer**, **Staff Engineer**, and companies like Walmart, Adobe, Oracle, Amazon, and Google.

Interviewers ask this because **collections like `HashMap` and `HashSet` depend entirely on the `equals()` and `hashCode()` contract**.

---

# Why do we need `equals()`?

By default, every Java object inherits `equals()` from `Object`.

Default implementation:

```java
public boolean equals(Object obj) {
    return this == obj;
}
```

It compares **object references**, not object contents.

Example:

```java
class Employee {

    int id;

    Employee(int id) {
        this.id = id;
    }
}

public class Main {

    public static void main(String[] args) {

        Employee e1 = new Employee(1);
        Employee e2 = new Employee(1);

        System.out.println(e1.equals(e2));

    }
}
```

Output:

```text
false
```

Although both employees have the same `id`, they are different objects.

---

# Overriding `equals()`

```java
class Employee {

    int id;

    Employee(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {

        if (this == obj)
            return true;

        if (obj == null || getClass() != obj.getClass())
            return false;

        Employee other = (Employee) obj;

        return id == other.id;
    }
}
```

Now:

```java
Employee e1 = new Employee(1);
Employee e2 = new Employee(1);

System.out.println(e1.equals(e2));
```

Output:

```text
true
```

---

# What is `hashCode()`?

`hashCode()` returns an integer hash value representing the object.

Default implementation is typically based on the object's identity (implementation-dependent).

Example:

```java
System.out.println(e1.hashCode());
```

Output:

```text
12345678
```

The exact value isn't important—only the contract is.

---

# Why is `hashCode()` Needed?

Collections like `HashMap` use it to find the correct bucket.

```text
Key
 │
 ▼
hashCode()
 │
 ▼
Bucket
 │
 ▼
equals()
```

Search process:

1. Compute `hashCode()`
2. Find the bucket
3. Use `equals()` to locate the exact key within that bucket

Without a correct `hashCode()`, lookups may fail even if `equals()` returns `true`.

---

# The `equals()` Contract

An implementation must satisfy:

### 1. Reflexive

```java
x.equals(x)
```

Always `true`.

---

### 2. Symmetric

```java
x.equals(y)
```

must equal

```java
y.equals(x)
```

---

### 3. Transitive

If

```java
x.equals(y)
```

and

```java
y.equals(z)
```

then

```java
x.equals(z)
```

must also be `true`.

---

### 4. Consistent

Repeated calls should produce the same result unless the data used for comparison changes.

---

### 5. Non-null

```java
x.equals(null)
```

must return `false`.

---

# The `hashCode()` Contract

### Rule 1

If two objects are equal according to `equals()`, they **must** have the same hash code.

```java
a.equals(b) == true
```

implies

```java
a.hashCode() == b.hashCode()
```

---

### Rule 2

The reverse is **not** required.

```text
Same hashCode
```

does **not** imply

```text
equals() == true
```

Multiple objects can legitimately share the same hash code (a **hash collision**).

---

### Rule 3

A hash code should remain consistent while the fields used to compute it remain unchanged.

---

# Incorrect Implementation

```java
class Employee {

    int id;

    @Override
    public boolean equals(Object obj) {

        return true;

    }
}
```

Forgot `hashCode()`.

Problem:

```java
HashSet<Employee> set = new HashSet<>();

set.add(new Employee(1));

System.out.println(set.contains(new Employee(1)));
```

May print:

```text
false
```

Why?

Different hash codes lead to different buckets, so `equals()` is never even called.

---

# Correct Implementation

```java
import java.util.Objects;

class Employee {

    private final int id;

    Employee(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {

        if (this == obj)
            return true;

        if (!(obj instanceof Employee))
            return false;

        Employee other = (Employee) obj;

        return id == other.id;
    }

    @Override
    public int hashCode() {

        return Objects.hash(id);

    }
}
```

Now:

```java
HashSet<Employee> set = new HashSet<>();

set.add(new Employee(1));

System.out.println(set.contains(new Employee(1)));
```

Output:

```text
true
```

---

# How `HashMap` Uses Them

```java
HashMap<Employee, String> map = new HashMap<>();

Employee e1 = new Employee(10);

map.put(e1, "Developer");
```

Later:

```java
Employee e2 = new Employee(10);

System.out.println(map.get(e2));
```

Execution:

```text
e2.hashCode()
      │
      ▼
Find bucket
      │
      ▼
Call equals()
      │
      ▼
Return "Developer"
```

---

# Hash Collision

Two different objects can have the same hash code.

```text
Employee(1) -> Bucket 5

Student(50) -> Bucket 5
```

Both keys occupy the same bucket.

`HashMap` then uses `equals()` to distinguish them.

---

# Performance

Ideal:

```text
hashCode()
       │
       ▼
Correct bucket
       │
       ▼
equals()
```

Time complexity:

```text
O(1)
```

Poor hash function:

```text
Everything → Same Bucket
```

Performance degrades toward:

```text
O(n)
```

A good `hashCode()` implementation distributes objects evenly across buckets.

---

# Mutable Keys

Avoid mutating fields used in `equals()` or `hashCode()` after using an object as a key.

Bad example:

```java
class Employee {

    int id;
}
```

```java
Employee e = new Employee();

e.id = 1;

map.put(e, "Developer");

e.id = 2;

map.get(e);
```

The lookup may fail because the hash code changed after insertion.

Prefer immutable keys.

---

# `Objects.equals()` and `Objects.hash()`

```java
import java.util.Objects;

@Override
public boolean equals(Object obj) {

    if (this == obj)
        return true;

    if (!(obj instanceof Person))
        return false;

    Person other = (Person) obj;

    return Objects.equals(name, other.name);

}
```

```java
@Override
public int hashCode() {

    return Objects.hash(name, age);

}
```

These utility methods reduce boilerplate and handle `null` values safely.

---

# Interview Questions

### Why must equal objects have the same hash code?

Because hash-based collections first locate the bucket using `hashCode()`. If equal objects produce different hash codes, they end up in different buckets and the collection cannot find the matching object.

---

### Can unequal objects have the same hash code?

Yes. This is called a **hash collision**. `HashMap` resolves collisions by comparing keys with `equals()`.

---

### What happens if you override `equals()` but not `hashCode()`?

You violate the contract. Collections like `HashMap` and `HashSet` can behave incorrectly, leading to failed lookups, duplicate logical keys, or inconsistent behavior.

---

### Can you override `hashCode()` without overriding `equals()`?

Yes, it's legal, but usually incorrect for domain objects. Unless the default identity-based equality is exactly what you want, both methods should be overridden together.

---

### Why are immutable objects recommended as keys in `HashMap`?

If fields involved in `equals()` and `hashCode()` change after insertion, the object's bucket location effectively changes, making lookups and removals unreliable.

---

# Best Practices

* Always override `equals()` and `hashCode()` together.
* Use the same fields in both methods.
* Prefer immutable fields for equality.
* Use `Objects.equals()` and `Objects.hash()` to simplify implementations.
* Make `equals()` reflexive, symmetric, transitive, consistent, and null-safe.
* Do not use mutable fields for hash-based keys.

---

# Interview Cheat Sheet

| Concept          | Key Point                                     |
| ---------------- | --------------------------------------------- |
| `equals()`       | Defines logical equality                      |
| `hashCode()`     | Determines the hash bucket                    |
| Equal objects    | **Must** have the same hash code              |
| Same hash code   | Does **not** mean objects are equal           |
| `HashMap` lookup | `hashCode()` → bucket → `equals()`            |
| Override rule    | Override `equals()` and `hashCode()` together |
| Mutable keys     | Avoid using them in hash-based collections    |
| Hash collisions  | Expected; resolved using `equals()`           |

## Senior Interview Tip

A classic interview question is:

> **"Why does `HashMap` use both `hashCode()` and `equals()`? Why not just `equals()`?"**

The answer is:

* Using only `equals()` would require comparing the new key against every key in the map, resulting in **O(n)** lookups.
* `hashCode()` narrows the search to a single bucket in **O(1)** average time.
* `equals()` is then used only to distinguish between keys within that bucket, including handling hash collisions.

This combination provides both **correctness** and **high performance**.
