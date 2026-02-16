# 📚 Complete Guide to Java Exception Handling

## 🎯 What is Exception Handling?

Exception Handling is a mechanism to handle **runtime errors** gracefully, ensuring the normal flow of the application is maintained even when unexpected events occur.

---

## 1️⃣ Types of Errors in Java

Java programs can encounter three main types of errors:

### 1.1. Syntax Errors (Compile-time Errors)

**Definition**: Occur when code violates Java's grammatical rules.

**Detection**: Detected during compilation. Modern IDEs highlight these in real-time.

**Example:**
```java
public class SyntaxErrorDemo {
    public static void main(String[] args) {
        // Missing semicolon - Syntax Error!
        System.out.println("Hello World")  // ❌ Compilation error
        
        // Missing closing brace
        if (true) {
            System.out.println("True");
        // Missing } - Syntax Error!
    }
}
```

**💡 Responsibility**: These are **programmer errors** that must be fixed before code can run.

---

### 1.2. Logical Errors (Bugs)

**Definition**: Program compiles and runs but produces **incorrect results** due to flawed logic.

**Detection**: Not detected by compiler or JVM. Found through **testing and debugging**.

**Example:**
```java
public class LogicalErrorDemo {
    // Method should calculate average but has logical error
    public static double calculateAverage(int sum, int count) {
        // ❌ Logical error: multiplication instead of division
        return sum * count;  // Should be sum / count
    }
    
    public static void main(String[] args) {
        int sum = 100;
        int count = 5;
        
        double avg = calculateAverage(sum, count);
        System.out.println("Average: " + avg);  // Output: 500.0 (WRONG!)
        // Should be 20.0 but got 500.0
    }
}
```

**💡 Responsibility**: Programmer errors - requires careful testing and debugging.

---

### 1.3. Runtime Errors (Exceptions)

**Definition**: Errors that occur during program execution after successful compilation.

**Impact**: If unhandled, causes program to **crash** (terminate abruptly).

**Example:**
```java
public class RuntimeErrorDemo {
    public static void main(String[] args) {
        System.out.println("Program started");
        
        // ❌ Runtime error: Division by zero
        int result = 10 / 0;  // ArithmeticException
        
        // This line never executes if exception occurs
        System.out.println("Program ended");  
    }
}
```

**Output:**
```
Program started
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at RuntimeErrorDemo.main(RuntimeErrorDemo.java:5)
```

**💡 Responsibility**: Can be programmer error or external factors (file not found, network issues).

---

## 2️⃣ What is an Exception?

An **exception** is an **event** that disrupts the normal flow of the program. It's an **object** that is **thrown** at runtime by the JVM when an unexpected event occurs.

```java
// Visual representation of exception flow:
// 
// Normal Flow:    Method1 → Method2 → Method3 → Result
//                      ↓
// Exception Flow: Method1 → Method2 → Method3 → ❗ EXCEPTION → 
//                  ↓
//                 Catch Block → Recovery → Continue
```

---

## 3️⃣ Handling Exceptions with try-catch

### 🔹 The try-catch Block

```java
public class TryCatchBasics {
    public static void main(String[] args) {
        System.out.println("Program starts");
        
        try {
            // Code that might throw an exception
            System.out.println("Inside try block - before exception");
            int result = 10 / 0;  // This throws ArithmeticException
            System.out.println("Inside try block - after exception"); // NEVER EXECUTES
        } 
        catch (ArithmeticException e) {
            // Code that executes when ArithmeticException occurs
            System.out.println("Inside catch block");
            System.out.println("Exception message: " + e.getMessage());
            System.out.println("Exception toString: " + e);
        }
        
        System.out.println("Program continues after handling");
        System.out.println("Program ends");
    }
}
```

**Output:**
```
Program starts
Inside try block - before exception
Inside catch block
Exception message: / by zero
Exception toString: java.lang.ArithmeticException: / by zero
Program continues after handling
Program ends
```

---

### 🔹 Multiple catch Blocks

```java
public class MultipleCatchDemo {
    public static void main(String[] args) {
        String str = null;
        int[] numbers = {1, 2, 3};
        
        try {
            // Try different operations that may throw exceptions
            System.out.println("Length: " + str.length());  // NullPointerException
            System.out.println(numbers[5]);                  // ArrayIndexOutOfBoundsException
            int result = 10 / 0;                              // ArithmeticException
        } 
        // ⚠️ ORDER MATTERS: Specific exceptions first, general last
        catch (NullPointerException e) {
            System.out.println("Caught NullPointerException");
            System.out.println("  Message: " + e.getMessage());
        }
        catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Caught ArrayIndexOutOfBoundsException");
            System.out.println("  Message: " + e.getMessage());
        }
        catch (ArithmeticException e) {
            System.out.println("Caught ArithmeticException");
            System.out.println("  Message: " + e.getMessage());
        }
        catch (Exception e) {  // Generic catch-all for any other Exception
            System.out.println("Caught general Exception");
            System.out.println("  Type: " + e.getClass().getSimpleName());
        }
        
        System.out.println("Program continues...");
    }
}
```

---

### 🔹 Multi-catch (Java 7+)

```java
public class MultiCatchDemo {
    public static void main(String[] args) {
        try {
            // Code that might throw different exceptions
            int[] arr = new int[5];
            // Uncomment different lines to test
            // arr[10] = 5;           // ArrayIndexOutOfBoundsException
            int result = 10 / 0;       // ArithmeticException
            // String str = null;
            // str.length();           // NullPointerException
            
        } 
        // Handle multiple exception types with same logic
        catch (ArithmeticException | ArrayIndexOutOfBoundsException e) {
            System.out.println("Caught either Arithmetic or ArrayIndex exception");
            System.out.println("Exception type: " + e.getClass().getSimpleName());
            System.out.println("Message: " + e.getMessage());
        }
        catch (NullPointerException e) {
            System.out.println("Caught NullPointer separately");
        }
        
        System.out.println("Program continues...");
    }
}
```

**⚠️ Important**: Exception types in multi-catch must be **disjoint** (no parent-child relationship).

---

### 🔹 Nested try-catch Blocks

```java
public class NestedTryCatchDemo {
    public static void main(String[] args) {
        try {
            System.out.println("Outer try block - Level 1");
            
            try {
                System.out.println("  Inner try block - Level 2");
                int[] arr = new int[5];
                arr[10] = 100;  // ArrayIndexOutOfBoundsException
                
                try {
                    System.out.println("    Inner-most try - Level 3");
                    int result = 10 / 0;  // ArithmeticException
                } 
                catch (ArithmeticException e) {
                    System.out.println("    Caught at Level 3: " + e.getMessage());
                }
            } 
            catch (ArrayIndexOutOfBoundsException e) {
                System.out.println("  Caught at Level 2: " + e.getMessage());
            }
        } 
        catch (Exception e) {
            System.out.println("Outer catch: " + e.getMessage());
        }
        
        System.out.println("Program continues after nested blocks");
    }
}
```

---

## 4️⃣ Exception Hierarchy

```
                    Object
                       |
                    Throwable
                       |
        ┌──────────────┴──────────────┐
        │                             │
      Error                        Exception
        │                             │
  (System Problems)           ┌────────┴────────┐
                              │                 │
                        RuntimeException    Checked Exceptions
                              │                 (IOException, etc.)
                    ┌─────────┼─────────┐
                    │         │         │
              NullPointer  Arithmetic  ArrayIndexOutOfBounds
              Exception    Exception   Exception
```

```java
public class ExceptionHierarchyDemo {
    public static void main(String[] args) {
        try {
            // This could throw any exception
            dangerousOperation();
        }
        // Can catch using any level of the hierarchy
        catch (ArithmeticException e) {
            System.out.println("Caught specific: ArithmeticException");
        }
        catch (RuntimeException e) {
            System.out.println("Caught general: RuntimeException");
        }
        catch (Exception e) {
            System.out.println("Caught more general: Exception");
        }
        catch (Throwable e) {
            System.out.println("Caught most general: Throwable");
        }
    }
    
    public static void dangerousOperation() {
        // Could throw various exceptions
        throw new ArithmeticException("Division by zero");
    }
}
```

**💡 Polymorphism in Catch Blocks**: Due to hierarchy, a catch block with superclass type can catch subclass exceptions.

---

## 5️⃣ Checked vs Unchecked Exceptions

### 5.1. Unchecked Exceptions (RuntimeExceptions)

**Definition**: Not checked at compile-time. Compiler doesn't force handling.

**Characteristics:**
- Subclasses of `RuntimeException`
- Usually indicate programming bugs
- Handling is optional

```java
public class UncheckedExceptionDemo {
    public static void main(String[] args) {
        String str = null;
        
        // No compile-time error - compiler doesn't force handling
        System.out.println(str.length());  // Will throw NullPointerException at runtime
        
        // But we CAN handle it if we want
        try {
            System.out.println(str.length());
        } catch (NullPointerException e) {
            System.out.println("Handled NullPointer: " + e.getMessage());
        }
        
        // More unchecked exceptions
        int[] arr = new int[5];
        // arr[10] = 5;     // ArrayIndexOutOfBoundsException
        
        // int result = 10 / 0;  // ArithmeticException
    }
}
```

**Common Unchecked Exceptions:**
- `NullPointerException`
- `ArrayIndexOutOfBoundsException`
- `ArithmeticException`
- `IllegalArgumentException`
- `NumberFormatException`

---

### 5.2. Checked Exceptions

**Definition**: Checked at compile-time. Compiler **forces** handling (try-catch or throws).

**Characteristics:**
- Subclasses of `Exception` (but not `RuntimeException`)
- Represent external, recoverable conditions
- Must be handled or declared

```java
import java.io.*;

public class CheckedExceptionDemo {
    public static void main(String[] args) {
        // ❌ This line won't compile - FileNotFoundException is checked!
        // FileReader reader = new FileReader("file.txt");
        
        // ✅ Must handle checked exception
        try {
            FileReader reader = new FileReader("file.txt");
            BufferedReader br = new BufferedReader(reader);
            String line = br.readLine();
            System.out.println(line);
            br.close();
        } 
        catch (FileNotFoundException e) {
            System.out.println("File not found: " + e.getMessage());
        } 
        catch (IOException e) {
            System.out.println("IO Error: " + e.getMessage());
        }
    }
    
    // Method declares it might throw checked exception
    public static void readFile() throws IOException {
        FileReader reader = new FileReader("file.txt");
        // Caller must handle or declare further
    }
}
```

**Common Checked Exceptions:**
- `IOException`
- `FileNotFoundException`
- `SQLException`
- `ClassNotFoundException`
- `InterruptedException`

---

## 6️⃣ The `throw` Keyword

Used to **explicitly throw** an exception object.

```java
public class ThrowKeywordDemo {
    
    // Method that manually throws unchecked exception
    public static void validateAge(int age) {
        if (age < 0) {
            // Throw unchecked exception - no need to declare
            throw new IllegalArgumentException("Age cannot be negative: " + age);
        }
        if (age < 18) {
            throw new RuntimeException("Minor not allowed");
        }
        System.out.println("Age " + age + " is valid");
    }
    
    // Method that throws checked exception - must declare 'throws'
    public static void checkFile(String filename) throws FileNotFoundException {
        if (filename == null || filename.isEmpty()) {
            // Throw checked exception - must be declared
            throw new FileNotFoundException("Filename cannot be empty");
        }
        System.out.println("Checking file: " + filename);
    }
    
    public static void main(String[] args) {
        // Handling unchecked exception
        try {
            validateAge(-5);
        } catch (IllegalArgumentException e) {
            System.out.println("Caught: " + e.getMessage());
        }
        
        // Handling checked exception
        try {
            checkFile("");
        } catch (FileNotFoundException e) {
            System.out.println("Caught: " + e.getMessage());
        }
    }
}
```

---

## 7️⃣ The `throws` Keyword

Used in method signature to **declare** that the method might throw exceptions.

```java
import java.io.*;

public class ThrowsKeywordDemo {
    
    // Method declares it throws checked exception
    public static void method1() throws IOException {
        System.out.println("Method1 might throw IOException");
        throw new IOException("IO Error in method1");
    }
    
    // Method declares multiple exceptions
    public static void method2() throws IOException, ClassNotFoundException {
        System.out.println("Method2 might throw multiple exceptions");
        if (Math.random() > 0.5) {
            throw new IOException("IO Error");
        } else {
            throw new ClassNotFoundException("Class not found");
        }
    }
    
    // This method handles the exception
    public static void callerHandles() {
        try {
            method1();
        } catch (IOException e) {
            System.out.println("Handled in caller: " + e.getMessage());
        }
    }
    
    // This method declares the exception (passes responsibility up)
    public static void callerDeclares() throws IOException {
        method1();  // Not handled, so must declare
    }
    
    public static void main(String[] args) {
        callerHandles();  // OK - handled
        
        // Must handle callerDeclares() since it declares exception
        try {
            callerDeclares();
        } catch (IOException e) {
            System.out.println("Caught in main: " + e.getMessage());
        }
        
        // Handle method2 with multiple exceptions
        try {
            method2();
        } catch (IOException | ClassNotFoundException e) {
            System.out.println("Caught: " + e.getClass().getSimpleName());
        }
    }
}
```

---

## 8️⃣ The `finally` Block

Code that **always executes** regardless of whether an exception occurs.

```java
public class FinallyDemo {
    
    public static void main(String[] args) {
        System.out.println("=== Scenario 1: No Exception ===");
        divide(10, 2);
        
        System.out.println("\n=== Scenario 2: Exception Occurred ===");
        divide(10, 0);
        
        System.out.println("\n=== Scenario 3: Exception with Return ===");
        System.out.println("Result: " + divideWithReturn(10, 0));
    }
    
    public static void divide(int a, int b) {
        System.out.println("  Entering divide method");
        
        try {
            System.out.println("    Inside try block");
            int result = a / b;
            System.out.println("    Result: " + result);
        } 
        catch (ArithmeticException e) {
            System.out.println("    Inside catch block: " + e.getMessage());
        } 
        finally {
            System.out.println("    ✅ Inside finally block - ALWAYS executes");
            System.out.println("    Cleanup operations go here (close files, connections, etc.)");
        }
        
        System.out.println("  Exiting divide method");
    }
    
    public static int divideWithReturn(int a, int b) {
        try {
            System.out.println("    Inside try with return");
            int result = a / b;
            return result;
        } 
        catch (ArithmeticException e) {
            System.out.println("    Inside catch with return");
            return -1;  // Return from catch
        } 
        finally {
            System.out.println("    ✅ Finally executes BEFORE return!");
            // Can't return from finally (would override other returns)
        }
    }
}
```

**Output:**
```
=== Scenario 1: No Exception ===
  Entering divide method
    Inside try block
    Result: 5
    ✅ Inside finally block - ALWAYS executes
    Cleanup operations go here (close files, connections, etc.)
  Exiting divide method

=== Scenario 2: Exception Occurred ===
  Entering divide method
    Inside try block
    Inside catch block: / by zero
    ✅ Inside finally block - ALWAYS executes
    Cleanup operations go here (close files, connections, etc.)
  Exiting divide method

=== Scenario 3: Exception with Return ===
    Inside try with return
    Inside catch with return
    ✅ Finally executes BEFORE return!
Result: -1
```

**💡 When to use `finally`:**
- Closing files, database connections
- Releasing network resources
- Cleaning up temporary files
- Releasing locks

---

## 9️⃣ try-with-resources (Java 7+)

Automatically closes resources that implement `AutoCloseable`.

```java
import java.io.*;
import java.sql.*;

public class TryWithResourcesDemo {
    
    // Traditional way (pre-Java 7) - lots of boilerplate
    public static void readFileTraditional(String path) {
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new FileReader(path));
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } 
        catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        } 
        finally {
            // Must manually close resource
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    System.out.println("Error closing: " + e.getMessage());
                }
            }
        }
    }
    
    // Modern way - try-with-resources
    public static void readFileModern(String path) {
        // Resources declared in try() are auto-closed
        try (FileReader fr = new FileReader(path);
             BufferedReader reader = new BufferedReader(fr)) {
            
            System.out.println("Reading file: " + path);
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println("  " + line);
            }
        } 
        catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
        // No finally needed - resources auto-closed!
    }
    
    // Multiple resources
    public static void copyFile(String source, String dest) {
        try (FileInputStream in = new FileInputStream(source);
             FileOutputStream out = new FileOutputStream(dest)) {
            
            byte[] buffer = new byte[1024];
            int bytesRead;
            while ((bytesRead = in.read(buffer)) != -1) {
                out.write(buffer, 0, bytesRead);
            }
            System.out.println("File copied successfully");
        } 
        catch (IOException e) {
            System.out.println("Copy failed: " + e.getMessage());
        }
    }
    
    // Custom resource implementing AutoCloseable
    static class DatabaseConnection implements AutoCloseable {
        public void connect() {
            System.out.println("Connecting to database...");
        }
        
        public void query(String sql) {
            System.out.println("Executing: " + sql);
        }
        
        @Override
        public void close() {
            System.out.println("Closing database connection...");
        }
    }
    
    public static void useDatabase() {
        try (DatabaseConnection conn = new DatabaseConnection()) {
            conn.connect();
            conn.query("SELECT * FROM users");
        } // close() automatically called here
        System.out.println("Database operation completed");
    }
    
    public static void main(String[] args) {
        System.out.println("=== Traditional File Reading ===");
        readFileTraditional("test.txt");
        
        System.out.println("\n=== Modern File Reading ===");
        readFileModern("test.txt");
        
        System.out.println("\n=== Custom Resource ===");
        useDatabase();
    }
}
```

---

## 🔟 Custom Exceptions

Create application-specific exception classes.

```java
// 1. Custom Checked Exception (extends Exception)
class InsufficientFundsException extends Exception {
    private double amount;  // Custom field
    private double balance;
    
    public InsufficientFundsException(String message) {
        super(message);
    }
    
    public InsufficientFundsException(String message, double amount, double balance) {
        super(message);
        this.amount = amount;
        this.balance = balance;
    }
    
    public double getAmount() {
        return amount;
    }
    
    public double getBalance() {
        return balance;
    }
    
    public double getDeficit() {
        return amount - balance;
    }
    
    @Override
    public String toString() {
        return String.format("InsufficientFundsException: %s (Attempted: $%.2f, Balance: $%.2f, Deficit: $%.2f)",
                getMessage(), amount, balance, getDeficit());
    }
}

// 2. Custom Unchecked Exception (extends RuntimeException)
class InvalidAccountException extends RuntimeException {
    private String accountNumber;
    
    public InvalidAccountException(String accountNumber) {
        super("Invalid account: " + accountNumber);
        this.accountNumber = accountNumber;
    }
    
    public String getAccountNumber() {
        return accountNumber;
    }
}

// 3. BankAccount class using custom exceptions
class BankAccount {
    private String accountNumber;
    private String accountHolder;
    private double balance;
    private static final double MINIMUM_BALANCE = 100.0;
    
    public BankAccount(String accountNumber, String accountHolder, double initialBalance) 
            throws InvalidAccountException {
        
        if (accountNumber == null || accountNumber.trim().isEmpty()) {
            throw new InvalidAccountException(accountNumber);
        }
        
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = initialBalance;
        System.out.println("Account created for " + accountHolder);
    }
    
    public void withdraw(double amount) throws InsufficientFundsException {
        System.out.println("\nAttempting to withdraw $" + amount);
        
        if (amount <= 0) {
            throw new IllegalArgumentException("Withdrawal amount must be positive");
        }
        
        if (amount > balance) {
            throw new InsufficientFundsException(
                "Cannot withdraw more than balance", amount, balance);
        }
        
        if (balance - amount < MINIMUM_BALANCE) {
            throw new InsufficientFundsException(
                "Would fall below minimum balance", amount, balance);
        }
        
        balance -= amount;
        System.out.printf("Withdrawal successful. New balance: $%.2f%n", balance);
    }
    
    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Deposit amount must be positive");
        }
        balance += amount;
        System.out.printf("Deposited $%.2f. New balance: $%.2f%n", amount, balance);
    }
    
    public void displayInfo() {
        System.out.printf("Account: %s | Holder: %s | Balance: $%.2f%n",
                accountNumber, accountHolder, balance);
    }
}

// 4. Test the custom exceptions
public class CustomExceptionDemo {
    public static void main(String[] args) {
        try {
            // Create account
            BankAccount account = new BankAccount("ACC123", "John Doe", 500.0);
            account.displayInfo();
            
            // Test various operations
            account.deposit(200.0);
            
            try {
                account.withdraw(1000.0);  // Should throw InsufficientFundsException
            } catch (InsufficientFundsException e) {
                System.out.println("❌ " + e);
                System.out.println("   Deficit: $" + e.getDeficit());
            }
            
            account.withdraw(450.0);  // Should work
            
            try {
                account.withdraw(200.0);  // Should fail (below minimum)
            } catch (InsufficientFundsException e) {
                System.out.println("❌ " + e.getMessage());
            }
            
        } catch (InvalidAccountException e) {
            System.out.println("❌ Failed to create account: " + e.getMessage());
            System.out.println("Invalid account number: " + e.getAccountNumber());
        } catch (IllegalArgumentException e) {
            System.out.println("❌ Invalid argument: " + e.getMessage());
        }
        
        System.out.println("\n=== Testing invalid account creation ===");
        try {
            BankAccount badAccount = new BankAccount("", "Invalid User", 100.0);
        } catch (InvalidAccountException e) {
            System.out.println("❌ " + e.getMessage());
            System.out.println("   Account number: " + e.getAccountNumber());
        }
    }
}
```

**Output:**
```
Account created for John Doe
Account: ACC123 | Holder: John Doe | Balance: $500.00
Deposited $200.00. New balance: $700.00

Attempting to withdraw $1000.0
❌ InsufficientFundsException: Cannot withdraw more than balance (Attempted: $1000.00, Balance: $700.00, Deficit: $300.00)
   Deficit: $300.0

Attempting to withdraw $450.0
Withdrawal successful. New balance: $250.00

Attempting to withdraw $200.0
❌ Would fall below minimum balance

=== Testing invalid account creation ===
❌ Invalid account: 
   Account number: 
```

---

## 1️⃣1️⃣ Stack Trace

The **stack trace** shows the method call sequence that led to an exception.

```java
public class StackTraceDemo {
    
    public static void method1() {
        System.out.println("  method1 starts");
        method2();
        System.out.println("  method1 ends");
    }
    
    public static void method2() {
        System.out.println("    method2 starts");
        method3();
        System.out.println("    method2 ends");
    }
    
    public static void method3() {
        System.out.println("      method3 starts");
        
        // Simulate an error
        int[] arr = new int[5];
        System.out.println(arr[10]);  // ArrayIndexOutOfBoundsException
        
        System.out.println("      method3 ends");  // Never reaches here
    }
    
    public static void main(String[] args) {
        System.out.println("main starts");
        
        try {
            method1();
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("\n❌ Exception caught!");
            System.out.println("Exception type: " + e.getClass().getName());
            System.out.println("Message: " + e.getMessage());
            
            // Print stack trace
            System.out.println("\n📋 Full Stack Trace:");
            e.printStackTrace();
            
            // Programmatic access to stack trace
            System.out.println("\n📋 Stack Trace Elements:");
            StackTraceElement[] elements = e.getStackTrace();
            for (int i = 0; i < elements.length; i++) {
                StackTraceElement el = elements[i];
                System.out.printf("  %d. %s.%s(%s:%d)%n",
                    i + 1,
                    el.getClassName(),
                    el.getMethodName(),
                    el.getFileName(),
                    el.getLineNumber());
            }
        }
        
        System.out.println("\nmain ends");
    }
}
```

**Output (simplified):**
```
main starts
  method1 starts
    method2 starts
      method3 starts

❌ Exception caught!
Exception type: java.lang.ArrayIndexOutOfBoundsException
Message: Index 10 out of bounds for length 5

📋 Full Stack Trace:
java.lang.ArrayIndexOutOfBoundsException: Index 10 out of bounds for length 5
    at StackTraceDemo.method3(StackTraceDemo.java:24)
    at StackTraceDemo.method2(StackTraceDemo.java:14)
    at StackTraceDemo.method1(StackTraceDemo.java:8)
    at StackTraceDemo.main(StackTraceDemo.java:32)

📋 Stack Trace Elements:
  1. StackTraceDemo.method3(StackTraceDemo.java:24)
  2. StackTraceDemo.method2(StackTraceDemo.java:14)
  3. StackTraceDemo.method1(StackTraceDemo.java:8)
  4. StackTraceDemo.main(StackTraceDemo.java:32)

main ends
```

---

## 📊 Summary Table

| Concept | Purpose | Example |
|---------|---------|---------|
| **try** | Contains code that might throw exception | `try { riskyCode(); }` |
| **catch** | Handles specific exception types | `catch (IOException e) { }` |
| **finally** | Always executes (cleanup) | `finally { closeResource(); }` |
| **throw** | Manually throw exception | `throw new Exception("Error");` |
| **throws** | Declare exceptions method might throw | `void method() throws IOException` |
| **Checked Exception** | Must be handled/declared | `IOException, SQLException` |
| **Unchecked Exception** | Optional handling | `NullPointerException, ArithmeticException` |
| **try-with-resources** | Auto-close resources | `try (FileReader fr = new FileReader())` |
| **Custom Exception** | Application-specific | `class MyException extends Exception` |

---

## 🎯 Best Practices

### ✅ DO's:
1. **Be specific** - Catch specific exceptions, not just `Exception`
2. **Log exceptions** - Always log exceptions for debugging
3. **Provide meaningful messages** - Include context in exception messages
4. **Close resources** - Use try-with-resources or finally blocks
5. **Throw early, catch late** - Validate early, handle at appropriate level

### ❌ DON'Ts:
1. **Don't ignore exceptions** - Empty catch blocks hide problems
2. **Don't catch `Throwable`** - Catches errors too (like `OutOfMemoryError`)
3. **Don't use exceptions for flow control** - Expensive and makes code hard to read
4. **Don't lose original exception** - When rethrowing, include original as cause

```java
// ❌ BAD: Empty catch block
try {
    riskyOperation();
} catch (Exception e) {
    // Do nothing - BAD!
}

// ✅ GOOD: Log and handle appropriately
try {
    riskyOperation();
} catch (SpecificException e) {
    logger.error("Operation failed", e);
    // Handle or rethrow with context
    throw new BusinessException("Failed to process", e);
}
```

---

## 🎯 Key Takeaways

1. **Exceptions disrupt normal flow** - Handle them to prevent crashes
2. **Checked vs Unchecked** - Compiler enforces checked exception handling
3. **try-catch-finally** - Basic structure for exception handling
4. **throw vs throws** - `throw` throws exception, `throws` declares it
5. **finally always executes** - Perfect for cleanup
6. **try-with-resources** - Auto-closes resources (Java 7+)
7. **Custom exceptions** - Make your code more expressive
8. **Stack trace** - Your best debugging friend

**Remember:** Good exception handling separates robust applications from fragile ones!
