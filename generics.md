# 📚 Complete Guide to Generics in Java

## 1️⃣ Introduction to Generics

**Generics** were introduced in Java 5 (J2SE 5.0) to provide **type safety** and **code reusability** by allowing classes, interfaces, and methods to operate on types specified by the caller.

### 🎯 Why Generics?

| Problem Without Generics | Solution With Generics |
|--------------------------|------------------------|
| **No type safety** – Collections could hold any object, leading to `ClassCastException` at runtime. | **Compile-time type checking** – Errors caught early. |
| **Manual casting** – Required every time an element is retrieved. | **Automatic casting** – Compiler inserts implicit casts. |
| **Code duplication** – Needed separate classes for different types (e.g., `StringList`, `IntegerList`). | **Reusable code** – One generic class works for many types. |

### ✅ Example: Before vs After Generics

```java
// Without Generics (Raw Type)
List list = new ArrayList();
list.add("Hello");
String s = (String) list.get(0); // Explicit cast needed

// With Generics
List<String> list = new ArrayList<>();
list.add("Hello");
String s = list.get(0); // No cast, compiler guarantees type safety
```

---

## 2️⃣ Generic Classes

A generic class is defined with one or more **type parameters** in angle brackets `<>`.

### 📌 Syntax

```java
public class ClassName<T> {
    private T data;
    
    public ClassName(T data) {
        this.data = data;
    }
    
    public T getData() {
        return data;
    }
    
    public void setData(T data) {
        this.data = data;
    }
}
```

### ✅ Example: Generic Box Class

```java
// Generic class with type parameter T
class Box<T> {
    private T content;

    public Box(T content) {
        this.content = content;
    }

    public T getContent() {
        return content;
    }

    public void setContent(T content) {
        this.content = content;
    }

    public void displayType() {
        System.out.println("Type: " + content.getClass().getName());
    }
}

public class GenericClassDemo {
    public static void main(String[] args) {
        // Box for String
        Box<String> stringBox = new Box<>("Hello Generics");
        System.out.println("String content: " + stringBox.getContent());
        stringBox.displayType();

        // Box for Integer
        Box<Integer> intBox = new Box<>(100);
        System.out.println("Integer content: " + intBox.getContent());
        intBox.displayType();

        // Compile-time type safety – uncommenting below causes error
        // stringBox.setContent(123); // ❌ Not allowed
    }
}
```

**Output:**
```
String content: Hello Generics
Type: java.lang.String
Integer content: 100
Type: java.lang.Integer
```

### 🔑 Key Points
- Type parameters follow class name, e.g., `Box<T>`.
- Can use multiple type parameters: `class Pair<K, V> { ... }`
- Cannot use primitive types as type arguments (use wrapper classes: `Integer`, `Double`, etc.)
- Type parameters are placeholders replaced with actual types at instantiation.

---

## 3️⃣ Generic Methods

A **generic method** introduces its own type parameters, independent of the class's type parameters.

### 📌 Syntax

```java
public <T> ReturnType methodName(T parameter) {
    // method body
}
```

### ✅ Example: Generic Method for Array Utilities

```java
public class GenericMethodDemo {

    // Generic method to print any array
    public static <E> void printArray(E[] array) {
        for (E element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }

    // Generic method to find maximum in an array (requires Comparable)
    public static <T extends Comparable<T>> T findMax(T[] array) {
        if (array == null || array.length == 0) return null;
        T max = array[0];
        for (T item : array) {
            if (item.compareTo(max) > 0) {
                max = item;
            }
        }
        return max;
    }

    public static void main(String[] args) {
        Integer[] intArray = {1, 5, 3, 9, 2};
        String[] strArray = {"apple", "orange", "banana", "pear"};

        System.out.print("Integer array: ");
        printArray(intArray); // E is inferred as Integer

        System.out.print("String array: ");
        printArray(strArray); // E is inferred as String

        System.out.println("Max integer: " + findMax(intArray));
        System.out.println("Max string (lexicographic): " + findMax(strArray));
    }
}
```

**Output:**
```
Integer array: 1 5 3 9 2 
String array: apple orange banana pear 
Max integer: 9
Max string (lexicographic): pear
```

### 🔑 Key Points
- Type parameter `<T>` appears **before** return type.
- Can be static or non‑static.
- Type inference: compiler deduces actual type from arguments.
- Can have multiple type parameters: `<T, U> void method(T t, U u)`

---

## 4️⃣ Bounded Type Parameters

Sometimes you need to restrict the types that can be used as type arguments. This is done using **bounds**.

### 📌 Syntax

- **Upper bound:** `<T extends SomeClass>` – T must be a subclass of SomeClass (or implement an interface).
- **Multiple bounds:** `<T extends ClassA & InterfaceB & InterfaceC>` – class first, then interfaces.

### ✅ Example: Upper Bounded Parameter

```java
// T must be a subclass of Number (or Number itself)
class NumericBox<T extends Number> {
    private T number;

    public NumericBox(T number) {
        this.number = number;
    }

    public double doubleValue() {
        return number.doubleValue(); // Number methods available
    }
}

public class BoundedTypeDemo {
    public static void main(String[] args) {
        NumericBox<Integer> intBox = new NumericBox<>(10);
        NumericBox<Double> doubleBox = new NumericBox<>(5.5);
        
        System.out.println("Int as double: " + intBox.doubleValue());
        System.out.println("Double as double: " + doubleBox.doubleValue());

        // NumericBox<String> stringBox = new NumericBox<>("Hello"); // ❌ Compile error
    }
}
```

### ✅ Example: Multiple Bounds

```java
interface Readable {
    void read();
}

interface Writable {
    void write();
}

class Document implements Readable, Writable {
    public void read() { System.out.println("Reading..."); }
    public void write() { System.out.println("Writing..."); }
}

class FileHandler<T extends Readable & Writable> {
    private T item;

    public FileHandler(T item) {
        this.item = item;
    }

    public void process() {
        item.read();
        item.write();
    }
}

public class MultipleBoundsDemo {
    public static void main(String[] args) {
        Document doc = new Document();
        FileHandler<Document> handler = new FileHandler<>(doc);
        handler.process();
    }
}
```

---

## 5️⃣ Wildcards (`?`)

Wildcards represent **unknown types**. They are used in method signatures to increase flexibility.

### Types of Wildcards

| Wildcard | Syntax | Purpose |
|----------|--------|---------|
| **Unbounded** | `<?>` | Any type |
| **Upper bounded** | `<? extends T>` | Any subtype of T |
| **Lower bounded** | `<? super T>` | Any supertype of T |

### 5.1. Unbounded Wildcard `<?>`

Use when the code works with any type, and the type is irrelevant (e.g., printing a list).

```java
public static void printList(List<?> list) {
    for (Object elem : list) {
        System.out.print(elem + " ");
    }
    System.out.println();
}

// Cannot add elements (except null) because type is unknown.
// list.add("hello"); // ❌ Compile error
```

### 5.2. Upper Bounded Wildcard `<? extends T>`

Used when you want to **read** items from a structure, but not write (except null). The actual type is some subtype of T.

```java
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number num : list) {
        sum += num.doubleValue();
    }
    return sum;
}

List<Integer> ints = Arrays.asList(1, 2, 3);
System.out.println(sumOfList(ints)); // Works

List<Double> doubles = Arrays.asList(1.5, 2.5, 3.5);
System.out.println(sumOfList(doubles)); // Works
```

### 5.3. Lower Bounded Wildcard `<? super T>`

Used when you want to **write** items into a structure. You can add elements of type T (or its subtypes) but reading gives `Object`.

```java
public static void addNumbers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
    // list.add(3.5); // ❌ Not allowed
}

List<Number> numList = new ArrayList<>();
addNumbers(numList);
System.out.println(numList); // [10, 20]

List<Object> objList = new ArrayList<>();
addNumbers(objList); // Also works because Object is superclass of Integer
```

### 5.4. Wildcard Guidelines (PECS)

**PECS** stands for **Producer `extends`, Consumer `super`** – a mnemonic to remember when to use which wildcard.

- If you **produce** values (read from a structure), use `<? extends T>`.
- If you **consume** values (write to a structure), use `<? super T>`.
- If you both read and write, don't use wildcard; use a concrete type.

```java
public void copy(List<? extends T> source, List<? super T> dest) {
    for (T item : source) {
        dest.add(item);
    }
}
```

---

## 6️⃣ Type Erasure

**Type erasure** is the process by which the compiler removes all generic type information and inserts necessary casts. This ensures compatibility with pre‑generic code (raw types).

### What Happens During Erasure?

- Replace type parameters with their leftmost bound (or `Object` if unbounded).
- Insert casts where necessary.
- Generate bridge methods to preserve polymorphism.

### ✅ Example: Before and After Erasure

```java
// Generic class
public class Box<T> {
    private T content;
    public T getContent() { return content; }
    public void setContent(T content) { this.content = content; }
}

// After erasure (conceptually)
public class Box {
    private Object content;  // T replaced with Object
    public Object getContent() { return content; }
    public void setContent(Object content) { this.content = content; }
}
```

For bounded type parameters:

```java
public class NumericBox<T extends Number> {
    private T number;
    public T getNumber() { return number; }
}

// After erasure
public class NumericBox {
    private Number number;  // T replaced with Number
    public Number getNumber() { return number; }
}
```

### 🔑 Implications of Type Erasure
- You cannot use `instanceof` with parameterized types (e.g., `list instanceof List<String>` is illegal).
- You cannot create arrays of parameterized types (e.g., `new List<String>[10]` is illegal).
- Overloading methods with same erasure is not allowed.
- Runtime type information is limited; reflection shows raw types.

---

## 7️⃣ Generic Interfaces

Interfaces can also be generic, similar to classes.

### ✅ Example: Generic Interface

```java
interface Pair<K, V> {
    K getKey();
    V getValue();
}

class OrderedPair<K, V> implements Pair<K, V> {
    private K key;
    private V value;

    public OrderedPair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public K getKey() { return key; }
    public V getValue() { return value; }
}

public class GenericInterfaceDemo {
    public static void main(String[] args) {
        Pair<String, Integer> p1 = new OrderedPair<>("Age", 30);
        Pair<String, String> p2 = new OrderedPair<>("Name", "Alice");

        System.out.println(p1.getKey() + " : " + p1.getValue());
        System.out.println(p2.getKey() + " : " + p2.getValue());
    }
}
```

### 📌 Raw Type vs Generic Implementation
- You can implement a generic interface with a specific type: `class StringPair implements Pair<String, Integer>`
- Or keep it generic: `class MyPair<T, U> implements Pair<T, U>`

---

## 8️⃣ Restrictions and Limitations

Due to type erasure, there are several restrictions when using generics.

| Restriction | Explanation | Example |
|-------------|-------------|---------|
| **Cannot instantiate with primitive types** | Use wrapper classes instead. | `List<int>` ❌ → `List<Integer>` ✅ |
| **Cannot create instances of type parameters** | `new T()` is illegal. | Need to use reflection or factory pattern. |
| **Cannot declare static fields of type parameters** | Static fields are shared, but type parameter is per instance. | `private static T item;` ❌ |
| **Cannot use `instanceof` with parameterized types** | Erasure removes type info. | `if (list instanceof List<String>)` ❌ |
| **Cannot create arrays of parameterized types** | Type safety would be compromised. | `new List<String>[10]` ❌ |
| **Cannot overload methods where erasure makes signatures identical** | | `void print(List<String>)` and `void print(List<Integer>)` conflict after erasure. |
| **Cannot have exception classes that are generic** | `class MyException<T> extends Exception` ❌ | JVM cannot handle generic exceptions. |

---

## 9️⃣ Best Practices

### ✅ Do's
- **Use diamond operator** (`<>`) for brevity: `List<String> list = new ArrayList<>();`
- **Prefer generic methods** when the type parameter is used only in the method, not the class.
- **Use bounded wildcards** to increase API flexibility (PECS).
- **Document type parameters** with meaningful names (e.g., `K` for key, `V` for value, `T` for type, `E` for element).
- **Use `@SuppressWarnings("unchecked")` sparingly** and only when you're certain of type safety.

### ❌ Don'ts
- **Don't use raw types** (e.g., `List` instead of `List<String>`). They exist only for legacy compatibility.
- **Don't create arrays of generic types** – use collections like `ArrayList<List<String>>`.
- **Don't rely on runtime type information** for generics; use reflection carefully.
- **Avoid mixing generic and non-generic code** without proper casting.

---

## 🔟 Summary Table

| Concept | Syntax | Use Case |
|---------|--------|----------|
| **Generic Class** | `class Name<T> { }` | Reusable class for any type |
| **Generic Method** | `<T> void method(T t)` | Type-safe method independent of class |
| **Bounded Type** | `<T extends Number>` | Restrict to specific hierarchy |
| **Unbounded Wildcard** | `<?>` | Read‑only, type unknown |
| **Upper Bounded Wildcard** | `<? extends T>` | Read (producer) |
| **Lower Bounded Wildcard** | `<? super T>` | Write (consumer) |
| **Type Erasure** | – | Compiler removes generic info |

Generics are a cornerstone of modern Java programming, enabling type-safe collections, reusable algorithms, and cleaner code. Mastering them is essential for writing robust and maintainable applications.
