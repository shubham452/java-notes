# 🔹 Complete Guide to `static` in Java

## 📚 Introduction to `static` in Java

The `static` keyword in Java is a **non-access modifier** used for memory management. When a member is declared `static`, it belongs to the **class itself** rather than to instances (objects) of the class.

### 🌟 Key Concept
- **Without `static`**: Each object has its own copy (instance members)
- **With `static`**: One copy shared by all objects (class members)

```java
// Visual representation in memory:
// 
// HEAP Memory
// ┌─────────────────────────────────┐
// │  Object 1                       │
// │  - name = "Alice"  (instance)   │
// │  - age = 20        (instance)   │
// └─────────────────────────────────┘
// ┌─────────────────────────────────┐
// │  Object 2                       │
// │  - name = "Bob"    (instance)   │
// │  - age = 22        (instance)   │
// └─────────────────────────────────┘
//
// Method Area (Class Area)
// ┌─────────────────────────────────┐
// │  Student Class                   │
// │  - count = 2        (static)    │ ← ONE COPY SHARED
// └─────────────────────────────────┘
```

### ✅ Where Can `static` Be Used?
| Applicable To | Example | Common Use |
|--------------|---------|------------|
| **Static Variables** | `static int count;` | Shared counters, constants |
| **Static Methods** | `static void main(){}` | Utility methods |
| **Static Blocks** | `static { }` | Complex initialization |
| **Static Nested Classes** | `static class Nested { }` | Group related classes |

---

## 🔹 1. Static Variables (Fields)

**Definition**: Variables declared with the `static` keyword that belong to the class rather than any object.

### 🎯 Characteristics:
- **One copy per class** (shared across all instances)
- **Memory efficient** - saves memory by sharing
- **Initialized once** when class is loaded
- **Accessed via class name**: `ClassName.variableName`

### ✅ Example: Counting Objects Created

```java
class Student {
    // Instance variables - each object has its own copy
    String name;
    int age;
    
    // Static variable - ONE copy shared by ALL objects
    static int totalStudents = 0;
    static String schoolName = "Greenwood High";
    
    // Constructor
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
        
        // Increment static counter when new object created
        totalStudents++;  // Can also write: Student.totalStudents++
        
        System.out.println("Created: " + name + " | Total students: " + totalStudents);
    }
    
    // Method to display student info
    public void displayInfo() {
        System.out.println("Name: " + name + ", Age: " + age + 
                          ", School: " + schoolName);  // static accessed here
    }
}

public class TestStaticVariables {
    public static void main(String[] args) {
        System.out.println("Initial total: " + Student.totalStudents);  // 0
        
        Student s1 = new Student("Alice", 20);
        Student s2 = new Student("Bob", 22);
        Student s3 = new Student("Charlie", 21);
        
        System.out.println("\n--- Student Info ---");
        s1.displayInfo();
        s2.displayInfo();
        s3.displayInfo();
        
        System.out.println("\nFinal total: " + Student.totalStudents);  // 3
        
        // All objects share the same static variable
        System.out.println("\nAccess via objects (not recommended):");
        System.out.println("s1.totalStudents: " + s1.totalStudents);  // 3
        System.out.println("s2.totalStudents: " + s2.totalStudents);  // 3
        System.out.println("s3.totalStudents: " + s3.totalStudents);  // 3
        
        // Changing static variable affects all
        Student.schoolName = "Oxford High";
        System.out.println("\nAfter school name change:");
        s1.displayInfo();  // Now shows Oxford High
        s2.displayInfo();  // Now shows Oxford High
    }
}
```

**✅ Output:**
```
Initial total: 0
Created: Alice | Total students: 1
Created: Bob | Total students: 2
Created: Charlie | Total students: 3

--- Student Info ---
Name: Alice, Age: 20, School: Greenwood High
Name: Bob, Age: 22, School: Greenwood High
Name: Charlie, Age: 21, School: Greenwood High

Final total: 3

Access via objects (not recommended):
s1.totalStudents: 3
s2.totalStudents: 3
s3.totalStudents: 3

After school name change:
Name: Alice, Age: 20, School: Oxford High
Name: Bob, Age: 22, School: Oxford High
Name: Charlie, Age: 21, School: Oxford High
```

### 💡 Best Practice: Use Class Name, Not Object Reference
```java
// ✅ GOOD
Student.totalStudents++;

// ❌ BAD (works but misleading)
s1.totalStudents++;
```

---

## 🔹 2. Static Methods

**Definition**: Methods declared with `static` that belong to the class and can be called without creating an object.

### 🎯 Characteristics:
- **Called using class name**: `ClassName.methodName()`
- **Cannot access instance members directly** (no `this` reference)
- **Cannot use `this` or `super` keywords**
- **Commonly used for utility/helper methods**

### ✅ Example: Math Utility Class

```java
class MathUtils {
    // Static methods - no object needed
    public static int add(int a, int b) {
        return a + b;
    }
    
    public static int subtract(int a, int b) {
        return a - b;
    }
    
    public static int multiply(int a, int b) {
        return a * b;
    }
    
    public static double divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Cannot divide by zero");
        }
        return (double) a / b;
    }
    
    public static int factorial(int n) {
        if (n < 0) throw new IllegalArgumentException("Negative number");
        if (n == 0 || n == 1) return 1;
        
        int result = 1;
        for (int i = 2; i <= n; i++) {
            result *= i;
        }
        return result;
    }
}

public class TestStaticMethods {
    public static void main(String[] args) {
        // Call static methods directly using class name
        System.out.println("10 + 5 = " + MathUtils.add(10, 5));
        System.out.println("10 - 5 = " + MathUtils.subtract(10, 5));
        System.out.println("10 × 5 = " + MathUtils.multiply(10, 5));
        System.out.println("10 ÷ 5 = " + MathUtils.divide(10, 5));
        System.out.println("Factorial of 5 = " + MathUtils.factorial(5));
        
        // No need to create MathUtils object!
        // MathUtils utils = new MathUtils(); // Waste of memory
    }
}
```

**✅ Output:**
```
10 + 5 = 15
10 - 5 = 5
10 × 5 = 50
10 ÷ 5 = 2.0
Factorial of 5 = 120
```

### ⚠️ Important Restriction: Static Can't Access Instance Members

```java
class Demonstration {
    int instanceVar = 10;           // Instance variable
    static int staticVar = 20;       // Static variable
    
    // Static method
    static void staticMethod() {
        // ✅ Can access static members
        System.out.println("Static var: " + staticVar);
        
        // ❌ Cannot access instance members directly
        // System.out.println(instanceVar);  // COMPILE ERROR!
        
        // ❌ Cannot use 'this' (no current object)
        // this.instanceVar = 30;  // COMPILE ERROR!
        
        // ✅ Can access instance members IF we create an object
        Demonstration demo = new Demonstration();
        System.out.println("Instance var via object: " + demo.instanceVar);
    }
    
    // Instance method
    void instanceMethod() {
        // ✅ Can access both static and instance members
        System.out.println("Static var: " + staticVar);
        System.out.println("Instance var: " + instanceVar);
        
        // ✅ Can use 'this'
        this.instanceVar = 30;
    }
}
```

### 📌 Common Static Methods in Java API

```java
// Math class - all static
Math.max(10, 20);
Math.min(10, 20);
Math.sqrt(25);
Math.random();

// Arrays class - all static
int[] arr = {3, 1, 4, 1, 5};
Arrays.sort(arr);
Arrays.toString(arr);
Arrays.copyOf(arr, 10);

// Collections class - all static
Collections.sort(list);
Collections.reverse(list);
Collections.max(list);

// Integer wrapper class - static methods
Integer.parseInt("123");
Integer.toString(456);
Integer.valueOf(789);
```

---

## 🔹 3. Static Blocks

**Definition**: A block of code marked with `static` that executes once when the class is first loaded into memory.

### 🎯 Characteristics:
- **Executes once** when class is loaded (before any objects are created)
- **Runs before the `main` method**
- **Can have multiple static blocks** (execute in order)
- **Used for complex static initialization**

### ✅ Example: Multiple Static Blocks

```java
class DatabaseConfig {
    static String url;
    static String username;
    static String password;
    static int connectionTimeout;
    
    // Static block 1 - Initialization
    static {
        System.out.println("Static block 1: Loading database configuration...");
        url = "jdbc:mysql://localhost:3306/mydb";
        username = "admin";
        System.out.println("URL and username set");
    }
    
    // Static block 2 - More complex initialization
    static {
        System.out.println("Static block 2: Setting security parameters...");
        password = "secret@123";
        connectionTimeout = 5000; // 5 seconds
    }
    
    // Static block 3 - Validation
    static {
        System.out.println("Static block 3: Validating configuration...");
        if (url == null || username == null || password == null) {
            throw new RuntimeException("Database configuration incomplete!");
        }
        System.out.println("Configuration validated successfully!");
    }
    
    // Constructor
    public DatabaseConfig() {
        System.out.println("Constructor: DatabaseConfig object created");
    }
    
    // Static method
    public static void displayConfig() {
        System.out.println("\n--- Database Configuration ---");
        System.out.println("URL: " + url);
        System.out.println("Username: " + username);
        System.out.println("Password: " + "******");
        System.out.println("Timeout: " + connectionTimeout + "ms");
    }
}

public class TestStaticBlocks {
    public static void main(String[] args) {
        System.out.println("Main method started");
        
        // Access static member - triggers class loading if not already loaded
        System.out.println("\nAccessing static configuration:");
        DatabaseConfig.displayConfig();
        
        System.out.println("\nCreating first object:");
        DatabaseConfig config1 = new DatabaseConfig();
        
        System.out.println("\nCreating second object:");
        DatabaseConfig config2 = new DatabaseConfig();
        
        // Note: Static blocks executed ONLY ONCE when class first loaded
    }
}
```

**✅ Output:**
```
Static block 1: Loading database configuration...
URL and username set
Static block 2: Setting security parameters...
Static block 3: Validating configuration...
Configuration validated successfully!
Main method started

Accessing static configuration:

--- Database Configuration ---
URL: jdbc:mysql://localhost:3306/mydb
Username: admin
Password: ******
Timeout: 5000ms

Creating first object:
Constructor: DatabaseConfig object created

Creating second object:
Constructor: DatabaseConfig object created
```

### 🔄 Execution Order Visualization

```
JVM Starts → Load Class → Execute Static Blocks (in order) → 
Execute Main Method → Create Objects (Constructors)
```

---

## 🔹 4. Common Use Cases of `static`

### ✅ a. Utility Classes

Classes that contain only static methods and are not meant to be instantiated.

```java
// File: StringUtils.java
public class StringUtils {
    // Private constructor prevents instantiation
    private StringUtils() {
        throw new AssertionError("Cannot instantiate utility class");
    }
    
    // Static utility methods
    public static boolean isEmpty(String str) {
        return str == null || str.trim().isEmpty();
    }
    
    public static String reverse(String str) {
        if (str == null) return null;
        return new StringBuilder(str).reverse().toString();
    }
    
    public static int countVowels(String str) {
        if (str == null) return 0;
        int count = 0;
        String vowels = "AEIOUaeiou";
        for (char c : str.toCharArray()) {
            if (vowels.indexOf(c) != -1) count++;
        }
        return count;
    }
    
    public static String capitalize(String str) {
        if (isEmpty(str)) return str;
        return str.substring(0, 1).toUpperCase() + 
               str.substring(1).toLowerCase();
    }
}

// Usage
public class TestUtils {
    public static void main(String[] args) {
        // StringUtils utils = new StringUtils();  // ❌ Can't instantiate
        
        System.out.println(StringUtils.isEmpty(""));           // true
        System.out.println(StringUtils.reverse("hello"));      // "olleh"
        System.out.println(StringUtils.countVowels("Java"));   // 2
        System.out.println(StringUtils.capitalize("java"));    // "Java"
    }
}
```

### ✅ b. Singleton Pattern (Detailed)

The Singleton pattern ensures a class has **only one instance** and provides a **global access point** to it.

```java
// File: config/AppConfig.java
package config;

public class AppConfig {
    // 1. Static variable to hold the single instance
    private static AppConfig instance;
    
    // 2. Configuration data (stored in the single instance)
    private String appName;
    private String version;
    private String environment;
    private int maxUsers;
    
    // 3. Private constructor - prevents external instantiation
    private AppConfig() {
        System.out.println("🔧 Creating AppConfig instance...");
        
        // Simulate loading configuration from file
        loadConfiguration();
    }
    
    // 4. Static method to get the single instance (lazy initialization)
    public static AppConfig getInstance() {
        if (instance == null) {
            instance = new AppConfig();  // Created only once
        }
        return instance;
    }
    
    // Private helper method
    private void loadConfiguration() {
        // In real app, this would read from a file
        this.appName = "MyApplication";
        this.version = "2.1.0";
        this.environment = "production";
        this.maxUsers = 1000;
        
        System.out.println("📋 Configuration loaded:");
        System.out.println("   - App: " + appName);
        System.out.println("   - Version: " + version);
        System.out.println("   - Environment: " + environment);
        System.out.println("   - Max Users: " + maxUsers);
    }
    
    // Getter methods
    public String getAppName() { return appName; }
    public String getVersion() { return version; }
    public String getEnvironment() { return environment; }
    public int getMaxUsers() { return maxUsers; }
    
    // Business methods
    public void displayConfig() {
        System.out.println("\n=== Current Configuration ===");
        System.out.println("App Name: " + appName);
        System.out.println("Version: " + version);
        System.out.println("Environment: " + environment);
        System.out.println("Max Users: " + maxUsers);
        System.out.println("Instance ID: " + this.hashCode());
    }
}

// File: test/TestSingleton.java
package test;

import config.AppConfig;

public class TestSingleton {
    public static void main(String[] args) {
        System.out.println("=== Singleton Pattern Demo ===\n");
        
        // Get instance first time - creates the object
        System.out.println("First call to getInstance():");
        AppConfig config1 = AppConfig.getInstance();
        config1.displayConfig();
        
        System.out.println("\nSecond call to getInstance():");
        AppConfig config2 = AppConfig.getInstance();
        config2.displayConfig();
        
        System.out.println("\nThird call to getInstance():");
        AppConfig config3 = AppConfig.getInstance();
        config3.displayConfig();
        
        // Verify all references point to the same object
        System.out.println("\n=== Object Identity Check ===");
        System.out.println("config1 hash: " + config1.hashCode());
        System.out.println("config2 hash: " + config2.hashCode());
        System.out.println("config3 hash: " + config3.hashCode());
        System.out.println("Same instance? " + (config1 == config2));  // true
        System.out.println("Same instance? " + (config2 == config3));  // true
        
        // Demonstrate that changes affect the single instance
        System.out.println("\n=== State Modification ===");
        // Note: In real Singleton, you'd have setters with proper validation
        // This is just to demonstrate it's the same object
    }
}
```

**✅ Output:**
```
=== Singleton Pattern Demo ===

First call to getInstance():
🔧 Creating AppConfig instance...
📋 Configuration loaded:
   - App: MyApplication
   - Version: 2.1.0
   - Environment: production
   - Max Users: 1000

=== Current Configuration ===
App Name: MyApplication
Version: 2.1.0
Environment: production
Max Users: 1000
Instance ID: 123456

Second call to getInstance():
// No "Creating" message - object reused!

=== Current Configuration ===
App Name: MyApplication
Version: 2.1.0
Environment: production
Max Users: 1000
Instance ID: 123456

Third call to getInstance():
// Still no creation!

=== Current Configuration ===
App Name: MyApplication
Version: 2.1.0
Environment: production
Max Users: 1000
Instance ID: 123456

=== Object Identity Check ===
config1 hash: 123456
config2 hash: 123456
config3 hash: 123456
Same instance? true
Same instance? true
```

**💡 Singleton Key Points:**
- **Private constructor** - No external instantiation
- **Static instance variable** - Holds the single instance
- **Static getInstance()** - Global access point
- **Lazy initialization** - Created only when first needed
- **Thread safety** - Basic version not thread-safe (advanced versions add synchronization)

---

## 📊 Summary Table

| Feature | Static Variable | Static Method | Static Block | Static Class |
|---------|-----------------|---------------|--------------|--------------|
| **When created** | Class loading | Class loading | Class loading | Class loading |
| **How many copies** | One per class | One per class | Executes once | One nested class |
| **Access** | ClassName.var | ClassName.method() | Automatic | ClassName.Nested |
| **Can access instance members?** | No | No | No | Yes (if object created) |
| **Can use `this`?** | No | No | No | Yes (in instance methods) |
| **Common use** | Constants, counters | Utilities | Complex init | Group related classes |

---

## ❓ Interview Questions with Detailed Answers

### 🔹 Beginner Level

**1. Q: What is the static keyword in Java?**
**A:** `static` is a non-access modifier used to create class-level members. Static members belong to the class itself, not to individual objects. They are shared across all instances and can be accessed without creating an object.

---

**2. Q: Can we access static members without creating an object?**
**A:** Yes, static members can be accessed directly using the class name: `ClassName.staticMember`. No object is required.

```java
Math.max(10, 20);  // No Math object created!
```

---

**3. Q: Why is the main method in Java static?**
**A:** The `main` method is static because the JVM needs to call it **before any objects exist**. If `main` weren't static, the JVM would need to create an object of the class first, leading to a chicken-and-egg problem.

```java
public static void main(String[] args) {
    // JVM calls this without creating any object
}
```

---

**4. Q: How many copies of a static variable are created?**
**A:** **Exactly one copy** per class, regardless of how many objects are created. All instances share this single copy.

---

**5. Q: Can static methods access instance variables?**
**A:** **No**. Static methods cannot directly access instance variables because they don't have a `this` reference. They can only access instance variables through an object reference.

```java
class Example {
    int x = 10;
    static void method() {
        // System.out.println(x);  // ❌ ERROR
        Example e = new Example();
        System.out.println(e.x);    // ✅ OK - through object
    }
}
```

---

### 🔹 Intermediate Level

**6. Q: What happens if we try to access a non-static field from a static method?**
**A:** Compile-time error: *"Cannot make a static reference to the non-static field"*. Static methods belong to the class and don't have access to instance-specific data.

---

**7. Q: Can we override static methods in Java?**
**A:** **No**, static methods are **not overridden** - they are **hidden**. This is called **method hiding**, not overriding.

```java
class Parent {
    static void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    static void show() {  // This HIDES Parent.show(), not overrides it
        System.out.println("Child");
    }
}

public class Test {
    public static void main(String[] args) {
        Parent p = new Child();
        p.show();  // Output: "Parent" (not "Child") - method resolution based on reference type!
        
        // For static methods, the method called depends on the reference type, not the object type
    }
}
```

**💡 Key Difference:**
- **Overriding** (instance methods): Based on **object type** at runtime
- **Hiding** (static methods): Based on **reference type** at compile time

---

**8. Q: Can a static block throw an exception?**
**A:** Static blocks can throw **unchecked exceptions** (RuntimeException and subclasses). They **cannot throw checked exceptions** unless caught, because there's no method signature to declare them.

```java
static {
    // ✅ This is fine - RuntimeException
    if (something) {
        throw new RuntimeException("Error");
    }
    
    // ❌ This would cause compile error
    // throw new IOException();  // Checked exception - not allowed!
    
    // ✅ This is fine - caught checked exception
    try {
        throw new IOException();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

---

**9. Q: Can we have multiple static blocks in a class?**
**A:** **Yes**, and they execute in the order they appear in the source code.

```java
class Demo {
    static { System.out.println("First"); }
    static { System.out.println("Second"); }
    static { System.out.println("Third"); }
}
// Output when class loads: First Second Third
```

---

**10. Q: What is method hiding in Java?**
**A:** Method hiding occurs when a subclass defines a static method with the same signature as a static method in the parent class. The subclass method **hides** the parent method rather than overriding it. The method called depends on the reference type, not the object type.

---

### 🔹 Advanced Level

**11. Q: Can a class be declared static?**
**A:** **Top-level classes cannot be static**, but **nested (inner) classes can be declared static**. A static nested class behaves like a top-level class but is nested for packaging convenience.

```java
public class Outer {
    private int x = 10;
    private static int y = 20;
    
    // Static nested class
    public static class StaticNested {
        public void display() {
            // System.out.println(x);  // ❌ Can't access non-static outer members
            System.out.println(y);      // ✅ Can access static outer members
        }
    }
    
    // Usage
    public static void main(String[] args) {
        Outer.StaticNested nested = new Outer.StaticNested();
        nested.display();
    }
}
```

---

**12. Q: What is the difference between static block and constructor?**

| Feature | Static Block | Constructor |
|---------|--------------|-------------|
| **Execution time** | When class is loaded | When object is created |
| **Frequency** | Once per class loading | Once per object creation |
| **Purpose** | Class-level initialization | Object-level initialization |
| **Can access instance members?** | No (no object yet) | Yes |
| **Can throw checked exceptions?** | No (unless caught) | Yes |
| **Order** | Before main and constructors | After static blocks |

---

**13. Q: Is it possible to make a static class in Java?**
**A:** Only **nested classes** can be static. Top-level classes cannot be declared static. A static nested class:
- Doesn't need an instance of the outer class to be instantiated
- Can only access static members of the outer class directly
- Behaves like a regular top-level class but is nested for organizational purposes

---

**14. Q: How can you implement a singleton class using static?**
**A:** The classic Singleton pattern uses:
1. `private static` instance variable
2. `private` constructor
3. `public static` method to get the instance

```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() { }
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

**💡 Note:** This basic version is **not thread-safe**. For thread safety, use `synchronized` or eager initialization.

---

**15. Q: What happens if you try to use `this` in a static method?**
**A:** **Compile-time error**. `this` refers to the current object, but static methods execute in a class context where no current object exists. The error message: *"Cannot use this in a static context"*

---

## 🎯 Key Takeaways

1. **`static` means "class-level"** - belongs to the class, not instances
2. **One copy shared** - all objects share the same static member
3. **Access via class name** - `ClassName.staticMember` (preferred)
4. **Cannot access instance members** - static methods/blocks have no `this`
5. **Static blocks execute once** - when class is first loaded
6. **Perfect for utilities** - Math, Arrays, Collections classes
7. **Singleton pattern** - classic use of static for single instance
8. **Memory efficient** - no duplicate copies for each object

**Remember:** Static members are **class citizens** - they exist even when no objects exist!
