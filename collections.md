# 📚 Complete Guide to Java Collections Framework & Streams API

## 1️⃣ Introduction to Java Collections Framework

### What is a Collection?
A **collection** is simply a group of elements - like a collection of coins, stamps, or in Java, a group of objects (strings, numbers, custom objects).

### What is the Collection Framework?
It's a **unified architecture** for storing and manipulating groups of objects, consisting of:
- **Interfaces** (blueprints)
- **Implementations** (concrete classes)
- **Algorithms** (utility methods)

### 📜 History: Before Collection Framework (Pre-Java 1.2)
Java relied on legacy classes like `Vector`, `Stack`, `Hashtable`, and arrays, which had:
- ❌ **Inconsistency** - Different APIs for similar operations
- ❌ **No common interface** - Couldn't write generic methods
- ❌ **Limited functionality** - Missing common collection operations

### ✅ Benefits of Collection Framework (Java 1.2+)
- **Interoperability** - Classes work together seamlessly
- **Common Interfaces** - Write generic, reusable code
- **Performance** - Highly optimized implementations
- **Thread-safety options** - Both synchronized and concurrent versions

---

## 2️⃣ Key Interfaces in Collection Framework

```
                    Iterable (java.lang)
                         │
                    Collection
                    ┌─────┴─────┐
                    │           │
                    List        Set     Queue     Deque
                    │           │        │         │
              ┌─────┼─────┐     │        │         │
         ArrayList  LinkedList  HashSet  PriorityQueue  ArrayDeque
         Vector     CopyOnWrite  LinkedHashSet  BlockingQueues
         Stack      ArrayList    TreeSet

                    Map (Separate Hierarchy)
                    ┌─────┼─────┐
                HashMap  Hashtable  SortedMap
                    │        │         │
              LinkedHashMap  |     TreeMap
              WeakHashMap    |
              IdentityHashMap|
              ConcurrentHashMap
              EnumMap
```

### 📌 Core Interfaces Explained

| Interface | Description | Key Characteristics |
|-----------|-------------|---------------------|
| **Iterable** | Root interface | Enables for-each loops |
| **Collection** | Root of collection hierarchy | Basic operations (add, remove, size) |
| **List** | Ordered, allows duplicates | Index-based access |
| **Set** | No duplicates | Focus on uniqueness |
| **Queue** | FIFO order | Elements awaiting processing |
| **Deque** | Double-ended queue | Insert/remove from both ends |
| **Map** | Key-value pairs | Unique keys, not a Collection |

---

## 3️⃣ List Interface & Implementations

### 3.1. ArrayList ⭐ Most Commonly Used

**When to Use:** Need ordered sequence, allow duplicates, require fast random access

```java
import java.util.*;
import java.lang.reflect.Field;

public class ArrayListDeepDive {
    public static void main(String[] args) {
        // ===== CREATION =====
        // 1. Default constructor (initial capacity 10)
        List<String> list1 = new ArrayList<>();
        
        // 2. With initial capacity (for optimization when size is known)
        List<String> list2 = new ArrayList<>(20);
        
        // 3. From another collection
        List<String> list3 = new ArrayList<>(List.of("A", "B", "C"));
        
        // ===== BASIC OPERATIONS =====
        List<Integer> numbers = new ArrayList<>();
        
        // Add elements
        numbers.add(10);        // [10]
        numbers.add(20);         // [10, 20]
        numbers.add(1, 15);      // Insert at index 1: [10, 15, 20]
        System.out.println("After adds: " + numbers);
        
        // Get elements
        System.out.println("Element at index 1: " + numbers.get(1)); // 15
        
        // Size vs Capacity
        System.out.println("Size (actual elements): " + numbers.size()); // 3
        
        // Check existence
        System.out.println("Contains 20? " + numbers.contains(20)); // true
        System.out.println("Contains 99? " + numbers.contains(99)); // false
        
        // Remove elements
        numbers.remove(1);        // Remove by index: removes 15
        numbers.remove(Integer.valueOf(20)); // Remove by object: removes 20
        System.out.println("After removals: " + numbers); // [10]
        
        // Replace element
        numbers.set(0, 100);       // Replace index 0 with 100
        System.out.println("After set: " + numbers); // [100]
        
        // ===== BULK OPERATIONS =====
        List<Integer> listA = new ArrayList<>(List.of(1, 2, 3));
        List<Integer> listB = List.of(4, 5, 6);
        
        listA.addAll(listB);       // Add all elements from listB
        System.out.println("After addAll: " + listA); // [1, 2, 3, 4, 5, 6]
        
        listA.removeAll(List.of(2, 4, 6)); // Remove specific elements
        System.out.println("After removeAll: " + listA); // [1, 3, 5]
        
        listA.retainAll(List.of(1, 5)); // Keep only these elements
        System.out.println("After retainAll: " + listA); // [1, 5]
        
        // ===== ITERATION =====
        List<String> fruits = new ArrayList<>(List.of("Apple", "Banana", "Cherry"));
        
        // Method 1: Traditional for loop
        System.out.println("\n--- For loop ---");
        for (int i = 0; i < fruits.size(); i++) {
            System.out.println(fruits.get(i));
        }
        
        // Method 2: Enhanced for-each (recommended for readability)
        System.out.println("\n--- For-each loop ---");
        for (String fruit : fruits) {
            System.out.println(fruit);
        }
        
        // Method 3: Iterator (useful when removing during iteration)
        System.out.println("\n--- Iterator ---");
        Iterator<String> iterator = fruits.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        
        // Method 4: forEach with lambda (Java 8+)
        System.out.println("\n--- forEach with lambda ---");
        fruits.forEach(fruit -> System.out.println(fruit));
        // Or method reference
        fruits.forEach(System.out::println);
        
        // ===== SORTING =====
        List<Integer> unsorted = new ArrayList<>(List.of(5, 2, 8, 1, 9));
        
        // Natural order (ascending)
        Collections.sort(unsorted);
        System.out.println("Sorted ascending: " + unsorted); // [1, 2, 5, 8, 9]
        
        // Reverse order
        unsorted.sort(Collections.reverseOrder());
        System.out.println("Sorted descending: " + unsorted); // [9, 8, 5, 2, 1]
        
        // Custom comparator (sort by last digit)
        unsorted.sort((a, b) -> (a % 10) - (b % 10));
        System.out.println("Sorted by last digit: " + unsorted); // [1, 2, 5, 8, 9]
        
        // ===== CONVERSION =====
        List<String> nameList = new ArrayList<>(List.of("John", "Jane", "Bob"));
        
        // List to Array
        String[] nameArray = nameList.toArray(new String[0]);
        System.out.println("Array: " + Arrays.toString(nameArray));
        
        // Array to List (fixed-size)
        String[] arr = {"X", "Y", "Z"};
        List<String> fixedList = Arrays.asList(arr);
        // fixedList.add("W"); // ❌ UnsupportedOperationException!
        
        // Fixed list to modifiable ArrayList
        List<String> modifiableList = new ArrayList<>(fixedList);
        modifiableList.add("W"); // ✅ Works
        
        // ===== PERFORMANCE CHARACTERISTICS =====
        demonstratePerformance();
        
        // ===== INTERNAL WORKING DEMO =====
        inspectInternalCapacity();
    }
    
    private static void demonstratePerformance() {
        System.out.println("\n=== Performance Characteristics ===");
        System.out.println("✅ get(index): O(1) - Constant time (direct array access)");
        System.out.println("✅ add(element): O(1) amortized - Usually fast, occasionally resizes");
        System.out.println("❌ add(index, element): O(n) - Shifts elements right");
        System.out.println("❌ remove(index): O(n) - Shifts elements left");
        System.out.println("❌ remove(object): O(n) - Requires search + shift");
        System.out.println("✅ contains? O(n) - Linear search (use HashSet for faster contains)");
    }
    
    private static void inspectInternalCapacity() {
        try {
            ArrayList<Integer> list = new ArrayList<>();
            System.out.println("\n=== Internal Capacity Growth ===");
            
            // Add 11 elements to trigger resize (default capacity 10 → 15)
            for (int i = 0; i < 11; i++) {
                list.add(i);
            }
            
            // Using reflection to peek at internal array (for demonstration only!)
            Field elementData = ArrayList.class.getDeclaredField("elementData");
            elementData.setAccessible(true);
            Object[] internalArray = (Object[]) elementData.get(list);
            System.out.println("Size: " + list.size() + ", Capacity: " + internalArray.length);
            
            list.trimToSize();
            internalArray = (Object[]) elementData.get(list);
            System.out.println("After trimToSize - Size: " + list.size() + 
                             ", Capacity: " + internalArray.length);
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 3.2. LinkedList

**When to Use:** Frequent insertions/deletions in middle, need queue/stack behavior

```java
import java.util.*;

public class LinkedListDeepDive {
    public static void main(String[] args) {
        // ===== CREATION =====
        LinkedList<String> linkedList = new LinkedList<>();
        
        // ===== LIST OPERATIONS =====
        linkedList.add("Apple");      // Add to end
        linkedList.add(0, "Banana");  // Add at index 0
        linkedList.addFirst("First"); // Add to beginning
        linkedList.addLast("Last");   // Add to end
        
        System.out.println("LinkedList: " + linkedList);
        // [First, Banana, Apple, Last]
        
        // Get elements
        System.out.println("First: " + linkedList.getFirst());    // First
        System.out.println("Last: " + linkedList.getLast());      // Last
        System.out.println("Index 1: " + linkedList.get(1));      // Banana
        
        // Remove elements
        linkedList.removeFirst();      // Remove first
        linkedList.removeLast();       // Remove last
        linkedList.remove(0);          // Remove by index
        linkedList.remove("Apple");    // Remove by object
        
        System.out.println("After removals: " + linkedList); // []
        
        // ===== QUEUE OPERATIONS (FIFO) =====
        System.out.println("\n=== LinkedList as Queue ===");
        LinkedList<Integer> queue = new LinkedList<>();
        
        // Enqueue (add to end)
        queue.offer(10);    // offer() returns false if fails
        queue.offer(20);
        queue.add(30);      // add() throws exception if fails
        
        System.out.println("Queue: " + queue); // [10, 20, 30]
        
        // Peek (examine front without removing)
        System.out.println("Peek: " + queue.peek());    // 10
        System.out.println("Element: " + queue.element()); // 10 (throws if empty)
        
        // Dequeue (remove from front)
        System.out.println("Poll: " + queue.poll());     // 10 (returns null if empty)
        System.out.println("Remove: " + queue.remove()); // 20 (throws if empty)
        
        System.out.println("Queue after: " + queue); // [30]
        
        // ===== STACK OPERATIONS (LIFO) =====
        System.out.println("\n=== LinkedList as Stack ===");
        LinkedList<Integer> stack = new LinkedList<>();
        
        // Push (add to front)
        stack.push(10);
        stack.push(20);
        stack.push(30);
        
        System.out.println("Stack: " + stack); // [30, 20, 10]
        
        // Peek (examine top)
        System.out.println("Peek: " + stack.peek()); // 30
        
        // Pop (remove from front)
        System.out.println("Pop: " + stack.pop());   // 30
        System.out.println("Pop: " + stack.pop());   // 20
        
        System.out.println("Stack after: " + stack); // [10]
        
        // ===== DEQUE OPERATIONS =====
        System.out.println("\n=== LinkedList as Deque ===");
        LinkedList<Integer> deque = new LinkedList<>();
        
        deque.addFirst(1);    // Add to front
        deque.addLast(2);     // Add to back
        deque.addFirst(0);    // Add to front
        
        System.out.println("Deque: " + deque); // [0, 1, 2]
        
        // Remove from both ends
        System.out.println("Remove first: " + deque.removeFirst()); // 0
        System.out.println("Remove last: " + deque.removeLast());   // 2
        
        System.out.println("Deque after: " + deque); // [1]
        
        // ===== PERFORMANCE =====
        System.out.println("\n=== Performance Characteristics ===");
        System.out.println("✅ addFirst/addLast/removeFirst/removeLast: O(1)");
        System.out.println("✅ offer/poll/peek: O(1)");
        System.out.println("❌ get(index): O(n) - Must traverse from beginning");
        System.out.println("❌ remove(index): O(n) - Traverse + pointer updates");
        System.out.println("❌ contains: O(n) - Linear search");
        System.out.println("📌 Memory: More than ArrayList (stores pointers)");
    }
}
```

### 3.3. Vector (Legacy Thread-Safe List)

**When to Use:** Only when legacy code requires it, otherwise use `ArrayList` or `CopyOnWriteArrayList`

```java
import java.util.*;

public class VectorDemo {
    public static void main(String[] args) throws InterruptedException {
        // ===== CREATION =====
        Vector<Integer> vector = new Vector<>();           // Default capacity 10
        Vector<Integer> vector2 = new Vector<>(5);        // Initial capacity 5
        Vector<Integer> vector3 = new Vector<>(5, 2);     // Capacity 5, increment 2
        
        System.out.println("Default capacity: " + vector.capacity()); // 10
        System.out.println("Custom capacity: " + vector2.capacity()); // 5
        
        // Add elements to show capacity growth
        for (int i = 0; i < 6; i++) {
            vector3.add(i);
        }
        System.out.println("After 6 adds (increment 2): " + vector3.capacity()); // 7 (5+2)
        
        // ===== THREAD SAFETY DEMONSTRATION =====
        System.out.println("\n=== Thread Safety Demo ===");
        
        // ArrayList (Not thread-safe)
        List<Integer> arrayList = new ArrayList<>();
        Runnable addTask = () -> {
            for (int i = 0; i < 1000; i++) {
                arrayList.add(i);
            }
        };
        
        Thread t1 = new Thread(addTask);
        Thread t2 = new Thread(addTask);
        t1.start(); t2.start();
        t1.join(); t2.join();
        
        System.out.println("ArrayList size (expected 2000): " + arrayList.size());
        // Often less than 2000 due to race conditions
        
        // Vector (Thread-safe)
        Vector<Integer> threadSafeVector = new Vector<>();
        Runnable vectorAddTask = () -> {
            for (int i = 0; i < 1000; i++) {
                threadSafeVector.add(i);
            }
        };
        
        Thread t3 = new Thread(vectorAddTask);
        Thread t4 = new Thread(vectorAddTask);
        t3.start(); t4.start();
        t3.join(); t4.join();
        
        System.out.println("Vector size (expected 2000): " + threadSafeVector.size());
        // Always 2000 - synchronized methods prevent corruption
        
        // ===== PERFORMANCE COMPARISON =====
        long start = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            arrayList.add(i);
        }
        long arrayListTime = System.nanoTime() - start;
        
        start = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            threadSafeVector.add(i);
        }
        long vectorTime = System.nanoTime() - start;
        
        System.out.printf("\nArrayList time (single thread): %.2f ms%n", arrayListTime / 1_000_000.0);
        System.out.printf("Vector time (single thread): %.2f ms%n", vectorTime / 1_000_000.0);
        // Vector is slower due to synchronization overhead even in single thread
    }
}
```

### 3.4. Stack (LIFO)

**When to Use:** Need LIFO behavior in thread-safe environment (consider `ArrayDeque` for better performance)

```java
import java.util.*;

public class StackDemo {
    public static void main(String[] args) {
        // ===== TRADITIONAL STACK =====
        Stack<Integer> stack = new Stack<>();
        
        // Push elements
        stack.push(10);
        stack.push(20);
        stack.push(30);
        
        System.out.println("Stack: " + stack); // [10, 20, 30]
        
        // Peek (view top without removing)
        System.out.println("Peek: " + stack.peek()); // 30
        
        // Pop (remove and return top)
        System.out.println("Pop: " + stack.pop());   // 30
        System.out.println("Stack after pop: " + stack); // [10, 20]
        
        // Search (1-based position from top)
        System.out.println("Position of 10: " + stack.search(10)); // 2
        System.out.println("Position of 99: " + stack.search(99)); // -1
        
        // Check if empty
        System.out.println("Is empty? " + stack.empty()); // false
        
        // ===== MODERN APPROACH: ArrayDeque as Stack =====
        System.out.println("\n=== ArrayDeque as Stack (Recommended) ===");
        ArrayDeque<Integer> modernStack = new ArrayDeque<>();
        
        modernStack.push(100);
        modernStack.push(200);
        modernStack.push(300);
        
        System.out.println("Modern stack: " + modernStack); // [300, 200, 100]
        System.out.println("Pop: " + modernStack.pop());    // 300
        System.out.println("Peek: " + modernStack.peek());  // 200
        
        // ===== COMMON USE CASES =====
        System.out.println("\n=== Use Case: Balanced Parentheses ===");
        String expression = "({[]})";
        System.out.println(expression + " is balanced? " + isBalanced(expression));
        
        expression = "({[}])";
        System.out.println(expression + " is balanced? " + isBalanced(expression));
    }
    
    // Classic stack use case: checking balanced parentheses
    public static boolean isBalanced(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '{' || c == '[') {
                stack.push(c);
            } else if (c == ')' && !stack.isEmpty() && stack.peek() == '(') {
                stack.pop();
            } else if (c == '}' && !stack.isEmpty() && stack.peek() == '{') {
                stack.pop();
            } else if (c == ']' && !stack.isEmpty() && stack.peek() == '[') {
                stack.pop();
            } else {
                return false;
            }
        }
        return stack.isEmpty();
    }
}
```

### 3.5. CopyOnWriteArrayList (Thread-Safe for Read-Heavy Scenarios)

**When to Use:** Many readers, few writers; need stable iterators

```java
import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

public class CopyOnWriteArrayListDemo {
    public static void main(String[] args) throws InterruptedException {
        // ===== DEMO: Modification during iteration =====
        System.out.println("=== Modification During Iteration ===");
        
        // ArrayList - throws ConcurrentModificationException
        List<String> arrayList = new ArrayList<>(List.of("A", "B", "C"));
        System.out.println("ArrayList before: " + arrayList);
        
        try {
            for (String s : arrayList) {
                if (s.equals("B")) {
                    arrayList.add("D"); // ❌ Throws exception
                }
                System.out.println(s);
            }
        } catch (ConcurrentModificationException e) {
            System.out.println("❌ ArrayList: " + e.getClass().getSimpleName());
        }
        
        // CopyOnWriteArrayList - handles it gracefully
        List<String> cowList = new CopyOnWriteArrayList<>(List.of("A", "B", "C"));
        System.out.println("\nCopyOnWriteArrayList before: " + cowList);
        
        for (String s : cowList) {
            System.out.println("Reading: " + s);
            if (s.equals("B")) {
                cowList.add("D"); // ✅ Works fine - modifies a copy
                System.out.println("   Added D to new copy");
            }
        }
        System.out.println("CopyOnWriteArrayList after: " + cowList);
        // Note: 'D' not printed in loop because iterator sees snapshot
        
        // ===== THREAD SAFETY DEMO =====
        System.out.println("\n=== Thread Safety Demo ===");
        CopyOnWriteArrayList<Integer> sharedList = new CopyOnWriteArrayList<>();
        sharedList.addAll(List.of(1, 2, 3, 4, 5));
        
        // Reader thread - gets stable snapshot
        Thread reader = new Thread(() -> {
            System.out.println("Reader starting...");
            for (Integer num : sharedList) {
                System.out.println("Reader sees: " + num);
                try { Thread.sleep(50); } catch (InterruptedException e) {}
            }
        });
        
        // Writer thread - modifies while reader iterates
        Thread writer = new Thread(() -> {
            try { Thread.sleep(100); } catch (InterruptedException e) {}
            System.out.println("Writer adding 6, 7, 8");
            sharedList.add(6);
            sharedList.add(7);
            sharedList.add(8);
        });
        
        reader.start();
        writer.start();
        reader.join();
        writer.join();
        
        // ===== PERFORMANCE CHARACTERISTICS =====
        System.out.println("\n=== Performance Characteristics ===");
        System.out.println("✅ Reads: O(1) - No locking, direct array access");
        System.out.println("❌ Writes: O(n) - Creates full copy of array");
        System.out.println("📌 Best for: Read-heavy, write-rarely scenarios");
        System.out.println("📌 Iterator: Snapshot at creation time");
    }
}
```

---

## 4️⃣ Map Interface & Implementations

### 4.1. HashMap ⭐ Most Commonly Used

**When to Use:** Need key-value pairs with fast access, no order requirements

```java
import java.util.*;
import java.util.Map.Entry;

public class HashMapDeepDive {
    public static void main(String[] args) {
        // ===== CREATION =====
        Map<String, Integer> hashMap = new HashMap<>();
        
        // ===== BASIC OPERATIONS =====
        // Put elements
        hashMap.put("Apple", 100);
        hashMap.put("Banana", 50);
        hashMap.put("Cherry", 75);
        hashMap.put("Apple", 90); // Replaces previous value (100 → 90)
        
        System.out.println("HashMap: " + hashMap);
        // {Apple=90, Banana=50, Cherry=75} (order may vary)
        
        // Get elements
        System.out.println("Price of Apple: " + hashMap.get("Apple"));    // 90
        System.out.println("Price of Mango: " + hashMap.get("Mango"));    // null
        
        // Get with default
        System.out.println("Price of Mango: " + hashMap.getOrDefault("Mango", 0)); // 0
        
        // Check existence
        System.out.println("Contains key 'Banana'? " + hashMap.containsKey("Banana")); // true
        System.out.println("Contains value 75? " + hashMap.containsValue(75)); // true
        
        // Remove
        hashMap.remove("Banana");
        System.out.println("After removing Banana: " + hashMap); // {Apple=90, Cherry=75}
        
        // Replace
        hashMap.replace("Cherry", 80);
        System.out.println("After replace Cherry: " + hashMap); // {Apple=90, Cherry=80}
        
        // ===== BULK OPERATIONS =====
        Map<String, Integer> newItems = new HashMap<>();
        newItems.put("Date", 120);
        newItems.put("Elderberry", 150);
        
        hashMap.putAll(newItems);
        System.out.println("After putAll: " + hashMap);
        
        // ===== ITERATION =====
        System.out.println("\n=== Iteration Methods ===");
        
        // Method 1: Iterate over entries (most efficient)
        System.out.println("1. Using entrySet():");
        for (Entry<String, Integer> entry : hashMap.entrySet()) {
            System.out.println("   " + entry.getKey() + " → " + entry.getValue());
        }
        
        // Method 2: Iterate over keys
        System.out.println("2. Using keySet():");
        for (String key : hashMap.keySet()) {
            System.out.println("   " + key + " → " + hashMap.get(key));
        }
        
        // Method 3: Iterate over values
        System.out.println("3. Using values():");
        for (Integer value : hashMap.values()) {
            System.out.println("   " + value);
        }
        
        // Method 4: forEach with lambda (Java 8+)
        System.out.println("4. Using forEach():");
        hashMap.forEach((key, value) -> 
            System.out.println("   " + key + " → " + value));
        
        // ===== ADVANCED OPERATIONS =====
        System.out.println("\n=== Advanced Operations ===");
        
        // putIfAbsent
        hashMap.putIfAbsent("Apple", 999); // Won't put (key exists)
        hashMap.putIfAbsent("Fig", 200);   // Will put (key absent)
        System.out.println("After putIfAbsent: " + hashMap);
        
        // computeIfAbsent
        hashMap.computeIfAbsent("Grape", k -> 180);
        System.out.println("After computeIfAbsent Grape: " + hashMap);
        
        // computeIfPresent
        hashMap.computeIfPresent("Apple", (k, v) -> v + 10);
        System.out.println("After computeIfPresent Apple (+10): " + hashMap);
        
        // merge
        hashMap.merge("Apple", 5, (oldVal, newVal) -> oldVal + newVal);
        System.out.println("After merge Apple (+5): " + hashMap);
        
        // ===== NULL HANDLING =====
        hashMap.put(null, 999);     // One null key allowed
        hashMap.put("NullValue", null); // Multiple null values allowed
        System.out.println("\nNull handling: " + hashMap);
        
        // ===== INTERNAL WORKING DEMO =====
        explainInternalWorking();
    }
    
    private static void explainInternalWorking() {
        System.out.println("\n=== HashMap Internal Working ===");
        System.out.println("1. Array of buckets (default 16)");
        System.out.println("2. hashCode() determines bucket index");
        System.out.println("3. equals() finds exact key within bucket");
        System.out.println("4. Collision handling:");
        System.out.println("   - Java 7: LinkedList (O(n) worst case)");
        System.out.println("   - Java 8+: Tree after threshold (O(log n) worst case)");
        System.out.println("5. Load factor (0.75) triggers resize when 75% full");
        System.out.println("6. Rehashing: Doubles array size, recomputes all positions");
        
        // Time Complexity
        System.out.println("\n⏱️ Time Complexity:");
        System.out.println("   ✅ get/put/remove/containsKey: O(1) average");
        System.out.println("   ❌ containsValue: O(n) - must scan all");
        System.out.println("   ⚠️ Worst case (many collisions): O(log n) or O(n)");
    }
}

// IMPORTANT: Custom class as HashMap key
class Person {
    private String name;
    private int id;
    
    public Person(String name, int id) {
        this.name = name;
        this.id = id;
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return id == person.id && Objects.equals(name, person.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, id); // Must override both!
    }
    
    @Override
    public String toString() {
        return name + "(" + id + ")";
    }
}
```

### 4.2. LinkedHashMap (Maintains Insertion Order)

**When to Use:** Need predictable iteration order (insertion or access order)

```java
import java.util.*;

public class LinkedHashMapDemo {
    public static void main(String[] args) {
        // ===== INSERTION ORDER (default) =====
        Map<String, Integer> insertionOrder = new LinkedHashMap<>();
        
        insertionOrder.put("Banana", 2);
        insertionOrder.put("Apple", 5);
        insertionOrder.put("Cherry", 3);
        insertionOrder.put("Date", 1);
        
        System.out.println("Insertion Order:");
        System.out.println(insertionOrder);
        // {Banana=2, Apple=5, Cherry=3, Date=1} - insertion order preserved
        
        // ===== ACCESS ORDER (for LRU cache) =====
        Map<String, Integer> accessOrder = new LinkedHashMap<>(16, 0.75f, true);
        
        accessOrder.put("One", 1);
        accessOrder.put("Two", 2);
        accessOrder.put("Three", 3);
        
        System.out.println("\nInitial (insertion order): " + accessOrder);
        
        // Access some elements
        accessOrder.get("One");    // Moves "One" to end
        accessOrder.get("Two");    // Moves "Two" to end
        
        System.out.println("After access (access order): " + accessOrder);
        // {Three=3, One=1, Two=2} - most recently accessed at end
        
        // ===== LRU CACHE IMPLEMENTATION =====
        System.out.println("\n=== LRU Cache Demo ===");
        
        class LRUCache<K, V> extends LinkedHashMap<K, V> {
            private final int maxSize;
            
            public LRUCache(int maxSize) {
                super(16, 0.75f, true); // access order = true
                this.maxSize = maxSize;
            }
            
            @Override
            protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
                return size() > maxSize; // Remove oldest when over capacity
            }
        }
        
        LRUCache<Integer, String> cache = new LRUCache<>(3);
        
        cache.put(1, "One");
        cache.put(2, "Two");
        cache.put(3, "Three");
        System.out.println("Cache after adding 3 items: " + cache);
        
        cache.get(1); // Access 1, makes it most recent
        System.out.println("After accessing key 1: " + cache);
        
        cache.put(4, "Four"); // This should evict the least recently used (2)
        System.out.println("After adding key 4 (evicts LRU): " + cache);
    }
}
```

### 4.3. WeakHashMap (Auto-Cleanup with GC)

**When to Use:** Caching scenarios where entries should be removed when keys are no longer referenced elsewhere

```java
import java.util.*;
import java.lang.ref.WeakReference;

public class WeakHashMapDemo {
    static class Image {
        String name;
        
        Image(String name) { this.name = name; }
        
        @Override
        public String toString() { return "Image(" + name + ")"; }
    }
    
    public static void main(String[] args) throws InterruptedException {
        // ===== WEAK REFERENCE DEMO =====
        System.out.println("=== WeakReference Demo ===");
        
        Image strongImage = new Image("Strong");
        WeakReference<Image> weakRef = new WeakReference<>(new Image("Weak"));
        
        System.out.println("Before GC - Strong: " + strongImage);
        System.out.println("Before GC - Weak: " + weakRef.get());
        
        System.gc();
        Thread.sleep(100); // Give GC time to run
        
        System.out.println("After GC - Strong: " + strongImage);
        System.out.println("After GC - Weak: " + weakRef.get()); // May be null
        
        // ===== WEAK HASH MAP DEMO =====
        System.out.println("\n=== WeakHashMap Demo ===");
        
        Map<Image, String> weakMap = new WeakHashMap<>();
        
        // Using new keyword so objects aren't interned
        Image img1 = new Image("Duke");
        Image img2 = new Image("Java");
        
        weakMap.put(img1, "First Image");
        weakMap.put(img2, "Second Image");
        
        System.out.println("WeakMap before nullify: " + weakMap);
        
        // Remove strong references
        img1 = null;
        img2 = null;
        
        System.out.println("Strong references nullified. Suggesting GC...");
        System.gc();
        Thread.sleep(100);
        
        System.out.println("WeakMap after GC: " + weakMap); // Likely empty
    }
}
```

### 4.4. IdentityHashMap (Reference Equality)

**When to Use:** Need to distinguish objects by memory address, not content

```java
import java.util.*;

public class IdentityHashMapDemo {
    public static void main(String[] args) {
        // ===== COMPARISON WITH HASHMAP =====
        System.out.println("=== HashMap (content equality) ===");
        Map<String, String> hashMap = new HashMap<>();
        
        String key1 = new String("key");
        String key2 = new String("key");
        
        hashMap.put(key1, "Value1");
        hashMap.put(key2, "Value2"); // Replaces Value1 (keys are equal by content)
        
        System.out.println("HashMap size: " + hashMap.size()); // 1
        System.out.println("HashMap: " + hashMap); // {key=Value2}
        
        System.out.println("\n=== IdentityHashMap (reference equality) ===");
        Map<String, String> identityMap = new IdentityHashMap<>();
        
        identityMap.put(key1, "Value1");
        identityMap.put(key2, "Value2"); // Different object, different entry
        
        System.out.println("IdentityHashMap size: " + identityMap.size()); // 2
        System.out.println("IdentityHashMap: " + identityMap);
        // {key=Value1, key=Value2} - both keys appear same but are different objects
        
        // ===== DEMONSTRATING REFERENCE EQUALITY =====
        System.out.println("\n=== Reference Comparison ===");
        System.out.println("key1 == key2: " + (key1 == key2)); // false
        System.out.println("key1.equals(key2): " + key1.equals(key2)); // true
        System.out.println("System.identityHashCode(key1): " + System.identityHashCode(key1));
        System.out.println("System.identityHashCode(key2):
