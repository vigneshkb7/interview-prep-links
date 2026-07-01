This is one of the **most important Java interview topics**. Nearly every **Senior Java** interview includes JVM architecture questions because they test your understanding of **memory management**, **performance**, and **GC**.

---

# JVM Architecture Overview

```text
                    Java Source (.java)
                           │
                           ▼
                      Java Compiler
                         (javac)
                           │
                           ▼
                     Bytecode (.class)
                           │
                           ▼
                    +----------------+
                    |      JVM       |
                    +----------------+
                           │
      ┌────────────────────┼────────────────────┐
      │                    │                    │
      ▼                    ▼                    ▼
 ClassLoader          Runtime Data Areas   Execution Engine
                           │                    │
      ┌────────────────────┼────────────────────┘
      │
      ▼
+---------------------------------------------+
|              Runtime Memory                  |
|---------------------------------------------|
| Heap                                        |
| Java Stack                                  |
| Metaspace                                   |
| PC Register                                 |
| Native Method Stack                         |
+---------------------------------------------+
```

---

# JVM Components

The JVM mainly consists of:

1. ClassLoader
2. Runtime Data Areas
3. Execution Engine
4. Garbage Collector
5. JNI (Java Native Interface)

---

# 1. ClassLoader

The ClassLoader loads `.class` files into memory.

Flow:

```text
MyClass.class
      │
      ▼
ClassLoader
      │
      ▼
Method Area / Metaspace
```

A class is loaded only when needed.

---

# Class Loading Phases

```text
Loading
     │
     ▼
Linking
     │
     ├── Verification
     ├── Preparation
     └── Resolution
     │
     ▼
Initialization
```

---

## Loading

Reads the `.class` file.

```text
Employee.class

↓

Employee Class Object
```

---

## Verification

Checks:

* Valid bytecode
* JVM format
* Security
* Illegal instructions

Example:

```text
Invalid Bytecode

↓

ClassFormatError
```

---

## Preparation

Allocates memory for static variables.

Example:

```java
class Test {

    static int x = 100;

}
```

During preparation:

```text
x = 0
```

(default value)

---

## Resolution

Resolves symbolic references.

Example:

```java
Employee e = new Employee();
```

The JVM resolves:

```text
Employee

↓

Memory Address
```

---

## Initialization

Runs:

* Static variable assignments
* Static initializer blocks

```java
class Demo {

    static {

        System.out.println("Loaded");

    }

}
```

Executed once.

---

# Built-in ClassLoaders

```text
Bootstrap ClassLoader
        │
        ▼
Platform ClassLoader
        │
        ▼
Application ClassLoader
```

---

## Bootstrap ClassLoader

Loads core Java classes.

Examples:

* `java.lang.String`
* `java.lang.Object`
* `java.util.List`

---

## Platform ClassLoader

Loads platform libraries.

Examples:

```text
java.sql.*

java.xml.*
```

---

## Application ClassLoader

Loads your application's classes.

```text
com.company.Employee
```

---

# Parent Delegation Model

Suppose:

```java
new Employee();
```

Flow:

```text
Application

↓

Platform

↓

Bootstrap
```

If Bootstrap can't find the class:

```text
↓

Platform

↓

Application
```

This prevents malicious code from replacing core Java classes.

---

# Runtime Data Areas

The JVM has several memory regions.

```text
          JVM Memory

     +------------------+
     |     Heap         |
     +------------------+

     +------------------+
     |     Stack        |
     +------------------+

     +------------------+
     |   Metaspace      |
     +------------------+

     +------------------+
     |  PC Register     |
     +------------------+

     +------------------+
     | Native Stack     |
     +------------------+
```

---

# 2. Heap Memory

The Heap stores:

* Objects
* Arrays
* Instance variables

Example:

```java
Employee e = new Employee();
```

Memory:

```text
Stack

e ----+

      |

      ▼

Heap

Employee Object
```

The Heap is shared by all threads.

---

## Modern Heap Layout

```text
Heap

+----------------------+
| Young Generation     |
|----------------------|
| Eden                 |
| Survivor 0           |
| Survivor 1           |
+----------------------+

+----------------------+
| Old Generation       |
+----------------------+
```

---

## Eden Space

New objects are created here.

```java
new Employee();
```

↓

```text
Eden
```

---

## Survivor Spaces

Objects that survive a Minor GC move between Survivor spaces.

```text
Eden

↓

S0

↓

S1
```

---

## Old Generation

Long-lived objects eventually move here.

```text
Employee Cache

Session Objects

Singletons
```

---

# 3. Stack Memory

Every thread has its own stack.

Contains:

* Local variables
* Method parameters
* Return addresses

Example:

```java
void test() {

    int x = 10;

}
```

Memory:

```text
Stack Frame

x = 10
```

Method call:

```text
main()

↓

test()

↓

calculate()
```

Each method creates a new stack frame.

---

## Stack Frame

Contains:

```text
Local Variables

Operand Stack

Return Address
```

When the method finishes:

```text
Frame Removed
```

---

# Heap vs Stack

| Heap              | Stack                                   |
| ----------------- | --------------------------------------- |
| Stores objects    | Stores local variables                  |
| Shared by threads | One per thread                          |
| Managed by GC     | Automatically freed when methods return |
| Larger            | Smaller                                 |

---

# 4. Metaspace

Before Java 8:

```text
PermGen
```

Java 8+:

```text
Metaspace
```

Stores:

* Class metadata
* Method metadata
* Runtime constant pool
* Bytecode-related information

Does **not** store object instances.

---

Example:

```java
class Employee
```

Stored in:

```text
Metaspace
```

Object:

```java
new Employee()
```

Stored in:

```text
Heap
```

---

# 5. PC Register

Each thread has its own Program Counter.

It stores the address of the current bytecode instruction.

```text
Instruction 10

↓

Instruction 11

↓

Instruction 12
```

Used for thread scheduling and resuming execution.

---

# 6. Native Method Stack

Used for methods written in native languages like C/C++.

Example:

```java
System.loadLibrary(...)
```

Native execution uses this stack.

---

# Execution Engine

Responsible for executing bytecode.

Components:

```text
Execution Engine

      │

      ├── Interpreter

      ├── JIT Compiler

      └── Garbage Collector
```

---

# Interpreter

Executes bytecode line by line.

```text
Bytecode

↓

Instruction

↓

Instruction
```

Simple, but slower.

---

# JIT Compiler

Frequently executed ("hot") methods are compiled into native machine code.

```text
Bytecode

↓

Native Code

↓

CPU
```

This greatly improves performance.

---

# Garbage Collection

The GC automatically frees memory occupied by unreachable objects.

Example:

```java
Employee e = new Employee();

e = null;
```

The object becomes eligible for GC.

---

# GC Roots

Garbage collection starts from **GC Roots**.

Anything reachable from a GC Root is **alive**.

```text
GC Roots

     │

     ▼

Object A

     │

     ▼

Object B

     │

     ▼

Object C
```

All reachable objects survive.

---

## Common GC Roots

* Local variables on thread stacks
* Static fields
* Active threads
* JNI references
* Loaded classes

---

## Reachability Example

```java
Employee e = new Employee();

Employee x = e;
```

Memory:

```text
GC Root

  │

  ▼

Employee

  ▲

  │

  x
```

Object survives.

---

Now:

```java
e = null;
x = null;
```

```text
Employee
```

No references remain.

Eligible for GC.

---

# Minor GC

Runs on the Young Generation.

```text
Young

↓

Collect

↓

Move survivors
```

Fast and frequent.

---

# Major (Old Generation) GC

Runs on the Old Generation.

Slower because it scans more memory.

---

# Full GC

Collects the entire heap and often class metadata as well.

Usually the slowest GC event and something you generally want to minimize in production systems.

---

# Interview Questions

### Why is the stack faster than the heap?

The stack uses a simple Last-In-First-Out (LIFO) allocation strategy. Allocating and freeing memory is just moving the stack pointer, whereas heap allocation and garbage collection are more complex.

---

### Why is Metaspace better than PermGen?

Metaspace uses native memory rather than a fixed-size JVM memory region. This reduces `OutOfMemoryError: PermGen space` issues and allows metadata storage to grow (within configured limits).

---

### Why doesn't the JVM use reference counting for GC?

Reference counting cannot reclaim **cyclic references**.

Example:

```java
class A {
    B b;
}

class B {
    A a;
}
```

If no GC roots reference these objects, they're unreachable even though they reference each other. Modern JVM GCs use **reachability analysis from GC Roots**, which correctly collects such cycles.

---

### What are GC Roots?

Objects directly referenced from:

* Thread stacks (local variables)
* Static fields
* Active threads
* JNI references
* Other JVM-internal root references

GC starts traversing the object graph from these roots.

---

### Why does Java use generations?

Most objects die young (the **weak generational hypothesis**).

By separating young and old objects:

* Minor GCs are fast because they scan only the Young Generation.
* Long-lived objects are promoted to the Old Generation.
* Overall GC performance improves significantly.

---

# Best Practices

* Avoid creating unnecessary temporary objects in hot paths.
* Release references to objects that are no longer needed.
* Reuse expensive objects where appropriate (for example, thread pools or connection pools).
* Understand object allocation patterns before tuning the JVM.
* Monitor heap usage and GC behavior using profiling tools rather than guessing.

---

# Complete JVM Architecture

```text
                 Java Source
                      │
                    javac
                      │
                  Bytecode
                      │
               +----------------+
               |  ClassLoader   |
               +----------------+
                      │
          Loading → Linking → Initialization
                      │
               +----------------+
               | Runtime Memory |
               +----------------+
        Heap | Stack | Metaspace
        PC Register | Native Stack
                      │
               +----------------+
               | Execution Engine|
               +----------------+
        Interpreter | JIT | GC
                      │
                    CPU
```

---

# Senior Interview Cheat Sheet

| Component             | Responsibility                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------ |
| ClassLoader           | Loads `.class` files into the JVM                                                                |
| Bootstrap ClassLoader | Loads core Java classes                                                                          |
| Heap                  | Stores objects and arrays                                                                        |
| Stack                 | Stores method frames and local variables                                                         |
| Metaspace             | Stores class metadata                                                                            |
| PC Register           | Tracks the current bytecode instruction for each thread                                          |
| Native Method Stack   | Supports execution of native methods                                                             |
| Interpreter           | Executes bytecode instruction by instruction                                                     |
| JIT Compiler          | Compiles frequently executed code into native machine code                                       |
| GC Roots              | Starting points for reachability analysis                                                        |
| Minor GC              | Collects the Young Generation                                                                    |
| Major GC              | Primarily collects the Old Generation                                                            |
| Full GC               | Collects the entire heap (and may reclaim class metadata), typically with the highest pause cost |

### Senior Interview Tip

A favorite interview question is:

> **"Where is an object stored, and where is its reference stored?"**

For example:

```java
Employee e = new Employee();
```

* The **`Employee` object** is allocated in the **Heap**.
* The **reference variable `e`** is stored in the **current method's stack frame** (on the thread's **Stack**).
* The **`Employee` class metadata** (fields, methods, constant pool information, etc.) resides in **Metaspace**.

This distinction demonstrates a clear understanding of the JVM memory model.
