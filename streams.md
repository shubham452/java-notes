# 📚 Complete Guide to Java Streams API

## 1️⃣ Introduction to Streams

Introduced in **Java 8**, the Streams API provides a **functional approach** to processing sequences of elements (like collections) in a declarative way. Streams enable **concise**, **readable**, and often **parallelizable** operations on data.

### 🎯 Why Streams?

| Problem with Traditional Loops | Streams Solution |
|--------------------------------|------------------|
| **Imperative style** – verbose, focus on *how* | **Declarative style** – focus on *what* |
| **Difficult to parallelize** – manual thread management | **Parallel streams** – automatic parallelization |
| **Hard to read** – nested loops and conditions | **Method chaining** – fluent, readable pipeline |
| **Code duplication** – similar loops repeated | **Reusable operations** – filter, map, reduce |

### ✅ Simple Example: Filtering and Collecting

```java
// Traditional way
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");
List<String> result = new ArrayList<>();
for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name.toUpperCase());
    }
}
System.out.println(result);

// Stream way
List<String> resultStream = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());
System.out.println(resultStream);
```

Both produce: `[ALICE]`

---

## 2️⃣ Stream Pipeline

A stream pipeline consists of three parts:

1. **Source** – data source (collection, array, generator, I/O)
2. **Zero or more intermediate operations** – transform the stream (lazy)
3. **Terminal operation** – produces result or side effect (eager)

```
source.stream()
      .intermediate1()
      .intermediate2()
      ...
      .terminalOperation();
```

**Key Characteristics:**
- Streams are **not data structures**; they carry values from a source.
- Intermediate operations are **lazy** – they don't execute until a terminal operation is invoked.
- Streams are **consumable** – can be used only once; after terminal operation, stream is closed.

---

## 3️⃣ Creating Streams

### 3.1. From Collections

```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> streamFromList = list.stream();
Stream<String> parallelStream = list.parallelStream(); // parallel
```

### 3.2. From Arrays

```java
String[] array = {"a", "b", "c"};
Stream<String> streamFromArray = Arrays.stream(array);
Stream<String> streamFromArrayRange = Arrays.stream(array, 0, 2); // "a", "b"
```

### 3.3. From Values (Stream.of)

```java
Stream<Integer> numberStream = Stream.of(1, 2, 3);
Stream<String> emptyStream = Stream.empty();
```

### 3.4. From Ranges (IntStream, LongStream)

```java
IntStream intStream = IntStream.range(1, 5);        // 1,2,3,4
IntStream intStreamClosed = IntStream.rangeClosed(1, 5); // 1,2,3,4,5
```

### 3.5. Infinite Streams (generate, iterate)

```java
// Stream.generate – infinite supply
Stream<Double> randoms = Stream.generate(Math::random).limit(5);

// Stream.iterate – seed and UnaryOperator
Stream<Integer> evens = Stream.iterate(0, n -> n + 2).limit(5); // 0,2,4,6,8

// Java 9+ iterate with predicate
Stream<Integer> evensUntil10 = Stream.iterate(0, n -> n < 10, n -> n + 2);
```

### 3.6. From Files (lines)

```java
try (Stream<String> lines = Files.lines(Paths.get("file.txt"))) {
    lines.forEach(System.out::println);
} catch (IOException e) { e.printStackTrace(); }
```

### 3.7. From StringBuilder/Regex

```java
Pattern pattern = Pattern.compile(" ");
Stream<String> words = pattern.splitAsStream("Hello World Java");
```

---

## 4️⃣ Intermediate Operations

Intermediate operations transform a stream into another stream. They are **lazy**.

### 4.1. `filter(Predicate<T>)`

Selects elements matching a condition.

```java
Stream<Integer> evenNumbers = Stream.of(1,2,3,4,5,6)
    .filter(n -> n % 2 == 0); // 2,4,6
```

### 4.2. `map(Function<T, R>)`

Transforms each element.

```java
Stream<String> upperNames = Stream.of("alice", "bob")
    .map(String::toUpperCase); // "ALICE", "BOB"
```

### 4.3. `flatMap(Function<T, Stream<R>>)`

Flattens nested structures. Often used with collections inside collections.

```java
List<List<String>> listOfLists = Arrays.asList(
    Arrays.asList("a", "b"),
    Arrays.asList("c", "d")
);

Stream<String> flatStream = listOfLists.stream()
    .flatMap(List::stream); // "a","b","c","d"
```

**Example: Splitting strings**

```java
Stream<String> words = Stream.of("Hello World", "Java Streams")
    .flatMap(line -> Arrays.stream(line.split(" ")));
// "Hello", "World", "Java", "Streams"
```

### 4.4. `distinct()`

Removes duplicates (uses `equals()`).

```java
Stream<Integer> unique = Stream.of(1,2,2,3,3,3).distinct(); // 1,2,3
```

### 4.5. `sorted()`, `sorted(Comparator)`

Sorts elements. Without comparator uses natural order.

```java
Stream<Integer> sorted = Stream.of(5,3,1,4,2).sorted(); // 1,2,3,4,5
Stream<String> reverseSorted = Stream.of("a","c","b")
    .sorted(Comparator.reverseOrder()); // "c","b","a"
```

### 4.6. `peek(Consumer<T>)`

Performs action on each element (useful for debugging).

```java
Stream.of("one", "two")
    .peek(e -> System.out.println("Before: " + e))
    .map(String::toUpperCase)
    .peek(e -> System.out.println("After: " + e))
    .collect(Collectors.toList());
```

### 4.7. `limit(long maxSize)`

Truncates the stream to at most maxSize elements.

```java
Stream.iterate(1, n -> n+1).limit(5); // 1,2,3,4,5
```

### 4.8. `skip(long n)`

Discards first n elements.

```java
Stream.of(1,2,3,4,5).skip(2); // 3,4,5
```

### 4.9. `takeWhile(Predicate)` (Java 9+)

Takes elements while predicate is true (then stops).

```java
Stream.of(1,2,3,4,5,6).takeWhile(n -> n < 4); // 1,2,3
```

### 4.10. `dropWhile(Predicate)` (Java 9+)

Drops elements while predicate is true, then takes the rest.

```java
Stream.of(1,2,3,4,5,6).dropWhile(n -> n < 4); // 4,5,6
```

---

## 5️⃣ Terminal Operations

Terminal operations produce a result or side effect and close the stream.

### 5.1. `forEach(Consumer)`

Performs action on each element (order not guaranteed in parallel).

```java
Stream.of("a","b","c").forEach(System.out::println);
```

### 5.2. `forEachOrdered(Consumer)`

Respects encounter order even in parallel streams.

### 5.3. `toList()` (Java 16+) / `collect(Collectors.toList())`

Collects elements into a List.

```java
List<String> list = Stream.of("a","b").toList(); // Java 16+
List<String> list2 = Stream.of("a","b").collect(Collectors.toList());
```

### 5.4. `toArray()`

Collects into an array.

```java
String[] array = Stream.of("a","b").toArray(String[]::new);
```

### 5.5. `reduce(BinaryOperator)` / `reduce(identity, BinaryOperator)`

Combines elements to a single value using an associative accumulation function.

```java
// Sum of integers
Optional<Integer> sum = Stream.of(1,2,3,4).reduce(Integer::sum);
System.out.println(sum.get()); // 10

// With identity (provides default and ensures result type)
int sum2 = Stream.of(1,2,3,4).reduce(0, Integer::sum); // 10

// Concatenating strings
String concat = Stream.of("a","b","c").reduce("", (s1,s2) -> s1 + s2); // "abc"
```

### 5.6. `collect(Collector)`

Most flexible terminal operation; uses Collectors utilities.

### 5.7. `count()`

Returns number of elements.

```java
long count = Stream.of(1,2,3).count(); // 3
```

### 5.8. `anyMatch(Predicate)`, `allMatch(Predicate)`, `noneMatch(Predicate)`

Short‑circuiting boolean checks.

```java
boolean hasEven = Stream.of(1,2,3).anyMatch(n -> n % 2 == 0); // true
boolean allEven = Stream.of(2,4,6).allMatch(n -> n % 2 == 0); // true
boolean noneNegative = Stream.of(1,2,-3).noneMatch(n -> n < 0); // false
```

### 5.9. `findFirst()`, `findAny()`

Returns an `Optional` describing the first/any element.

```java
Optional<Integer> first = Stream.of(5,2,3).findFirst(); // Optional[5]
Optional<Integer> any = stream.parallel().findAny(); // any element (faster)
```

### 5.10. `min(Comparator)`, `max(Comparator)`

Returns min/max element according to comparator.

```java
Optional<Integer> min = Stream.of(5,2,3).min(Integer::compareTo); // 2
Optional<Integer> max = Stream.of(5,2,3).max(Integer::compareTo); // 5
```

### 5.11. `collect(Collectors.toSet())`

Collects into a Set (no duplicates).

### 5.12. `collect(Collectors.joining())`

For `CharSequence` streams, concatenates into a String.

```java
String joined = Stream.of("a","b","c").collect(Collectors.joining(", ")); // "a, b, c"
```

---

## 6️⃣ Collectors – Detailed Examples

`Collectors` is a utility class providing many predefined collectors.

### 6.1. `toList()`, `toSet()`, `toCollection()`

```java
List<String> list = stream.collect(Collectors.toList());
Set<String> set = stream.collect(Collectors.toSet());
TreeSet<String> treeSet = stream.collect(Collectors.toCollection(TreeSet::new));
```

### 6.2. `toMap()`

```java
// keyMapper, valueMapper
Map<Integer, String> map = Stream.of("a", "bb", "ccc")
    .collect(Collectors.toMap(String::length, Function.identity()));

// If duplicate keys, need merge function
Map<Integer, String> mapWithMerge = Stream.of("a", "b", "cc")
    .collect(Collectors.toMap(
        String::length,
        Function.identity(),
        (existing, replacement) -> existing + "," + replacement
    ));
// {1="a,b", 2="cc"}
```

### 6.3. `groupingBy()`

Group elements by a classifier function.

```java
Map<Integer, List<String>> byLength = Stream.of("a", "bb", "c", "dd")
    .collect(Collectors.groupingBy(String::length));
// {1=["a","c"], 2=["bb","dd"]}

// Grouping with downstream collector (e.g., counting)
Map<Integer, Long> countByLength = Stream.of("a", "bb", "c", "dd")
    .collect(Collectors.groupingBy(String::length, Collectors.counting()));
// {1=2, 2=2}
```

### 6.4. `partitioningBy()`

Partitions into two groups based on a predicate.

```java
Map<Boolean, List<Integer>> evenOdd = Stream.of(1,2,3,4)
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
// {false=[1,3], true=[2,4]}
```

### 6.5. `summarizingInt()`, `averagingInt()`, etc.

```java
IntSummaryStatistics stats = Stream.of(1,2,3,4)
    .collect(Collectors.summarizingInt(Integer::intValue));
System.out.println(stats); // count=4, sum=10, min=1, max=4, avg=2.5
```

### 6.6. `joining()`

```java
String csv = Stream.of("a", "b", "c")
    .collect(Collectors.joining(", ", "[", "]"));
// "[a, b, c]"
```

### 6.7. `reducing()`

General reduction collector.

```java
Optional<Integer> sum = stream.collect(Collectors.reducing(Integer::sum));
```

---

## 7️⃣ Parallel Streams

Parallel streams leverage multiple threads to process data concurrently.

### 7.1. Creating Parallel Streams

```java
// From collection
list.parallelStream()

// Convert existing stream
stream.parallel()
```

### 7.2. When to Use Parallel Streams

- Large dataset (thousands of elements)
- CPU‑intensive operations (no I/O)
- Operations are independent (no shared mutable state)
- Order not critical (or use `forEachOrdered`)

### 7.3. Example: Parallel Sum

```java
long sum = LongStream.rangeClosed(1, 10_000_000)
    .parallel()
    .sum();
```

### 7.4. Performance Pitfalls

- Overhead of splitting and merging can outweigh gains for small datasets.
- Operations like `limit`, `findFirst` may be slower due to ordering requirements.
- Avoid shared mutable state – use reduction or concurrent collections.

### 7.5. Forcing Sequential

```java
stream.parallel().sequential(); // back to sequential
```

---

## 8️⃣ Primitive Streams

Specialized streams for primitives to avoid boxing overhead.

| Primitive Stream | Wrapper Stream | Key Methods |
|------------------|----------------|-------------|
| `IntStream` | `Stream<Integer>` | `sum()`, `average()`, `range()`, `rangeClosed()` |
| `LongStream` | `Stream<Long>` | `sum()`, `average()`, `range()`, `rangeClosed()` |
| `DoubleStream` | `Stream<Double>` | `sum()`, `average()`, `summaryStatistics()` |

### 8.1. Creating Primitive Streams

```java
IntStream ints = IntStream.of(1,2,3);
IntStream range = IntStream.rangeClosed(1, 10);
IntStream generated = IntStream.generate(() -> 1).limit(5);

DoubleStream doubles = Arrays.stream(new double[]{1.1, 2.2});
```

### 8.2. Converting Between Streams

```java
// Stream to IntStream
IntStream intStream = Stream.of("1","2","3")
    .mapToInt(Integer::parseInt);

// IntStream to Stream<Integer> (boxed)
Stream<Integer> boxed = IntStream.range(1,5).boxed();

// Primitive stream to array
int[] array = IntStream.range(1,5).toArray();
```

### 8.3. Terminal Operations on Primitive Streams

```java
int sum = IntStream.range(1,5).sum(); // 10
double avg = IntStream.range(1,5).average().getAsDouble(); // 2.5
IntSummaryStatistics stats = IntStream.range(1,5).summaryStatistics();
```

---

## 9️⃣ Stream Pitfalls & Best Practices

### ❌ Pitfalls

1. **Reusing a stream** – causes `IllegalStateException`.
   ```java
   Stream<String> s = list.stream();
   s.forEach(...);
   s.forEach(...); // ERROR
   ```

2. **Infinite streams without limit** – terminal operation never ends.
   ```java
   Stream.generate(Math::random).forEach(System.out::println); // never stops
   ```

3. **Modifying source while streaming** – may cause `ConcurrentModificationException` (except concurrent collections).
   ```java
   List<String> list = new ArrayList<>(Arrays.asList("a","b"));
   list.stream().forEach(e -> list.remove(e)); // BAD
   ```

4. **Using parallel streams with shared mutable state** – data corruption.
   ```java
   int[] sum = {0};
   IntStream.range(0,1000).parallel().forEach(i -> sum[0] += i); // race condition
   ```

5. **Heavy operations inside `peek`** – `peek` is meant for debugging, not for processing.

### ✅ Best Practices

- **Use method references** where possible for readability.
- **Prefer primitive streams** for numeric operations to avoid boxing.
- **Use `collect()` instead of `forEach()` for accumulating results**.
- **Keep operations stateless** in parallel streams.
- **Use `findFirst()` when order matters, `findAny()` when it doesn't**.
- **Chain operations logically**: `filter` early to reduce data.
- **Don't overuse parallel streams**; measure performance first.

---

## 🔟 Stream vs Collections

| Feature | Stream | Collection |
|---------|--------|------------|
| **Storage** | No storage; conveys data from source | Stores elements |
| **Purpose** | Processing/transformation | Data structure |
| **Laziness** | Operations are lazy | Eager |
| **Reusability** | Single-use | Multi-use |
| **Data modification** | Does not modify source | Can modify elements |

---

## 1️⃣1️⃣ Complete Real-World Example

```java
import java.util.*;
import java.util.stream.*;

class Employee {
    String name;
    String dept;
    double salary;

    Employee(String name, String dept, double salary) {
        this.name = name;
        this.dept = dept;
        this.salary = salary;
    }

    public String getDept() { return dept; }
    public double getSalary() { return salary; }
    public String getName() { return name; }
    public String toString() { return name; }
}

public class StreamDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Alice", "IT", 85000),
            new Employee("Bob", "HR", 60000),
            new Employee("Charlie", "IT", 95000),
            new Employee("David", "Finance", 70000),
            new Employee("Eve", "HR", 65000)
        );

        // 1. Average salary per department
        Map<String, Double> avgSalaryByDept = employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDept,
                Collectors.averagingDouble(Employee::getSalary)
            ));
        System.out.println("Avg salary by dept: " + avgSalaryByDept);

        // 2. Highest paid employee in each department
        Map<String, Optional<Employee>> highestPaid = employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDept,
                Collectors.maxBy(Comparator.comparingDouble(Employee::getSalary))
            ));
        System.out.println("Highest paid by dept: " + highestPaid);

        // 3. Names of all employees earning > 70k, sorted
        List<String> highEarners = employees.stream()
            .filter(e -> e.getSalary() > 70000)
            .map(Employee::getName)
            .sorted()
            .collect(Collectors.toList());
        System.out.println("High earners: " + highEarners);

        // 4. Total payroll
        double totalPayroll = employees.stream()
            .mapToDouble(Employee::getSalary)
            .sum();
        System.out.println("Total payroll: " + totalPayroll);

        // 5. Any employee in IT?
        boolean hasIT = employees.stream()
            .anyMatch(e -> "IT".equals(e.getDept()));
        System.out.println("Has IT employee? " + hasIT);
    }
}
```

---

## 1️⃣2️⃣ Summary

| Concept | Description |
|---------|-------------|
| **Stream** | Sequence of elements supporting functional-style operations |
| **Source** | Collection, array, generator, I/O |
| **Intermediate ops** | Transform stream (lazy) – `filter`, `map`, `flatMap`, `sorted`, etc. |
| **Terminal ops** | Produce result – `collect`, `forEach`, `reduce`, `count`, etc. |
| **Collectors** | Utility for gathering results into collections, maps, or summaries |
| **Parallel streams** | `parallelStream()` for concurrent processing |
| **Primitive streams** | `IntStream`, `LongStream`, `DoubleStream` for efficiency |

Streams bring **functional programming** to Java, making code **declarative**, **concise**, and often **more efficient**. Mastering the Streams API is essential for modern Java development.
