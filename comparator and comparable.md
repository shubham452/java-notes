# 📚 Complete Guide to Comparator & Comparable in Java

## 1️⃣ Introduction to Sorting in Java

Sorting is a fundamental operation in programming. In Java, we have two primary interfaces to define sorting order:

| Interface | Package | Purpose |
|-----------|---------|---------|
| **`Comparable`** | `java.lang` | Defines **natural ordering** of objects (e.g., numerical order, alphabetical order). |
| **`Comparator`** | `java.util` | Defines **custom ordering**; can be used when natural ordering is not desired or not available. |

---

## 2️⃣ The `Comparable` Interface

### 📌 Definition
`Comparable` is used to impose a **natural ordering** on objects of a class. The class itself must implement this interface and override the `compareTo()` method.

### 🔧 Syntax
```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

### 📊 Return Value of `compareTo()`
- **negative integer** : `this` object is less than the specified object
- **zero** : `this` object is equal to the specified object
- **positive integer** : `this` object is greater than the specified object

### ✅ Example: Implementing Comparable

```java
import java.util.*;

class Student implements Comparable<Student> {
    private int id;
    private String name;
    private double gpa;

    public Student(int id, String name, double gpa) {
        this.id = id;
        this.name = name;
        this.gpa = gpa;
    }

    // Getters (optional but useful)
    public int getId() { return id; }
    public String getName() { return name; }
    public double getGpa() { return gpa; }

    // Natural ordering by ID
    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.id, other.id);
    }

    @Override
    public String toString() {
        return String.format("Student{id=%d, name='%s', gpa=%.2f}", id, name, gpa);
    }

    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student(103, "Alice", 3.8));
        students.add(new Student(101, "Bob", 3.5));
        students.add(new Student(102, "Charlie", 3.9));

        System.out.println("Before sorting: " + students);
        Collections.sort(students); // Uses natural ordering (by ID)
        System.out.println("After sorting by ID: " + students);
    }
}
```

**Output:**
```
Before sorting: [Student{id=103, name='Alice', gpa=3.80}, Student{id=101, name='Bob', gpa=3.50}, Student{id=102, name='Charlie', gpa=3.90}]
After sorting by ID: [Student{id=101, name='Bob', gpa=3.50}, Student{id=102, name='Charlie', gpa=3.90}, Student{id=103, name='Alice', gpa=3.80}]
```

### ⚠️ Important Rules
- `compareTo()` must be **consistent with equals()** (recommended but not strictly required).
- Should throw `NullPointerException` if argument is null (unless designed to handle null).
- Must ensure transitivity, reflexivity, symmetry.

---

## 3️⃣ The `Comparator` Interface

### 📌 Definition
`Comparator` is used to define **custom ordering** externally. It's a separate class or lambda that implements `compare()` method.

### 🔧 Syntax
```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
    // many default and static methods
}
```

### 📊 Return Value of `compare()`
- **negative** : first argument is less than second
- **zero** : first argument equals second
- **positive** : first argument is greater than second

### ✅ Example: Multiple Comparators for the Same Class

```java
import java.util.*;

class Student {
    private int id;
    private String name;
    private double gpa;

    public Student(int id, String name, double gpa) {
        this.id = id;
        this.name = name;
        this.gpa = gpa;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public double getGpa() { return gpa; }

    @Override
    public String toString() {
        return String.format("Student{id=%d, name='%s', gpa=%.2f}", id, name, gpa);
    }
}

// Comparator implementations
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.getName().compareTo(s2.getName());
    }
}

class GpaComparator implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return Double.compare(s2.getGpa(), s1.getGpa()); // descending
    }
}

public class ComparatorDemo {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student(103, "Alice", 3.8));
        students.add(new Student(101, "Bob", 3.5));
        students.add(new Student(102, "Charlie", 3.9));
        students.add(new Student(104, "Alice", 3.7)); // duplicate name

        System.out.println("Original: " + students);

        // Using custom comparators
        students.sort(new NameComparator());
        System.out.println("Sorted by name: " + students);

        students.sort(new GpaComparator());
        System.out.println("Sorted by GPA (desc): " + students);
    }
}
```

---

## 4️⃣ Comparator with Lambda Expressions (Java 8+)

Since `Comparator` is a functional interface, we can use lambda expressions for concise code.

```java
// Instead of creating separate classes:
students.sort((s1, s2) -> s1.getName().compareTo(s2.getName()));

// Or using Comparator.comparing() helper:
students.sort(Comparator.comparing(Student::getName));

// Descending:
students.sort(Comparator.comparing(Student::getGpa).reversed());

// Multiple criteria:
students.sort(Comparator.comparing(Student::getName)
                       .thenComparing(Student::getId));
```

---

## 5️⃣ Comparator Factory Methods (Java 8+)

The `Comparator` interface provides many static and default methods for easy construction.

### 5.1. `comparing()` – Extracting keys

```java
Comparator<Student> byName = Comparator.comparing(Student::getName);
Comparator<Student> byId = Comparator.comparing(Student::getId);
Comparator<Student> byGpa = Comparator.comparingDouble(Student::getGpa);
```

### 5.2. `reversed()` – Reverse order

```java
students.sort(Comparator.comparing(Student::getName).reversed());
```

### 5.3. `thenComparing()` – Chaining

```java
Comparator<Student> byNameThenId = 
    Comparator.comparing(Student::getName)
              .thenComparing(Student::getId);
```

### 5.4. `nullsFirst()` / `nullsLast()` – Handling nulls

```java
List<String> names = Arrays.asList("Alice", null, "Bob", "Charlie");
names.sort(Comparator.nullsLast(String::compareTo));
System.out.println(names); // [Alice, Bob, Charlie, null]
```

### 5.5. `naturalOrder()` / `reverseOrder()`

```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1);
numbers.sort(Comparator.naturalOrder());   // ascending
numbers.sort(Comparator.reverseOrder());   // descending
```

### 5.6. `comparingInt()`, `comparingLong()`, `comparingDouble()` – for primitives

Avoids auto-boxing overhead.

```java
Comparator<Student> byId = Comparator.comparingInt(Student::getId);
Comparator<Student> byGpa = Comparator.comparingDouble(Student::getGpa);
```

---

## 6️⃣ Comprehensive Example with Multiple Criteria

```java
import java.util.*;

class Employee {
    private String name;
    private String department;
    private double salary;
    private int age;

    // constructor, getters, toString
    public Employee(String name, String department, double salary, int age) {
        this.name = name;
        this.department = department;
        this.salary = salary;
        this.age = age;
    }

    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
    public int getAge() { return age; }

    @Override
    public String toString() {
        return String.format("%s(%s, $%.0f, %d)", name, department, salary, age);
    }
}

public class AdvancedComparatorDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Alice", "IT", 85000, 30),
            new Employee("Bob", "HR", 65000, 25),
            new Employee("Charlie", "IT", 90000, 28),
            new Employee("David", "Finance", 75000, 35),
            new Employee("Eve", "HR", 70000, 32)
        );

        System.out.println("Original: " + employees);

        // 1. Sort by department, then by salary descending
        Comparator<Employee> deptThenSalaryDesc =
            Comparator.comparing(Employee::getDepartment)
                     .thenComparing(Comparator.comparingDouble(Employee::getSalary).reversed());

        employees.sort(deptThenSalaryDesc);
        System.out.println("\nBy Department, then Salary (desc):\n" + employees);

        // 2. Sort by salary, then age, with null-safe (no nulls here)
        employees.sort(Comparator.comparingDouble(Employee::getSalary)
                                 .thenComparingInt(Employee::getAge));
        System.out.println("\nBy Salary, then Age:\n" + employees);

        // 3. Using stream.sorted()
        List<Employee> sorted = employees.stream()
            .sorted(Comparator.comparing(Employee::getName))
            .toList();
        System.out.println("\nBy Name (stream):\n" + sorted);
    }
}
```

---

## 7️⃣ Comparator for Maps

Sorting maps by keys or values using Comparator.

```java
Map<String, Integer> scores = new HashMap<>();
scores.put("Alice", 95);
scores.put("Bob", 87);
scores.put("Charlie", 92);

// Sort by value (descending)
List<Map.Entry<String, Integer>> entries = new ArrayList<>(scores.entrySet());
entries.sort(Map.Entry.comparingByValue(Comparator.reverseOrder()));

System.out.println("Sorted by value (desc): " + entries);

// Or using Stream
scores.entrySet().stream()
      .sorted(Map.Entry.comparingByKey())
      .forEach(e -> System.out.println(e.getKey() + " -> " + e.getValue()));
```

---

## 8️⃣ Comparator for Arrays

Works with `Arrays.sort()` as well.

```java
String[] fruits = {"Banana", "Apple", "Cherry", "Date"};
Arrays.sort(fruits, Comparator.comparingInt(String::length));
System.out.println(Arrays.toString(fruits)); // [Date, Apple, Banana, Cherry]
```

---

## 9️⃣ Comparison: Comparable vs Comparator

| Feature | Comparable | Comparator |
|---------|------------|------------|
| **Package** | `java.lang` | `java.util` |
| **Method** | `int compareTo(T o)` | `int compare(T o1, T o2)` |
| **When to use** | Natural ordering (e.g., by ID) | Custom ordering (multiple ways) |
| **Modifies class?** | Yes, class must implement it | No, separate class/lambda |
| **Single/Multiple** | Single sorting sequence | Can have many comparators |
| **Impact on API** | Part of class definition | External, flexible |

---

## 🔟 Best Practices & Pitfalls

### ✅ Do's
- Always override `hashCode()` and `equals()` when using custom objects in sorted collections.
- Use `Comparator.comparing()` and method references for clarity.
- Use primitive specializations (`comparingInt`, etc.) for performance.
- Handle nulls explicitly with `nullsFirst`/`nullsLast` if needed.
- Ensure comparators are consistent with equals (if used in `SortedSet` or `SortedMap`).

### ❌ Don'ts
- Don't use `return o1.compareTo(o2)` if o1 could be null.
- Don't assume `compareTo` is consistent with `equals` unless documented.
- Avoid mutable fields in comparison keys.
- Don't forget to import `java.util.Comparator`.

---

## 📝 Summary

- **`Comparable`** defines **internal** natural order.
- **`Comparator`** defines **external** custom order.
- Java 8+ provides fluent Comparator building with lambda-friendly methods.
- Use chaining (`thenComparing`) for multi-level sorting.
- Leverage `nullsFirst`/`nullsLast` for null-safe sorting.
- Stream API integrates seamlessly with `sorted(Comparator)`.

Mastering Comparators allows you to sort any collection in any order, making your code more flexible and expressive.
