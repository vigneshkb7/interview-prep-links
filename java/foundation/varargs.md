These are common **Java interview topics** because they test your understanding of method signatures, object initialization, and the Java object lifecycle.

---

# Part 1: Varargs (`...`)

## What are Varargs?

Varargs (Variable Arguments) allow a method to accept **zero or more arguments** of the same type.

Instead of writing multiple overloaded methods:

```java
sum(int a)
sum(int a, int b)
sum(int a, int b, int c)
...
```

You write a single method:

```java
public static int sum(int... numbers) {

    int total = 0;

    for (int num : numbers) {
        total += num;
    }

    return total;
}
```

Usage:

```java
System.out.println(sum());
System.out.println(sum(10));
System.out.println(sum(10, 20));
System.out.println(sum(10, 20, 30));
```

Output:

```text
0
10
30
60
```

---

# How Varargs Work Internally

The compiler converts varargs into an array.

This method:

```java
public void print(String... names)
```

is compiled approximately as:

```java
public void print(String[] names)
```

Calling:

```java
print("A", "B", "C");
```

becomes:

```java
print(new String[]{"A", "B", "C"});
```

---

# Varargs with Other Parameters

The varargs parameter **must be the last parameter**.

Correct:

```java
void display(String title, int... numbers)
```

Usage:

```java
display("Scores", 10, 20, 30);
```

Incorrect:

```java
void display(int... numbers, String title)
```

Compile error.

---

# Rules of Varargs

Only **one** varargs parameter is allowed.

✔ Valid

```java
void test(int... numbers)
```

❌ Invalid

```java
void test(int... a, String... b)
```

---

Varargs must be the **last parameter**.

✔

```java
void test(String name, int... values)
```

❌

```java
void test(int... values, String name)
```

---

# Varargs and Method Overloading

Example:

```java
void print(int a) {
    System.out.println("One");
}

void print(int... a) {
    System.out.println("Varargs");
}
```

Calling:

```java
print(10);
```

Output:

```text
One
```

The compiler prefers the **most specific** method.

Calling:

```java
print(10, 20);
```

Output:

```text
Varargs
```

---

# Pitfall

```java
void test(int... x)
void test(int[] x)
```

This causes a compile error because they have the **same signature** after compilation.

---

# Real-world Uses

Common examples in the Java standard library:

```java
String.format("%s %d", "Age", 25);
```

```java
List.of(1, 2, 3, 4);
```

```java
printf("%d %s", 10, "Java");
```

---

# Part 2: Initializers

Java provides two initializer blocks:

* Static Initializer
* Instance Initializer

---

# Static Initializer Block

Runs **once** when the class is first loaded by the JVM.

Syntax:

```java
class Demo {

    static {

        System.out.println("Static Block");

    }

}
```

Output:

```text
Static Block
```

Executed before any object is created.

---

## When Does It Execute?

```java
class Demo {

    static {

        System.out.println("Loaded");

    }

    Demo() {

        System.out.println("Constructor");

    }
}

public class Main {

    public static void main(String[] args) {

        new Demo();
        new Demo();

    }

}
```

Output:

```text
Loaded
Constructor
Constructor
```

The static block executes only once.

---

# Multiple Static Blocks

```java
class Demo {

    static {

        System.out.println("First");

    }

    static {

        System.out.println("Second");

    }

}
```

Output:

```text
First
Second
```

Executed in the order they appear.

---

# Static Variables and Blocks

```java
class Demo {

    static int x;

    static {

        x = 100;

    }

}
```

Useful for complex static initialization.

---

# Instance Initializer Block

Runs **every time an object is created**, before the constructor.

```java
class Demo {

    {

        System.out.println("Instance Block");

    }

    Demo() {

        System.out.println("Constructor");

    }

}
```

Output:

```text
Instance Block
Constructor
```

---

# Instance Initializer Execution

```java
new Demo();
new Demo();
```

Output:

```text
Instance Block
Constructor

Instance Block
Constructor
```

Runs for every object.

---

# Execution Order

```java
class Demo {

    static {

        System.out.println("Static");

    }

    {

        System.out.println("Instance");

    }

    Demo() {

        System.out.println("Constructor");

    }

}
```

```java
new Demo();
```

Output:

```text
Static
Instance
Constructor
```

---

# Parent-Child Initialization Order

```java
class Parent {

    static {

        System.out.println("Parent Static");

    }

    {

        System.out.println("Parent Instance");

    }

    Parent() {

        System.out.println("Parent Constructor");

    }

}

class Child extends Parent {

    static {

        System.out.println("Child Static");

    }

    {

        System.out.println("Child Instance");

    }

    Child() {

        System.out.println("Child Constructor");

    }

}
```

```java
new Child();
```

Output:

```text
Parent Static
Child Static
Parent Instance
Parent Constructor
Child Instance
Child Constructor
```

---

# Initialization Flow

```text
Class Loading
      │
      ▼
Static Variables
      │
      ▼
Static Initializer Blocks
      │
      ▼
main()
      │
      ▼
Object Creation
      │
      ▼
Instance Variables
      │
      ▼
Instance Initializer Blocks
      │
      ▼
Constructor
```

---

# Why Use Static Initializers?

Typical use cases:

* Load configuration
* Initialize caches
* Register JDBC drivers (historically)
* Initialize expensive static objects

Example:

```java
class Config {

    static Map<String, String> cache = new HashMap<>();

    static {

        cache.put("host", "localhost");
        cache.put("port", "8080");

    }

}
```

---

# Why Use Instance Initializers?

Less common today, but useful when:

* Multiple constructors share initialization logic
* You want common setup before every constructor

Example:

```java
class Employee {

    private UUID id;

    {

        id = UUID.randomUUID();

    }

    Employee() {}

    Employee(String name) {}

}
```

Every constructor gets the same initialization without duplicating code.

---

# Interview Questions

### Why are varargs implemented as arrays?

To support a flexible number of arguments while keeping a simple runtime representation. The compiler packages the arguments into an array before invoking the method.

---

### Can a varargs parameter be `null`?

Yes.

```java
print((String[]) null);
```

passes a `null` array.

Whereas:

```java
print();
```

passes an empty array.

Your implementation should account for this if callers may explicitly pass `null`.

---

### Why must varargs be the last parameter?

Otherwise the compiler couldn't determine where the variable-length arguments end and subsequent parameters begin.

---

### Difference between a static block and a constructor?

| Static Block                  | Constructor                     |
| ----------------------------- | ------------------------------- |
| Runs once per class           | Runs for every object           |
| Executes during class loading | Executes during object creation |
| Initializes static state      | Initializes instance state      |

---

### Difference between an instance initializer and a constructor?

| Instance Initializer          | Constructor                         |
| ----------------------------- | ----------------------------------- |
| Runs before every constructor | Runs after the instance initializer |
| Shared by all constructors    | Can differ between constructors     |
| No parameters                 | Can accept parameters               |

---

### Are instance initializer blocks commonly used?

Not very often in modern Java. Most developers prefer:

* Constructor chaining using `this(...)`
* Private initialization methods
* Dependency injection frameworks

However, understanding them is important because they appear in legacy code and interviews.

---

# Interview Cheat Sheet

| Concept                 | Key Point                                                                                                |
| ----------------------- | -------------------------------------------------------------------------------------------------------- |
| Varargs                 | Accepts zero or more arguments                                                                           |
| Internal representation | Compiled to an array                                                                                     |
| Restrictions            | Only one varargs parameter, and it must be last                                                          |
| Static initializer      | Runs once when the class is loaded                                                                       |
| Instance initializer    | Runs before every constructor                                                                            |
| Execution order         | Static → Instance → Constructor                                                                          |
| Inheritance order       | Parent static → Child static → Parent instance → Parent constructor → Child instance → Child constructor |

### Senior Interview Tip

If asked **"What is the exact initialization order in Java?"**, the expected sequence is:

1. Parent static fields and static initializer blocks
2. Child static fields and static initializer blocks
3. Parent instance fields and instance initializer blocks
4. Parent constructor
5. Child instance fields and instance initializer blocks
6. Child constructor

Being able to explain this lifecycle clearly is a strong indicator of deep Java knowledge.
