# 📚 Complete Guide to Java Multithreading and Concurrency

## 1️⃣ Introduction to Multithreading

### What is Multithreading?

**Multithreading** is a programming technique where multiple threads of execution exist within a single application. A **thread** is the smallest unit of execution within a process, capable of running independently while sharing the same memory space with other threads in the same process .

Think of it like a restaurant kitchen:
- The **process** is the entire restaurant
- **Threads** are the chefs working simultaneously
- They share the same kitchen space (resources) but work on different orders 

### Why Use Multithreading?

| Benefit | Description |
|---------|-------------|
| **Improved Performance** | Divide complex tasks into smaller parts that execute simultaneously, leveraging multi-core CPUs  |
| **Better Resource Utilization** | Optimal use of system resources; while one thread waits for I/O, others continue working  |
| **Improved Responsiveness** | Keep UI responsive while background tasks run (e.g., text editor checking spelling while you type)  |
| **Scalability** | Handle multiple user requests concurrently in server applications  |
| **Asynchronous Processing** | Perform lengthy operations without blocking the main application flow  |

### Process vs. Thread

| Aspect | Process | Thread |
|--------|---------|--------|
| **Memory** | Each process has its own memory space | Threads share the same memory space within a process  |
| **Overhead** | High (context switching is expensive) | Low  |
| **Communication** | Inter-process communication is complex | Direct access to shared memory  |
| **Example** | Running Google Chrome and Eclipse separately | Google Chrome's tabs each run as threads  |

---

## 2️⃣ Core Concepts

### Concurrency vs. Parallelism

These terms are often confused but represent distinct concepts:

- **Concurrency**: Dealing with multiple tasks at once (managing and coordinating tasks). Can be achieved on a single-core processor through time-slicing .
- **Parallelism**: Executing multiple tasks simultaneously (requires multiple cores/processors) .

> **Analogy**: Concurrency is like juggling multiple balls – you're working on all of them, but only one is in the air at a time. Parallelism is like having multiple jugglers working simultaneously.

### Amdahl's Law – Theoretical Performance Gain

The maximum performance gain from parallelization is constrained by the portion of the task that cannot run in parallel :

```
Maximum performance gain = 1 / (F + ((1-F)/N))

Where:
- F = percentage that cannot run in parallel
- N = number of processes
```

### Concurrency Challenges

| Problem | Description |
|---------|-------------|
| **Visibility Problem** | Thread A reads shared data that Thread B modifies, but Thread A remains unaware of the change  |
| **Access Problem** | Multiple threads read and modify the same shared data simultaneously, causing corruption  |
| **Liveness Failure** | Program becomes unresponsive due to deadlocks or starvation  |
| **Safety Failure** | Program produces incorrect data due to improper handling of shared resources  |

---

## 3️⃣ Thread Creation in Java

Java provides three primary ways to create threads .

### Method 1: Extending the Thread Class

```java
class CounterThread extends Thread {
    @Override
    public void run() {
        for(int i = 1; i <= 5; i++) {
            System.out.println(Thread.currentThread().getName() + " counting: " + i);
            try {
                Thread.sleep(1000); // Pause for 1 second
            } catch (InterruptedException e) {
                System.out.println("Thread interrupted");
            }
        }
    }
}

// Usage
public class ThreadExample {
    public static void main(String[] args) {
        CounterThread thread = new CounterThread();
        thread.start(); // Starts the thread (calls run() internally)
        
        // Common mistake: calling run() directly executes in main thread
        // thread.run(); // ❌ This would NOT create a new thread
    }
}
```

### Method 2: Implementing the Runnable Interface (Preferred)

```java
class PrintTask implements Runnable {
    private String message;
    
    public PrintTask(String message) {
        this.message = message;
    }
    
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + 
                          " processing: " + message);
    }
}

// Usage
public class RunnableExample {
    public static void main(String[] args) {
        PrintTask task = new PrintTask("Hello from thread!");
        Thread thread = new Thread(task);
        thread.start();
    }
}
```

**Why Runnable is preferred** :
- Java doesn't support multiple inheritance – extending `Thread` prevents extending other classes
- Better separation of task logic from thread behavior
- Easier to reuse the same task with thread pools

### Method 3: Using Lambda Expressions (Java 8+)

```java
public class LambdaThreadExample {
    public static void main(String[] args) {
        // Using lambda for concise code
        Thread thread = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + 
                              " is running");
            // Task logic here
        });
        thread.start();
        
        // Even shorter with method reference
        Thread thread2 = new Thread(() -> processTask());
        thread2.start();
    }
    
    private static void processTask() {
        System.out.println("Processing task in: " + 
                          Thread.currentThread().getName());
    }
}
```

---

## 4️⃣ Thread Lifecycle and States

A thread transitions through several states during its lifetime :

```
                     ┌─────────────┐
                     │    NEW      │
                     └──────┬──────┘
                            │ start()
                            ↓
┌─────────────────────────────────────────────┐
│                  RUNNABLE                    │
│  (includes both READY and RUNNING states)    │
└──────┬────────────────────┬─────────────────┘
       │                     │
       │ wait()              │ sleep(), join()
       ↓                     ↓
┌──────────────┐      ┌───────────────┐
│   WAITING    │      │ TIMED_WAITING │
└──────────────┘      └───────────────┘
       ↑                     ↑
       │ notify()            │ timeout/interrupt
       └─────────────────────┘
              │
              ↓
    ┌─────────────────┐
    │   TERMINATED    │
    └─────────────────┘
```

### Thread States Explained

| State | Description |
|-------|-------------|
| **NEW** | Thread created but not yet started (`new Thread()`)  |
| **RUNNABLE** | Thread started and eligible for CPU scheduling. May be running or ready to run  |
| **BLOCKED** | Waiting to acquire a lock to enter synchronized code  |
| **WAITING** | Waiting indefinitely for another thread to perform a specific action  |
| **TIMED_WAITING** | Waiting for a specified time period (`sleep(ms)`, `join(ms)`)  |
| **TERMINATED** | Completed execution or was aborted  |

### Key Thread Methods

```java
public class ThreadLifecycleDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            try {
                System.out.println("Thread running...");
                Thread.sleep(2000); // TIMED_WAITING state
                System.out.println("Thread woke up");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        System.out.println("State: " + thread.getState()); // NEW
        
        thread.start();
        System.out.println("State after start: " + thread.getState()); // RUNNABLE
        
        Thread.sleep(100);
        System.out.println("State during sleep: " + thread.getState()); // TIMED_WAITING
        
        thread.join(); // Wait for completion
        System.out.println("Final state: " + thread.getState()); // TERMINATED
    }
}
```

---

## 5️⃣ Race Conditions and Data Inconsistency

### What is a Race Condition?

A **race condition** occurs when multiple threads access shared data simultaneously, and the final outcome depends on the unpredictable timing of thread execution .

### Example: The Problem

```java
class Counter {
    private int count = 0;
    
    public void increment() {
        count = count + 1; // Not atomic!
    }
    
    public int getCount() {
        return count;
    }
}

public class RaceConditionDemo {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        // Create 1000 threads that all increment the counter
        Thread[] threads = new Thread[1000];
        for (int i = 0; i < 1000; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 100; j++) {
                    counter.increment();
                }
            });
            threads[i].start();
        }
        
        // Wait for all threads to complete
        for (Thread t : threads) {
            t.join();
        }
        
        // Expected: 100,000 (1000 threads × 100 increments)
        // Actual: Often less due to race conditions
        System.out.println("Final count: " + counter.getCount());
    }
}
```

### Why This Happens

The operation `count = count + 1` is not atomic. It involves three steps :
1. Read current value of `count`
2. Increment the value
3. Write new value back to memory

If two threads read the same value (say 42) simultaneously, both will increment to 43 and write back, resulting in one lost increment.

---

## 6️⃣ Synchronization – The Solution

Synchronization ensures that only one thread can access a shared resource at a time, preventing race conditions .

### 6.1. Synchronized Methods

```java
class SafeCounter {
    private int count = 0;
    
    // Only one thread can execute this method at a time
    public synchronized void increment() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}
```

### 6.2. Synchronized Blocks (More Efficient)

Synchronized blocks allow finer-grained control, locking only the critical section rather than the entire method .

```java
class WebCrawler {
    private List<String> crawledSites = new ArrayList<>();
    private List<String> linkedSites = new ArrayList<>();
    
    public void addSite(String site) {
        // Only synchronize the critical section
        synchronized (this) {
            if (!crawledSites.contains(site)) {
                linkedSites.add(site);
            }
        }
    }
    
    public String nextSite() {
        synchronized (this) {
            if (!linkedSites.isEmpty()) {
                String site = linkedSites.remove(0);
                crawledSites.add(site);
                return site;
            }
            return null;
        }
    }
}
```

### 6.3. Static Synchronization

For static methods, the lock is on the `Class` object, not the instance:

```java
class StaticCounter {
    private static int count = 0;
    
    // Locks on StaticCounter.class
    public static synchronized void increment() {
        count++;
    }
    
    // Equivalent block synchronization
    public static void decrement() {
        synchronized (StaticCounter.class) {
            count--;
        }
    }
}
```

### 6.4. ReentrantLock – Advanced Locking

The `java.util.concurrent.locks` package provides more flexible locking .

```java
import java.util.concurrent.locks.ReentrantLock;

class LockCounter {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();
    
    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock(); // Always release in finally block
        }
    }
    
    // Try with timeout – avoids deadlocks
    public boolean tryIncrement(long timeout, TimeUnit unit) {
        try {
            if (lock.tryLock(timeout, unit)) {
                try {
                    count++;
                    return true;
                } finally {
                    lock.unlock();
                }
            }
            return false; // Couldn't acquire lock in time
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            return false;
        }
    }
}
```

**Benefits of ReentrantLock** :
- Ability to attempt lock with timeout
- Interruptible lock acquisition
- Fairness policy (threads acquire locks in order)
- Ability to check if lock is held

### 6.5. ReadWriteLock – Optimized for Read-Heavy Scenarios

```java
import java.util.concurrent.locks.ReentrantReadWriteLock;

class ReadWriteData {
    private int value;
    private final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
    
    public int read() {
        rwLock.readLock().lock();
        try {
            return value; // Multiple threads can read simultaneously
        } finally {
            rwLock.readLock().unlock();
        }
    }
    
    public void write(int newValue) {
        rwLock.writeLock().lock();
        try {
            value = newValue; // Exclusive access for writing
        } finally {
            rwLock.writeLock().unlock();
        }
    }
}
```

---

## 7️⃣ The `volatile` Keyword

The `volatile` keyword ensures that reads and writes to a variable are always done directly to main memory, not cached locally .

### When to Use `volatile`

- Simple flag variables (boolean, int) where atomicity isn't required
- Variables that are read by multiple threads but written by only one
- Ensuring visibility of changes across threads

```java
class VolatileExample {
    private volatile boolean running = true;
    
    public void startTask() {
        new Thread(() -> {
            while (running) {
                // Do work
                System.out.println("Working...");
            }
            System.out.println("Task stopped");
        }).start();
    }
    
    public void stopTask() {
        running = false; // Will be visible to other thread immediately
    }
}
```

### `volatile` vs. `synchronized`

| Feature | `volatile` | `synchronized` |
|---------|------------|----------------|
| **Mutual exclusion** | No | Yes |
| **Visibility guarantee** | Yes | Yes |
| **Atomicity** | No (except for 64-bit types) | Yes |
| **Performance** | Low overhead | Higher overhead |

> **Important**: `volatile` does **not** provide atomicity. `count++` with `volatile` is still not thread-safe .

---

## 8️⃣ Atomic Variables

For simple counters and accumulators, atomic variables provide thread-safe operations without explicit synchronization .

```java
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.atomic.AtomicReference;
import java.util.concurrent.atomic.AtomicLong;

class AtomicCounter {
    private AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet(); // Atomic increment
    }
    
    public void add(int delta) {
        count.addAndGet(delta); // Atomic addition
    }
    
    public int getCount() {
        return count.get(); // Atomic read
    }
    
    // Compare-and-set example
    public boolean trySet(int expected, int newValue) {
        return count.compareAndSet(expected, newValue);
    }
}

class AtomicStack {
    private AtomicReference<Node> top = new AtomicReference<>(null);
    
    public void push(String value) {
        Node newNode = new Node(value);
        Node oldTop;
        do {
            oldTop = top.get();
            newNode.next = oldTop;
        } while (!top.compareAndSet(oldTop, newNode));
    }
    
    private static class Node {
        final String value;
        Node next;
        Node(String value) { this.value = value; }
    }
}
```

**Why atomic variables are fast** :
- Use **Compare-And-Swap (CAS)** CPU instructions
- Non-blocking – no thread suspension
- Avoid context switching overhead

---

## 9️⃣ Thread Coordination – Inter-thread Communication

Threads often need to coordinate their activities. Java provides `wait()`, `notify()`, and `notifyAll()` for this purpose .

### The Producer-Consumer Pattern

```java
import java.util.LinkedList;
import java.util.Queue;

class SharedBuffer {
    private Queue<Integer> buffer = new LinkedList<>();
    private int capacity;
    
    public SharedBuffer(int capacity) {
        this.capacity = capacity;
    }
    
    public synchronized void produce(int value) throws InterruptedException {
        while (buffer.size() == capacity) {
            System.out.println("Buffer full. Producer waiting...");
            wait(); // Wait for consumer to consume
        }
        
        buffer.add(value);
        System.out.println("Produced: " + value);
        
        notifyAll(); // Notify waiting consumers
    }
    
    public synchronized int consume() throws InterruptedException {
        while (buffer.isEmpty()) {
            System.out.println("Buffer empty. Consumer waiting...");
            wait(); // Wait for producer to produce
        }
        
        int value = buffer.poll();
        System.out.println("Consumed: " + value);
        
        notifyAll(); // Notify waiting producers
        return value;
    }
}

public class ProducerConsumerDemo {
    public static void main(String[] args) {
        SharedBuffer buffer = new SharedBuffer(5);
        
        // Producer thread
        Thread producer = new Thread(() -> {
            int value = 0;
            try {
                while (true) {
                    buffer.produce(value++);
                    Thread.sleep(500);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        // Consumer thread
        Thread consumer = new Thread(() -> {
            try {
                while (true) {
                    buffer.consume();
                    Thread.sleep(1000); // Slower consumer
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

### Key Points about `wait()` and `notify()` 

- Must be called from within `synchronized` context
- `wait()` releases the lock and puts thread in WAITING state
- `notify()` wakes one waiting thread (not guaranteed which)
- `notifyAll()` wakes all waiting threads
- Always use `while` loop for condition checking (spurious wakeups)

---

## 🔟 The `ThreadLocal` Class

`ThreadLocal` provides thread-local variables – each thread has its own, independently initialized copy .

```java
class UserContext {
    private static ThreadLocal<String> currentUser = new ThreadLocal<>();
    
    public static void setUser(String user) {
        currentUser.set(user);
    }
    
    public static String getUser() {
        return currentUser.get();
    }
    
    public static void clear() {
        currentUser.remove(); // Prevent memory leaks
    }
}

class UserService {
    public void processRequest() {
        // Each thread sees its own user
        String user = UserContext.getUser();
        System.out.println(Thread.currentThread().getName() + 
                          " processing for: " + user);
    }
}

public class ThreadLocalDemo {
    public static void main(String[] args) {
        UserService service = new UserService();
        
        // Thread 1 with user "Alice"
        new Thread(() -> {
            UserContext.setUser("Alice");
            service.processRequest();
            UserContext.clear(); // Always clean up
        }, "Thread-A").start();
        
        // Thread 2 with user "Bob"
        new Thread(() -> {
            UserContext.setUser("Bob");
            service.processRequest();
            UserContext.clear();
        }, "Thread-B").start();
    }
}
```

**Use cases for ThreadLocal** :
- User sessions in web applications
- Transaction contexts
- Request-scoped data
- Avoiding parameter passing through deep call chains

---

## 1️⃣1️⃣ Executor Framework and Thread Pools

Creating new threads manually for every task is inefficient. The **Executor Framework** provides thread pooling for better performance .

### 1.1.1. ExecutorService – Basic Usage

```java
import java.util.concurrent.*;

public class ExecutorServiceDemo {
    public static void main(String[] args) {
        // Create a fixed thread pool with 4 threads
        ExecutorService executor = Executors.newFixedThreadPool(4);
        
        // Submit tasks
        for (int i = 0; i < 10; i++) {
            int taskId = i;
            executor.submit(() -> {
                System.out.println(Thread.currentThread().getName() + 
                                  " executing task " + taskId);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
        }
        
        // Shutdown properly
        executor.shutdown(); // No new tasks accepted
        try {
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow(); // Force shutdown
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
    }
}
```

### Types of Thread Pools

| Pool Type | Description | Use Case |
|-----------|-------------|----------|
| **`newFixedThreadPool(n)`** | Fixed number of threads | Consistent workload  |
| **`newCachedThreadPool()`** | Creates threads as needed, reuses idle ones | Bursty workloads  |
| **`newSingleThreadExecutor()`** | Single thread executing tasks sequentially | Guarantee order |
| **`newScheduledThreadPool(n)`** | Scheduled/delayed task execution | Timers, periodic tasks  |

### Callable and Future – Getting Results

Unlike `Runnable`, `Callable` can return results and throw exceptions .

```java
public class CallableDemo {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        List<Future<Integer>> futures = new ArrayList<>();
        
        // Submit multiple tasks
        for (int i = 0; i < 5; i++) {
            int taskId = i;
            Callable<Integer> task = () -> {
                System.out.println("Computing for task " + taskId);
                Thread.sleep(1000);
                return taskId * taskId;
            };
            
            futures.add(executor.submit(task));
        }
        
        // Collect results
        for (Future<Integer> future : futures) {
            try {
                // get() blocks until result is available
                Integer result = future.get(2, TimeUnit.SECONDS);
                System.out.println("Result: " + result);
            } catch (TimeoutException e) {
                System.out.println("Task timed out");
                future.cancel(true);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        
        executor.shutdown();
    }
}
```

### CompletableFuture – Asynchronous Programming (Java 8+)

`CompletableFuture` enables functional-style composition of asynchronous tasks .

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureDemo {
    public static void main(String[] args) {
        // Run async task
        CompletableFuture.supplyAsync(() -> {
            // Simulate API call
            sleep(1000);
            return "User data";
        })
        .thenApply(data -> {
            // Transform result
            return "Processed: " + data;
        })
        .thenAccept(result -> {
            // Consume final result
            System.out.println(result);
        })
        .exceptionally(throwable -> {
            // Handle errors
            System.err.println("Error: " + throwable);
            return null;
        });
        
        // Combine multiple futures
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");
        
        CompletableFuture<String> combined = future1.thenCombine(future2, 
            (s1, s2) -> s1 + " " + s2);
        
        System.out.println(combined.join()); // Hello World
        
        // Wait a bit for async tasks
        sleep(2000);
    }
    
    private static void sleep(int ms) {
        try { Thread.sleep(ms); } catch (InterruptedException e) {}
    }
}
```

---

## 1️⃣2️⃣ Concurrent Collections

The `java.util.concurrent` package provides thread-safe collections optimized for concurrent access .

| Collection | Description |
|------------|-------------|
| **`ConcurrentHashMap`** | Thread-safe HashMap with high concurrency |
| **`CopyOnWriteArrayList`** | Thread-safe List; creates copy on modification (read-heavy) |
| **`BlockingQueue`** | Queue that blocks when empty/full (producer-consumer) |
| **`ConcurrentLinkedQueue`** | Lock-free, thread-safe Queue |
| **`ConcurrentSkipListMap`** | Thread-safe, sorted Map (concurrent TreeMap) |

### ConcurrentHashMap Example

```java
import java.util.concurrent.ConcurrentHashMap;

public class ConcurrentHashMapDemo {
    public static void main(String[] args) throws InterruptedException {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        
        // Thread-safe operations
        map.put("key1", 100);
        map.putIfAbsent("key1", 200); // Won't replace
        
        // Atomic operations
        map.compute("key1", (k, v) -> v == null ? 1 : v + 1);
        
        // Parallel processing
        map.forEach(2, (k, v) -> 
            System.out.println(Thread.currentThread().getName() + 
                              ": " + k + " -> " + v));
    }
}
```

### BlockingQueue for Producer-Consumer

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

public class BlockingQueueDemo {
    public static void main(String[] args) {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5);
        
        // Producer
        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    queue.put(i); // Blocks if queue full
                    System.out.println("Produced: " + i);
                    Thread.sleep(500);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        // Consumer
        Thread consumer = new Thread(() -> {
            try {
                while (true) {
                    Integer value = queue.take(); // Blocks if queue empty
                    System.out.println("Consumed: " + value);
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

---

## 1️⃣3️⃣ Deadlocks and Prevention

### What is a Deadlock?

A **deadlock** occurs when two or more threads are blocked forever, each waiting for resources held by the other .

### Deadlock Example

```java
class DeadlockExample {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized (lock1) {
            System.out.println("Thread1: Holding lock1...");
            try { Thread.sleep(100); } catch (InterruptedException e) {}
            
            synchronized (lock2) {
                System.out.println("Thread1: Holding lock1 and lock2...");
            }
        }
    }
    
    public void method2() {
        synchronized (lock2) {
            System.out.println("Thread2: Holding lock2...");
            try { Thread.sleep(100); } catch (InterruptedException e) {}
            
            synchronized (lock1) {
                System.out.println("Thread2: Holding lock1 and lock2...");
            }
        }
    }
    
    public static void main(String[] args) {
        DeadlockExample deadlock = new DeadlockExample();
        
        new Thread(() -> deadlock.method1()).start();
        new Thread(() -> deadlock.method2()).start();
    }
}
```

### How to Detect Deadlocks

```java
// Using ThreadMXBean to detect deadlocks
import java.lang.management.ThreadMXBean;
import java.lang.management.ManagementFactory;

public class DeadlockDetector {
    public static void checkDeadlocks() {
        ThreadMXBean bean = ManagementFactory.getThreadMXBean();
        long[] threadIds = bean.findDeadlockedThreads();
        
        if (threadIds != null) {
            System.out.println("Deadlock detected! Threads involved:");
            for (long id : threadIds) {
                System.out.println("  Thread ID: " + id);
            }
        } else {
            System.out.println("No deadlocks detected");
        }
    }
}
```

### Deadlock Prevention Strategies 

| Strategy | Description | Example |
|----------|-------------|---------|
| **Lock Ordering** | Always acquire locks in a consistent order | Always lock lock1 then lock2 |
| **Timeouts** | Use `tryLock()` with timeout instead of indefinite blocking | `lock.tryLock(1, TimeUnit.SECONDS)` |
| **Avoid Nested Locks** | Minimize holding multiple locks simultaneously | Redesign to use single lock |
| **Use Higher-Level Concurrency** | Prefer `ConcurrentHashMap`, `AtomicInteger` over manual locks | Let the library manage concurrency |

```java
// Deadlock-free using tryLock
public boolean safeTransfer(Account from, Account to, int amount) {
    long timeout = 100; // milliseconds
    try {
        if (from.lock.tryLock(timeout, TimeUnit.MILLISECONDS)) {
            try {
                if (to.lock.tryLock(timeout, TimeUnit.MILLISECONDS)) {
                    try {
                        from.withdraw(amount);
                        to.deposit(amount);
                        return true;
                    } finally {
                        to.lock.unlock();
                    }
                }
            } finally {
                from.lock.unlock();
            }
        }
        return false; // Couldn't acquire both locks
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
        return false;
    }
}
```

---

## 1️⃣4️⃣ Fork/Join Framework

The **Fork/Join Framework** (Java 7+) is designed for divide-and-conquer parallel processing .

```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

class SumTask extends RecursiveTask<Long> {
    private static final int THRESHOLD = 1000;
    private final int[] array;
    private final int start;
    private final int end;
    
    public SumTask(int[] array, int start, int end) {
        this.array = array;
        this.start = start;
        this.end = end;
    }
    
    @Override
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // Compute directly
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += array[i];
            }
            return sum;
        } else {
            // Split task
            int mid = start + (end - start) / 2;
            SumTask leftTask = new SumTask(array, start, mid);
            SumTask rightTask = new SumTask(array, mid, end);
            
            leftTask.fork(); // Asynchronously execute
            long rightResult = rightTask.compute(); // Compute right
            long leftResult = leftTask.join(); // Wait for left
            
            return leftResult + rightResult;
        }
    }
}

public class ForkJoinDemo {
    public static void main(String[] args) {
        int[] array = new int[10_000];
        for (int i = 0; i < array.length; i++) {
            array[i] = i;
        }
        
        ForkJoinPool pool = new ForkJoinPool();
        SumTask task = new SumTask(array, 0, array.length);
        long result = pool.invoke(task);
        
        System.out.println("Sum: " + result);
        pool.shutdown();
    }
}
```

**When to use Fork/Join** :
- Computationally intensive tasks
- Recursive algorithms (sorting, searching, matrix multiplication)
- Problems that can be naturally divided into subtasks

---

## 1️⃣5️⃣ Best Practices and Performance Optimization

### DO's ✅

1. **Prefer higher-level concurrency utilities** 
   ```java
   // Good
   ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
   ExecutorService executor = Executors.newFixedThreadPool(10);
   
   // Avoid
   HashMap<String, Integer> map = new HashMap<>(); // Need manual sync
   new Thread(() -> {}).start(); // Manual thread per task
   ```

2. **Use immutable objects when possible** 
   ```java
   public final class ImmutableData {
       private final String value;
       public ImmutableData(String value) { this.value = value; }
       public String getValue() { return value; }
   }
   ```

3. **Minimize lock duration** 
   ```java
   // Good - lock only critical section
   public void update(String key, String value) {
       // Non-critical work outside lock
       validateInput(key, value);
       
       synchronized(lock) {
           sharedData.put(key, value);
       }
   }
   ```

4. **Use thread pools instead of creating threads** 
   ```java
   // Good
   ExecutorService executor = Executors.newFixedThreadPool(10);
   executor.submit(task);
   
   // Avoid
   new Thread(task).start(); // Creates new thread for every task
   ```

5. **Handle InterruptedException properly** 
   ```java
   try {
       Thread.sleep(1000);
   } catch (InterruptedException e) {
       // Restore interrupted status
       Thread.currentThread().interrupt();
       // Clean up and exit
       return;
   }
   ```

### ❌ Don'ts

1. **Don't synchronize on non-final fields**
   ```java
   // Bad
   public class Bad {
       private Object lock = new Object();
       public void method() {
           synchronized(lock) { // lock can be reassigned!
               // ...
           }
       }
   }
   ```

2. **Don't call `wait()` outside synchronized block** 
   ```java
   // Bad
   public void badWait() {
       try {
           wait(); // IllegalMonitorStateException!
       } catch (InterruptedException e) {}
   }
   ```

3. **Don't use double-checked locking without volatile** (for singletons)
   ```java
   // Broken without volatile
   private static Singleton instance;
   public static Singleton getInstance() {
       if (instance == null) {
           synchronized(Singleton.class) {
               if (instance == null) {
                   instance = new Singleton(); // May be partially constructed!
               }
           }
       }
       return instance;
   }
   ```

4. **Don't ignore thread interruptions**
   ```java
   // Bad
   try {
       Thread.sleep(1000);
   } catch (InterruptedException e) {
       // Do nothing - thread can't be interrupted now
   }
   ```

5. **Don't create too many threads** 
   ```java
   // Bad
   for (int i = 0; i < 10000; i++) {
       new Thread(() -> process()).start(); // Resource exhaustion
   }
   
   // Good
   ExecutorService executor = Executors.newFixedThreadPool(100);
   for (int i = 0; i < 10000; i++) {
       executor.submit(() -> process());
   }
   ```

### Performance Tuning Tips 

| Tip | Description |
|-----|-------------|
| **Right-size thread pools** | Number of threads = CPU cores × (1 + wait time / service time) |
| **Avoid false sharing** | Pad fields or use `@Contended` annotation |
| **Use striped locks** | `Striped` class for fine-grained locking |
| **Monitor with tools** | JVisualVM, JConsole, Java Mission Control  |
| **Test under load** | Concurrency bugs often appear only under high contention |

---

## 1️⃣6️⃣ Common Interview Questions

### Q1: What is the difference between `synchronized` method and `synchronized` block? 

**Answer:** 
- `synchronized` method locks the entire method, preventing any other thread from accessing it
- `synchronized` block locks only a specific section, allowing better performance and finer control
- Block synchronization is more efficient as it reduces the locked code region

### Q2: What are `volatile` and `transient` keywords? 

**Answer:**
- **`volatile`**: Ensures visibility of variable updates across threads; reads/writes go directly to main memory
- **`transient`**: Excludes a variable from serialization; used when fields shouldn't be persisted

### Q3: Difference between `ExecutorService` and `ForkJoinPool` 

**Answer:**
- **`ExecutorService`**: Manages thread pools for general parallel tasks
- **`ForkJoinPool`**: Optimized for recursive, divide-and-conquer tasks with work-stealing algorithm

### Q4: What is `ThreadLocal` and when to use it? 

**Answer:** `ThreadLocal` provides thread-specific storage – each thread has its own copy of the variable. Used for:
- User sessions in web apps
- Transaction contexts
- Request-scoped data
- Avoiding parameter passing through deep call chains

### Q5: How do you avoid deadlocks? 

**Answer:**
- Use consistent lock ordering
- Prefer `tryLock()` with timeout over indefinite locking
- Minimize nested locks
- Use higher-level concurrency utilities instead of manual synchronization
- Monitor for deadlocks using ThreadMXBean

---

## 1️⃣7️⃣ Summary Table

| Concept | Key Points | When to Use |
|---------|------------|-------------|
| **Thread** | Basic unit, created via `Thread` class or `Runnable` | Simple parallel tasks |
| **Synchronized** | Mutual exclusion, intrinsic locks | Protecting shared state |
| **`volatile`** | Visibility guarantee, no atomicity | Simple flags, single-writer |
| **Locks (`ReentrantLock`)** | Advanced features: timeout, interruptible | Complex locking scenarios |
| **Atomic Variables** | CAS-based, non-blocking | Counters, accumulators |
| **ThreadLocal** | Thread-local storage | Per-thread context |
| **ExecutorService** | Thread pools, task management | General concurrent processing |
| **Fork/Join** | Work-stealing, divide-and-conquer | Recursive parallel algorithms |
| **Concurrent Collections** | Thread-safe data structures | Shared collections |
| **CompletableFuture** | Async composition | Complex async workflows |

---

## 🎯 Key Takeaways

1. **Multithreading** enables concurrent execution for better performance and responsiveness .

2. **Thread safety** is critical – protect shared data using synchronization, locks, or atomic variables .

3. **Prefer high-level concurrency utilities** (`ExecutorService`, `ConcurrentHashMap`, `AtomicInteger`) over low-level thread management .

4. **Design for immutability** – immutable objects are inherently thread-safe .

5. **Understand the Java Memory Model** – `volatile`, `synchronized`, and `final` have specific visibility guarantees .

6. **Avoid deadlocks** through careful lock ordering and timeouts .

7. **Measure and tune** – profile your application to identify bottlenecks .

Mastering multithreading is essential for building high-performance, scalable Java applications that leverage modern multi-core processors. The journey from basic thread creation to advanced concurrency patterns transforms you from a coder into a true software architect .
