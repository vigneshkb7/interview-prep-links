This is an important topic for **Senior Java interviews**, especially at companies like Walmart, Adobe, Oracle, and Amazon. Interviewers expect you to understand not only **using annotations** but also **how they work internally** and **annotation processing**.

---

# What are Annotations?

Annotations provide **metadata** about Java code.

They **do not directly change program behavior**. Instead, they are read by:

* The compiler
* The JVM
* Frameworks (e.g., Spring, Hibernate)
* Annotation processors

Example:

```java id="y6xq7f"
@Override
public String toString() {
    return "Employee";
}
```

Here, `@Override` tells the compiler that the method should override a superclass method.

---

# Why Use Annotations?

Without annotations:

```java id="2pr7e6"
public class UserService {

    public void saveUser() {
        // ...
    }
}
```

With annotations:

```java id="e9x3wm"
@Service
public class UserService {

    @Transactional
    public void saveUser() {
        // ...
    }
}
```

The annotations provide metadata that frameworks can interpret.

---

# Built-in Annotations

Java provides several built-in annotations.

---

## 1. `@Override`

Checks that a method actually overrides a superclass or interface method.

Correct:

```java id="hmifqq"
class Parent {

    void display() {}

}

class Child extends Parent {

    @Override
    void display() {}

}
```

Incorrect:

```java id="9ggqoe"
class Parent {

    void show() {}

}

class Child extends Parent {

    @Override
    void display() {}

}
```

Compile-time error because no method is overridden.

---

## 2. `@Deprecated`

Marks code as deprecated.

```java id="uy05ca"
class Demo {

    @Deprecated
    void oldMethod() {}

}
```

Calling it:

```java id="gvuyh6"
Demo d = new Demo();

d.oldMethod();
```

Produces a compiler warning.

---

## 3. `@SuppressWarnings`

Suppresses specific compiler warnings.

```java id="jlwm5l"
@SuppressWarnings("unchecked")
List list = new ArrayList();
```

Common values:

* `"unchecked"`
* `"deprecation"`
* `"rawtypes"`

Use it sparingly and only when you understand the warning.

---

## 4. `@FunctionalInterface`

Ensures an interface has exactly one abstract method.

```java id="6pmlgu"
@FunctionalInterface
interface Calculator {

    int add(int a, int b);

}
```

Adding another abstract method causes a compile-time error.

---

## 5. `@SafeVarargs`

Suppresses warnings for safe use of generic varargs.

```java id="wxfqkz"
@SafeVarargs
static <T> void print(T... values) {

    for (T value : values) {
        System.out.println(value);
    }
}
```

Applicable only to methods where the compiler can trust the implementation (such as `static`, `final`, or `private` methods).

---

# Meta-Annotations

Meta-annotations are **annotations applied to annotation types**.

They define how an annotation behaves.

---

## `@Target`

Specifies where an annotation can be used.

```java id="upm7gq"
@Target(ElementType.METHOD)
@interface Log {}
```

Now it can only annotate methods.

Common `ElementType` values:

* `TYPE`
* `METHOD`
* `FIELD`
* `PARAMETER`
* `CONSTRUCTOR`
* `LOCAL_VARIABLE`
* `ANNOTATION_TYPE`

---

## `@Retention`

Specifies how long an annotation is retained.

```java id="od2qk0"
@Retention(RetentionPolicy.RUNTIME)
```

Options:

### `SOURCE`

```text id="dghwx3"
.java
   ↓
Compiler
   ↓
Removed
```

Example:

* `@Override`

---

### `CLASS`

Stored in the `.class` file but not available through reflection at runtime.

---

### `RUNTIME`

Available to the JVM and accessible through reflection.

Used by frameworks like Spring and Hibernate.

---

## `@Documented`

Includes the annotation in generated API documentation.

```java id="dnb2zu"
@Documented
@interface MyAnnotation {}
```

---

## `@Inherited`

Allows subclasses to inherit a class-level annotation.

```java id="30n7zx"
@Inherited
@interface MyAnnotation {}
```

Only applies to class annotations, not methods or fields.

---

## `@Repeatable`

Allows the same annotation to appear multiple times.

```java id="ud0q2n"
@Repeatable(Roles.class)
@interface Role {

    String value();

}
```

Usage:

```java id="ncbvbi"
@Role("ADMIN")
@Role("USER")
class Employee {}
```

---

# Creating a Custom Annotation

```java id="q50vbc"
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface LogExecution {

    String value() default "";

}
```

Usage:

```java id="2wz4dg"
class Service {

    @LogExecution("save")
    void save() {}

}
```

---

# Reading Annotations with Reflection

```java id="zwk0ke"
Method method = Service.class.getDeclaredMethod("save");

LogExecution annotation =
    method.getAnnotation(LogExecution.class);

System.out.println(annotation.value());
```

Output:

```text id="qzk9jp"
save
```

This works because the annotation has `RetentionPolicy.RUNTIME`.

---

# Annotation Processing

Annotations can also be processed **at compile time**.

Examples:

* Lombok
* MapStruct
* Dagger
* AutoValue

These tools generate Java source code during compilation.

---

# Annotation Processing Flow

```text id="nm8lfo"
Java Source
      │
      ▼
Compiler
      │
      ▼
Annotation Processor
      │
      ▼
Generated Source Code
      │
      ▼
Compiled Classes
```

---

# Creating a Custom Annotation Processor

A processor extends `AbstractProcessor`.

```java id="i06jlwm"
@SupportedAnnotationTypes("com.example.LogExecution")
@SupportedSourceVersion(SourceVersion.RELEASE_17)
public class LogProcessor extends AbstractProcessor {

    @Override
    public boolean process(
            Set<? extends TypeElement> annotations,
            RoundEnvironment roundEnv) {

        // Process annotations

        return true;
    }
}
```

The compiler invokes `process()` during compilation.

---

# Runtime vs Compile-time Processing

| Runtime                            | Compile-time                               |
| ---------------------------------- | ------------------------------------------ |
| Reflection                         | Annotation Processor API                   |
| Slight runtime overhead            | No runtime processing cost                 |
| Used by Spring                     | Used by Lombok, MapStruct                  |
| Requires `RetentionPolicy.RUNTIME` | Usually uses `SOURCE` or `CLASS` retention |

---

# Real-world Examples

### Spring

```java id="f5owfh"
@Service
class UserService {}
```

Spring scans classes at runtime and creates beans.

---

### Hibernate

```java id="qrz17l"
@Entity
class Employee {}
```

Hibernate maps the class to a database table.

---

### Lombok

```java id="v5n12d"
@Getter
@Setter
class Employee {}
```

The annotation processor generates getter and setter methods at compile time.

---

# Interview Questions

### Are annotations executable code?

No. They are metadata. Behavior comes from tools such as the compiler, the JVM, reflection, frameworks, or annotation processors that interpret the metadata.

---

### Difference between `SOURCE`, `CLASS`, and `RUNTIME` retention?

| Retention | Available During                                          |
| --------- | --------------------------------------------------------- |
| `SOURCE`  | Compilation only                                          |
| `CLASS`   | Stored in the `.class` file, not available via reflection |
| `RUNTIME` | Available via reflection at runtime                       |

---

### Why does Spring require `RetentionPolicy.RUNTIME`?

Spring discovers annotations using reflection while the application is running. If an annotation isn't retained at runtime, Spring can't see it.

---

### Why does Lombok not require `RUNTIME` retention?

Lombok runs during compilation using the annotation processing API. It generates code before the application runs, so the annotation doesn't need to be available at runtime.

---

### Difference between Reflection and Annotation Processing?

| Reflection                           | Annotation Processing                   |
| ------------------------------------ | --------------------------------------- |
| Happens at runtime                   | Happens during compilation              |
| Reads metadata from compiled classes | Reads source-level annotations          |
| Can inspect and invoke code          | Can generate new source files           |
| Used by frameworks like Spring       | Used by tools like Lombok and MapStruct |

---

# Best Practices

* Keep custom annotations focused on a single responsibility.
* Choose the narrowest appropriate `@Target`.
* Use the least permissive `@Retention` needed (`RUNTIME` only when runtime access is required).
* Prefer compile-time code generation when possible to reduce runtime overhead.
* Avoid overusing annotations for business logic; they should describe metadata, not replace clear program structure.

---

# Interview Cheat Sheet

| Concept              | Key Point                                                                               |
| -------------------- | --------------------------------------------------------------------------------------- |
| Annotation           | Metadata attached to code                                                               |
| Built-in annotations | `@Override`, `@Deprecated`, `@SuppressWarnings`, `@FunctionalInterface`, `@SafeVarargs` |
| `@Target`            | Specifies where an annotation can be applied                                            |
| `@Retention`         | Controls how long an annotation is retained                                             |
| `@Inherited`         | Allows subclasses to inherit class-level annotations                                    |
| `@Repeatable`        | Allows multiple instances of the same annotation                                        |
| Reflection           | Reads runtime annotations                                                               |
| Annotation Processor | Processes annotations during compilation                                                |
| Spring               | Runtime annotation processing                                                           |
| Lombok               | Compile-time annotation processing                                                      |

## Senior Interview Tip

A common question is:

> **"How does `@Override` work internally?"**

The answer is:

* `@Override` has **`RetentionPolicy.SOURCE`**.
* During compilation, the Java compiler checks whether the annotated method actually overrides a method from a superclass or interface.
* If it doesn't, compilation fails.
* The annotation is discarded after compilation and is **not present in the `.class` file or at runtime**.

This demonstrates the distinction between **compiler-checked annotations** and **runtime-processed annotations**, a concept interviewers often look for in senior candidates.
