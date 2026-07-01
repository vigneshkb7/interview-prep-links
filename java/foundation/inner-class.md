This is another frequently asked **Senior Java interview topic**. Java supports several types of nested classes, each serving different purposes.

---

# Types of Nested Classes

```text
Class
│
├── Static Nested Class
└── Inner Class
      ├── Member Inner Class
      ├── Local Inner Class
      └── Anonymous Inner Class
```

There are **4 commonly discussed types**:

1. Static Nested Class
2. Member Inner Class (often simply called "Inner Class")
3. Local Inner Class
4. Anonymous Inner Class

---

# 1. Static Nested Class

A static nested class is declared with the `static` keyword inside another class.

It behaves like a static member.

## Characteristics

* Cannot access non-static members of the outer class directly
* Can be instantiated without creating the outer class
* Uses less memory because it doesn't hold a reference to an outer object

### Example

```java
class Outer {

    static int x = 100;

    static class Nested {

        void display() {
            System.out.println(x);
        }
    }
}

public class Main {

    public static void main(String[] args) {

        Outer.Nested obj = new Outer.Nested();
        obj.display();

    }
}
```

Output

```
100
```

---

# 2. Member Inner Class

A member inner class is a non-static class declared inside another class.

Every inner class object belongs to an outer class object.

## Characteristics

* Can access all members of the outer class, including private members
* Requires an instance of the outer class to create it
* Implicitly holds a reference to the enclosing outer instance

### Example

```java
class Outer {

    private int x = 50;

    class Inner {

        void display() {
            System.out.println(x);
        }
    }
}

public class Main {

    public static void main(String[] args) {

        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();

        inner.display();

    }
}
```

Output

```
50
```

---

# 3. Local Inner Class

A local inner class is declared inside a method, constructor, or initialization block.

Its scope is limited to that block.

### Example

```java
class Demo {

    void show() {

        class Local {

            void print() {
                System.out.println("Hello");
            }
        }

        Local obj = new Local();
        obj.print();
    }
}
```

Output

```
Hello
```

## Characteristics

* Visible only within the method/block where it is declared
* Can access the enclosing class's members
* Can access local variables only if they are **final or effectively final**

Example:

```java
void display() {

    int number = 10;

    class Test {

        void print() {
            System.out.println(number);
        }
    }
}
```

This works because `number` is effectively final (its value isn't changed after initialization).

---

# 4. Anonymous Inner Class

An anonymous inner class is a class without a name, declared and instantiated in a single expression.

It's commonly used for one-time implementations of interfaces or subclasses.

### Example

```java
interface Greeting {

    void sayHello();
}

public class Main {

    public static void main(String[] args) {

        Greeting greeting = new Greeting() {

            @Override
            public void sayHello() {
                System.out.println("Hello");
            }
        };

        greeting.sayHello();
    }
}
```

Output

```
Hello
```

Another example extending a class:

```java
Thread t = new Thread() {

    @Override
    public void run() {
        System.out.println("Running...");
    }
};

t.start();
```

---

# Comparison Table

| Feature                                    | Static Nested                    | Member Inner                    | Local Inner           | Anonymous Inner                            |
| ------------------------------------------ | -------------------------------- | ------------------------------- | --------------------- | ------------------------------------------ |
| Has a class name                           | ✅                                | ✅                               | ✅                     | ❌                                          |
| Declared inside another class              | ✅                                | ✅                               | Inside a method/block | Inline expression                          |
| Needs outer object                         | ❌                                | ✅                               | ✅                     | Usually yes (if enclosing instance exists) |
| Can access outer instance members directly | ❌ (only static members directly) | ✅                               | ✅                     | ✅                                          |
| Reusable                                   | ✅                                | ✅                               | Limited to scope      | ❌                                          |
| Typical use                                | Group related helper classes     | Tight coupling with outer class | Method-specific logic | One-time implementation                    |

---

# Real-world Examples

### Static Nested Class

```java
public class Car {

    static class Engine {

        void start() {
            System.out.println("Engine Started");
        }
    }
}
```

The engine type is logically grouped with `Car`, but doesn't require a `Car` instance.

---

### Member Inner Class

```java
class BankAccount {

    private double balance = 1000;

    class Statement {

        void printBalance() {
            System.out.println(balance);
        }
    }
}
```

The statement is tightly associated with a specific bank account.

---

### Local Inner Class

Useful for helper classes needed only within a single method.

```java
void process() {

    class Validator {

        boolean isValid(String input) {
            return input != null;
        }
    }

    Validator validator = new Validator();
    System.out.println(validator.isValid("Java"));
}
```

---

### Anonymous Inner Class

Before Java 8, anonymous inner classes were commonly used for event listeners and callbacks.

```java
button.addActionListener(new ActionListener() {

    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println("Clicked");
    }
});
```

Since Java 8, lambda expressions are usually preferred for functional interfaces.

---

# Anonymous Class vs Lambda

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
};
```

Equivalent lambda:

```java
Runnable r = () -> System.out.println("Running");
```

Use lambdas only with **functional interfaces** (interfaces with a single abstract method).

---

# Common Interview Questions

### Why use a static nested class instead of an inner class?

A static nested class doesn't maintain a reference to an enclosing object, making it more memory-efficient when access to instance members isn't needed.

---

### Why can't a static nested class directly access instance variables?

Because there is no enclosing object associated with it. Instance members belong to an object, while static nested classes exist independently of outer class instances.

---

### Why must local variables accessed by local or anonymous classes be effectively final?

The local variable lives on the method's stack frame, which disappears when the method returns. The compiler captures its value, so allowing it to change later would lead to inconsistent behavior.

---

### When should you use an anonymous class instead of a lambda?

Use an anonymous class when you need:

* Multiple methods or additional state
* To extend a class rather than implement a functional interface
* Access to a distinct `this` reference (inside an anonymous class, `this` refers to the anonymous class instance; inside a lambda, `this` refers to the enclosing class)

---

## Interview Summary

* **Static Nested Class:** Independent helper class grouped with the outer class.
* **Member Inner Class:** Closely tied to an instance of the outer class.
* **Local Inner Class:** Temporary helper used within a single method or block.
* **Anonymous Inner Class:** One-time implementation or subclass, largely replaced by lambdas for functional interfaces.
