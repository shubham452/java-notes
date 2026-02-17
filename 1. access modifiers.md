# ✅ Access Modifiers in Java

Java provides four access levels that control the visibility of classes, methods, variables, and constructors:

| Modifier | Same Class | Same Package | Subclass (Different Package) | Non-subclass (Different Package) |
|----------|------------|--------------|------------------------------|-----------------------------------|
| private | ✅ | ❌ | ❌ | ❌ |
| default | ✅ | ✅ | ❌ | ❌ |
| protected | ✅ | ✅ | ✅ | ❌ |
| public | ✅ | ✅ | ✅ | ✅ |

---

## 1️⃣ Default Access (Package-Private)

- No modifier specified (i.e., not `public`, `private`, or `protected`)
- Accessible only within the **same package**

```java
// File: school/Utils.java
package school;

class Utils {  // default access
    static void greet() {
        System.out.println("Hello from Utils");
    }
}

// File: test/Test.java
package test;

import school.Utils;  // ❌ Compilation error: Utils is not visible

public class Test {
    public static void main(String[] args) {
        // Utils.greet();  // ❌ Not accessible
    }
}
```

---

## 2️⃣ Public Access

- Accessible from **any class** in **any package**

```java
// File: school/Student.java
package school;

public class Student {
    public String name;
    public int age;

    public void sayHello() {
        System.out.println("Hi, I'm " + name);
    }
}

// File: test/Test.java
package test;

import school.Student;

public class Test {
    public static void main(String[] args) {
        Student s = new Student();
        s.name = "Ram";
        s.age = 12;
        s.sayHello();  // ✅ Accessible from different package
    }
}
```

---

## 3️⃣ Private Access

- Accessible **only within the same class**

```java
// File: school/Student.java
package school;

public class Student {
    private String name = "Ram";
    public int age = 10;

    private void secret() {
        System.out.println("Secret: " + name);
    }

    public void show() {
        secret();  // ✅ Allowed inside same class
    }
}

// File: test/Test.java
package test;

import school.Student;

public class Test {
    public static void main(String[] args) {
        Student s = new Student();
        // s.name = "Shyam";    // ❌ Private field
        // s.secret();          // ❌ Private method
        s.show();               // ✅ Public method accessing private members
    }
}
```

### 📌 Private Constructor Example (Utility Class)

```java
// File: school/Utils.java
package school;

public class Utils {
    private Utils() {
        // Prevent object creation
    }

    public static void sayHello() {
        System.out.println("Hello!");
    }
}

// File: test/Test.java
package test;

import school.Utils;

public class Test {
    public static void main(String[] args) {
        // Utils u = new Utils();  // ❌ Private constructor
        Utils.sayHello();         // ✅ Static method call
    }
}
```

### 🧩 Singleton Pattern using Private Constructor

```java
// File: school/School.java
package school;

public class School {
    private static School instance;

    private School() {}  // Private constructor

    public static School getInstance() {
        if (instance == null) {
            instance = new School();
        }
        return instance;
    }

    public void print() {
        System.out.println("Singleton School");
    }
}

// File: test/Test.java
package test;

import school.School;

public class Test {
    public static void main(String[] args) {
        School s1 = School.getInstance();
        School s2 = School.getInstance();
        System.out.println(s1 == s2);  // true (same instance)
        s1.print();
    }
}
```

---

## 4️⃣ Protected Access

- Accessible:
  - Within the **same package**
  - In **subclasses** (even in different packages)

### ✅ Same Package Access

```java
// File: zoo/Animal.java
package zoo;

public class Animal {
    protected String sound;

    public Animal(String sound) {
        this.sound = sound;
    }

    protected void speak() {
        System.out.println("Animal says: " + sound);
    }
}

// File: zoo/Dog.java
package zoo;

public class Dog extends Animal {
    public Dog() {
        super("Bark");
    }

    public void show() {
        speak();  // ✅ Access protected method (same package)
    }
}
```

### ❌ Different Package (Non-subclass)

```java
// File: test/Test.java
package test;

import zoo.Animal;

public class Test {
    public static void main(String[] args) {
        Animal a = new Animal("Roar");
        // a.speak();  // ❌ Error: speak() is protected
    }
}
```

### ✅ Different Package (Subclass)

```java
// File: test/Dog.java
package test;

import zoo.Animal;

public class Dog extends Animal {
    public Dog() {
        super("Woof");
    }

    public void display() {
        System.out.println("Sound: " + sound);  // ✅ Access protected field
        speak();                                // ✅ Access protected method
    }
}
```

---

## 🎯 Interview Questions on Access Modifiers

### ✅ Beginner Level

**1. What are access modifiers in Java? Name them.**

Access modifiers define the visibility of classes, methods, constructors, and variables. Java provides four access modifiers:
- `public`
- `protected`
- `default` (no modifier)
- `private`

---

**2. What is the default access modifier in Java?**

If no access modifier is specified, Java uses **default access** (also known as **package-private**). The member is accessible only within the **same package**.

---

**3. Can private members be inherited in subclasses?**

No, `private` members are **not inherited**. They are accessible only within the same class.

---

**4. Which access modifier allows access from everywhere?**

`public` allows access from anywhere in any package.

---

**5. Can we have a private class in Java?**

Only **inner (nested) classes** can be `private`. Top-level classes cannot be private—they must be `public` or use default access.

---

### 🔹 Intermediate Level

**6. What is the difference between protected and default access?**

| Feature | `protected` | `default` |
|---------|-------------|-----------|
| Same package | ✅ Accessible | ✅ Accessible |
| Subclass (different package) | ✅ Accessible | ❌ Not accessible |
| Non-subclass (different package) | ❌ Not accessible | ❌ Not accessible |

---

**7. Why use private constructors in Java?**

Private constructors prevent object creation from outside the class. Common use cases:
- **Singleton pattern** (only one instance of a class)
- **Utility classes** (e.g., `Math`, `Collections`)

---

**8. Can a top-level class be private?**

No. A top-level class in Java can only be:
- `public` (accessible everywhere)
- Default/package-private (accessible within the same package)

---

**9. Can protected members be accessed outside the package without inheritance?**

No. `protected` members can be accessed outside the package **only through inheritance** (i.e., by a subclass).

---

**10. Can we override a private method?**

No. Private methods are not visible to subclasses and thus **cannot be overridden**. However, you can declare a new method with the same name in the subclass—this is **method hiding**, not overriding.

---

### 🔸 Advanced Level

**11. Explain the use of protected members in different packages.**

`protected` members strike a balance between encapsulation and inheritance:
- Within the same package, they behave like default access
- In subclasses of other packages, they provide controlled access
- This supports class hierarchies that span across packages while maintaining some encapsulation

---

**12. What happens if you try to access a default class in another package?**

A class with default access is **not visible** outside its package. Attempting to import or use it from another package results in a **compile-time error**.

---

**13. Can a public class have a private constructor? Why?**

Yes. A `public` class with a `private` constructor:
- Prevents instantiation by external code
- Controls object creation (Singleton pattern)
- Useful for utility classes where only static methods are needed

---

**14. How would you implement a utility class in Java?**

```java
public class Utils {
    private Utils() {}  // Prevent instantiation

    public static void sayHello() {
        System.out.println("Hello!");
    }
}
```

Key characteristics:
- `public` class
- `private` constructor
- All methods are `static`

---

**15. Explain how the Singleton pattern uses private constructors and static methods.**

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}  // Private constructor prevents direct instantiation

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();  // Controlled instantiation
        }
        return instance;
    }
}
```

How it works:
- **Private constructor** prevents external instantiation
- **Static factory method** (`getInstance()`) controls instance creation
- **Static instance variable** holds the single instance
- Ensures **only one instance** exists throughout the application
- Provides a **global access point** to that instance
