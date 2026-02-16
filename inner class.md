# 📚 Complete Guide to Inner Classes in Java

## 🧩 What are Inner Classes?

Java allows defining a class **inside another class** - these are called **Inner Classes** (or Nested Classes). They are useful for **logical grouping** and **encapsulating closely related logic**.

### 🎯 Why Use Inner Classes?

| Benefit | Explanation |
|---------|-------------|
| **Logical Grouping** | Group classes that belong together |
| **Encapsulation** | Hide implementation details |
| **Readability** | Keep related code close together |
| **Maintainability** | Easier to understand relationships |

---

## 📋 Types of Inner Classes

| Type | Keyword | Tied to Instance? | Scope |
|------|---------|-------------------|-------|
| **Member Inner Class** | (none) | ✅ Yes | Class-wide |
| **Static Nested Class** | `static` | ❌ No | Class-wide |
| **Local Inner Class** | (none) | ✅ Yes | Method-local |
| **Anonymous Inner Class** | (none) | ✅ Yes | One-time use |

---

## ✅ 1. Member Inner Class

A **non-static** class declared inside another class, at the member level. It's **tied to an instance** of the outer class.

### 📌 Key Characteristics:
- Can access **all** outer class members (even `private`)
- Requires an **outer class object** to be instantiated
- Has access to `this` of both outer and inner class

### 🚗 Real-World Example: Car and Engine

```java
class Car {
    // Outer class members
    private String model;
    private String color;
    private boolean isEngineOn = false;
    private static int totalCars = 0;  // static member
    
    public Car(String model, String color) {
        this.model = model;
        this.color = color;
        totalCars++;
        System.out.println("New " + color + " " + model + " created");
    }
    
    // Member Inner Class
    class Engine {
        private String engineType;
        private int horsepower;
        
        public Engine(String engineType, int horsepower) {
            this.engineType = engineType;
            this.horsepower = horsepower;
        }
        
        // Inner class can access all outer class members
        public void start() {
            if (!isEngineOn) {
                isEngineOn = true;
                System.out.println(model + "'s " + engineType + " engine started!");
                System.out.println("   Color: " + color);
                System.out.println("   Horsepower: " + horsepower);
            } else {
                System.out.println("Engine already running!");
            }
        }
        
        public void stop() {
            if (isEngineOn) {
                isEngineOn = false;
                System.out.println(model + "'s engine stopped");
            }
        }
        
        // Inner class can access outer class's 'this'
        public void displayCarInfo() {
            System.out.println("This engine belongs to: " + Car.this.model);
            // Car.this refers to outer class object
        }
    }
    
    // Method to get Engine (demonstrating inner class usage)
    public Engine getEngine(String type, int hp) {
        return new Engine(type, hp);
    }
    
    public void displayStats() {
        System.out.println("Total cars manufactured: " + totalCars);
    }
}

public class MemberInnerClassDemo {
    public static void main(String[] args) {
        // Create outer class object first
        Car car1 = new Car("Tesla Model S", "Red");
        
        // Create inner class object using outer instance
        Car.Engine engine1 = car1.new Engine("Electric", 670);
        engine1.start();
        
        // Alternative: create via outer class method
        Car car2 = new Car("Ford Mustang", "Blue");
        Car.Engine engine2 = car2.getEngine("V8", 450);
        engine2.start();
        
        // Demonstrate outer class reference
        engine1.displayCarInfo();
        engine2.displayCarInfo();
        
        // Stop engines
        engine1.stop();
        engine2.stop();
        
        car1.displayStats();
    }
}
```

**✅ Output:**
```
New Red Tesla Model S created
Tesla Model S's Electric engine started!
   Color: Red
   Horsepower: 670
New Blue Ford Mustang created
Ford Mustang's V8 engine started!
   Color: Blue
   Horsepower: 450
This engine belongs to: Tesla Model S
This engine belongs to: Ford Mustang
Tesla Model S's engine stopped
Ford Mustang's engine stopped
Total cars manufactured: 2
```

### 💡 Important Syntax:
```java
// Correct way to instantiate member inner class
OuterClass outer = new OuterClass();
OuterClass.InnerClass inner = outer.new InnerClass();

// Or via method
OuterClass.InnerClass inner = outer.getInnerInstance();
```

---

## ✅ 2. Static Nested Class

A **static** class declared inside another class. It's associated with the **outer class itself**, not with instances.

### 📌 Key Characteristics:
- Can access **only static members** of outer class
- **Doesn't require** outer class instance
- Behaves like a top-level class but nested for packaging

### 💻 Real-World Example: Computer and USB

```java
class Computer {
    private String brand;
    private String model;
    private static String manufacturer = "TechCorp";
    private static int totalComputers = 0;
    
    public Computer(String brand, String model) {
        this.brand = brand;
        this.model = model;
        totalComputers++;
    }
    
    // Static Nested Class
    static class USB {
        private String version;
        private String type;  // Type-A, Type-C, etc.
        private static int totalPorts = 0;  // static inside static nested
        
        public USB(String version, String type) {
            this.version = version;
            this.type = type;
            totalPorts++;
        }
        
        public void connect() {
            // Can access static members of outer class
            System.out.println("USB " + version + " (" + type + ") connected");
            System.out.println("   Manufacturer: " + manufacturer);  // ✅ static access
            
            // ❌ Cannot access non-static outer members
            // System.out.println(brand);  // ERROR!
        }
        
        public void displayInfo() {
            System.out.println("USB Details:");
            System.out.println("   Version: " + version);
            System.out.println("   Type: " + type);
            System.out.println("   Total USB ports: " + totalPorts);
        }
        
        // Static method in static nested class
        public static void showSpecs() {
            System.out.println("USB Specification v3.0");
            System.out.println("Manufacturer: " + manufacturer);  // Access outer static
        }
    }
    
    // Nested enum (another type of nested class)
    enum ComputerType {
        DESKTOP, LAPTOP, TABLET
    }
    
    public void displayInfo() {
        System.out.println("Computer: " + brand + " " + model);
        System.out.println("Total computers: " + totalComputers);
    }
}

public class StaticNestedClassDemo {
    public static void main(String[] args) {
        // Create static nested class WITHOUT outer instance!
        Computer.USB usb1 = new Computer.USB("3.2", "Type-C");
        usb1.connect();
        usb1.displayInfo();
        
        System.out.println();
        
        // Another USB port
        Computer.USB usb2 = new Computer.USB("2.0", "Type-A");
        usb2.connect();
        usb2.displayInfo();
        
        System.out.println();
        
        // Call static method of static nested class
        Computer.USB.showSpecs();
        
        System.out.println();
        
        // Create outer class objects
        Computer comp1 = new Computer("Dell", "XPS 15");
        Computer comp2 = new Computer("Apple", "MacBook Pro");
        
        comp1.displayInfo();
        comp2.displayInfo();
        
        // Using nested enum
        Computer.ComputerType type = Computer.ComputerType.LAPTOP;
        System.out.println("Computer type: " + type);
    }
}
```

**✅ Output:**
```
USB 3.2 (Type-C) connected
   Manufacturer: TechCorp
USB Details:
   Version: 3.2
   Type: Type-C
   Total USB ports: 1

USB 2.0 (Type-A) connected
   Manufacturer: TechCorp
USB Details:
   Version: 2.0
   Type: Type-A
   Total USB ports: 2

USB Specification v3.0
Manufacturer: TechCorp

Computer: Dell XPS 15
Total computers: 2
Computer: Apple MacBook Pro
Total computers: 2
Computer type: LAPTOP
```

### 📊 Member Inner vs Static Nested

| Feature | Member Inner Class | Static Nested Class |
|---------|-------------------|---------------------|
| **Outer instance needed?** | ✅ Yes | ❌ No |
| **Can access outer instance members?** | ✅ Yes | ❌ No (only static) |
| **Can have static members?** | ❌ No | ✅ Yes |
| **Memory** | Holds reference to outer | No outer reference |
| **Use case** | When class needs outer context | Utility/helper classes |

---

## ✅ 3. Local Inner Class

A class defined **inside a method or block**. It exists only within that method/block.

### 📌 Key Characteristics:
- Scope is limited to the enclosing **method/block**
- Can access **final or effectively final** local variables
- Cannot have static members
- Best for method-specific helper logic

### 🏨 Real-World Example: Hotel Reservation System

```java
class Hotel {
    private String hotelName;
    private int totalRooms;
    
    public Hotel(String hotelName, int totalRooms) {
        this.hotelName = hotelName;
        this.totalRooms = totalRooms;
    }
    
    public void reserveRoom(String guestName, int nights, String roomType) {
        // Local variables - must be final or effectively final
        int roomNumber = (int)(Math.random() * 1000);  // effectively final
        double basePrice = 200.0;  // effectively final
        
        // Local Inner Class (defined INSIDE method)
        class Reservation {
            private String reservationId;
            private double totalPrice;
            
            public Reservation() {
                this.reservationId = "RES" + System.currentTimeMillis();
                
                // Calculate price based on room type
                double multiplier;
                switch(roomType.toLowerCase()) {
                    case "suite": multiplier = 2.5; break;
                    case "deluxe": multiplier = 1.8; break;
                    default: multiplier = 1.0;
                }
                
                this.totalPrice = basePrice * nights * multiplier;
            }
            
            public void confirm() {
                // Accessing method parameters (effectively final)
                System.out.println("\n=== RESERVATION CONFIRMATION ===");
                System.out.println("Hotel: " + hotelName);  // Outer class field
                System.out.println("Guest: " + guestName);  // Method parameter
                System.out.println("Room: " + roomNumber);  // Local variable
                System.out.println("Type: " + roomType);    // Method parameter
                System.out.println("Nights: " + nights);    // Method parameter
                System.out.println("Total: $" + totalPrice);
                System.out.println("ID: " + reservationId);
                System.out.println("================================");
            }
            
            public void printReceipt() {
                System.out.println("Receipt for " + reservationId);
                System.out.println("Amount: $" + totalPrice);
            }
        }
        
        // Create and use the local inner class
        Reservation reservation = new Reservation();
        reservation.confirm();
        
        // Can create multiple instances within the method
        if (totalRooms > 0) {
            totalRooms--;
            System.out.println("Remaining rooms: " + totalRooms);
        }
    }
    
    // Multiple local inner classes in different methods
    public void cancelReservation(String reservationId) {
        // Another local inner class
        class Cancellation {
            private String reason;
            
            Cancellation(String reason) {
                this.reason = reason;
            }
            
            void process() {
                System.out.println("Cancelling reservation: " + reservationId);
                System.out.println("Reason: " + reason);
                totalRooms++;  // Can access outer class fields
                System.out.println("Room released. Total rooms: " + totalRooms);
            }
        }
        
        Cancellation cancel = new Cancellation("Guest request");
        cancel.process();
    }
}

public class LocalInnerClassDemo {
    public static void main(String[] args) {
        Hotel hotel = new Hotel("Grand Plaza", 50);
        
        // First reservation
        hotel.reserveRoom("John Doe", 3, "suite");
        
        System.out.println("\n" + "=".repeat(50) + "\n");
        
        // Second reservation
        hotel.reserveRoom("Jane Smith", 2, "standard");
        
        System.out.println("\n" + "=".repeat(50) + "\n");
        
        // Cancel a reservation
        hotel.cancelReservation("RES123456");
    }
}
```

### 🔑 "Effectively Final" Explained

```java
class EffectivelyFinalDemo {
    public void demonstrate() {
        int x = 10;      // Effectively final (never changed)
        int y = 20;      // Not effectively final (changed below)
        y = 30;          // Now y is NOT effectively final
        
        class Local {
            void print() {
                System.out.println(x);  // ✅ OK - x is effectively final
                // System.out.println(y);  // ❌ ERROR - y is not effectively final
            }
        }
        
        // y = 40;  // Even trying to change after class definition causes error
    }
}
```

---

## ✅ 4. Anonymous Inner Class

A class **without a name** used for **one-time implementation**. It's declared and instantiated in a single expression.

### 📌 Key Characteristics:
- No name - declared and instantiated together
- Used to implement interfaces or extend classes on the spot
- Cannot have explicit constructors
- Perfect for event handlers, callbacks, and one-off implementations

### 🎮 Real-World Example: Game Character System

```java
// Interface for game characters
interface Character {
    void attack();
    void defend();
    void useSpecialAbility();
}

// Abstract class for equipment
abstract class Weapon {
    protected String name;
    protected int damage;
    
    public Weapon(String name, int damage) {
        this.name = name;
        this.damage = damage;
    }
    
    public abstract void use();
    
    public void displayInfo() {
        System.out.println("Weapon: " + name + " (Damage: " + damage + ")");
    }
}

public class AnonymousInnerClassDemo {
    public static void main(String[] args) {
        
        // 1️⃣ Anonymous class implementing an interface
        Character hero = new Character() {
            private String name = "Shadow Knight";
            private int health = 100;
            
            @Override
            public void attack() {
                System.out.println(name + " performs a powerful slash!");
                System.out.println("   Damage dealt: 50");
            }
            
            @Override
            public void defend() {
                System.out.println(name + " raises shield, defense +20");
                health += 10;
            }
            
            @Override
            public void useSpecialAbility() {
                System.out.println(name + " uses 'Dark Slash' - CRITICAL HIT!");
                System.out.println("   Damage dealt: 150");
            }
            
            // Additional method (can only be called within this scope)
            public void checkHealth() {
                System.out.println(name + " health: " + health);
            }
        };
        
        // 2️⃣ Another anonymous class implementing same interface differently
        Character mage = new Character() {
            private String name = "Arcane Wizard";
            private int mana = 200;
            
            @Override
            public void attack() {
                System.out.println(name + " casts a fireball!");
                mana -= 20;
            }
            
            @Override
            public void defend() {
                System.out.println(name + " creates magical barrier, mana -10");
                mana -= 10;
            }
            
            @Override
            public void useSpecialAbility() {
                System.out.println(name + " summons meteor swarm!");
                System.out.println("   Mana remaining: " + mana);
            }
        };
        
        // 3️⃣ Anonymous class extending an abstract class
        Weapon sword = new Weapon("Dragon Slayer", 75) {
            @Override
            public void use() {
                System.out.println("⚔️ Swinging " + name + "!");
                System.out.println("   Deals " + damage + " slashing damage");
            }
            
            // Additional method
            public void sharpen() {
                System.out.println("Sharpening " + name + " - damage +10");
                // Can't modify damage as it's final? No, it's not final in parent
                // But we can't access parent fields directly to modify? Actually we can
            }
        };
        
        Weapon bow = new Weapon("Elven Bow", 45) {
            @Override
            public void use() {
                System.out.println("🏹 Firing " + name + "!");
                System.out.println("   Deals " + damage + " piercing damage");
                System.out.println("   Long range attack!");
            }
        };
        
        // 4️⃣ Anonymous class for event handling (common in GUI)
        Runnable gameLoop = new Runnable() {
            private int frame = 0;
            
            @Override
            public void run() {
                frame++;
                System.out.println("Game frame #" + frame + " rendered");
                if (frame >= 3) {
                    System.out.println("Game loop complete!");
                }
            }
        };
        
        // 5️⃣ Anonymous class with initialization block
        Character boss = new Character() {
            private String name;
            private int health;
            
            // Initialization block (acts like constructor)
            {
                name = "Dragon Lord";
                health = 1000;
                System.out.println("⚡ BOSS SPAWNED: " + name);
            }
            
            @Override
            public void attack() {
                System.out.println(name + " breathes fire!");
                health -= 10;  // Boss gets tired
            }
            
            @Override
            public void defend() {
                System.out.println(name + " scales harden, defense up");
            }
            
            @Override
            public void useSpecialAbility() {
                System.out.println(name + " uses 'Inferno' - MASSIVE DAMAGE!");
                System.out.println("   Current health: " + health);
            }
        };
        
        // Test all anonymous classes
        System.out.println("=== GAME CHARACTERS ===\n");
        
        System.out.println("--- Hero Actions ---");
        hero.attack();
        hero.defend();
        hero.useSpecialAbility();
        
        System.out.println("\n--- Mage Actions ---");
        mage.attack();
        mage.defend();
        mage.useSpecialAbility();
        
        System.out.println("\n--- Weapon Testing ---");
        sword.use();
        bow.use();
        
        System.out.println("\n--- Game Loop ---");
        gameLoop.run();
        gameLoop.run();
        gameLoop.run();
        
        System.out.println("\n--- Boss Battle ---");
        boss.attack();
        boss.defend();
        boss.useSpecialAbility();
        
        // 6️⃣ Anonymous class as method argument (common pattern)
        System.out.println("\n=== ANONYMOUS CLASS AS ARGUMENT ===");
        performAction(new Character() {
            @Override
            public void attack() {
                System.out.println("Surprise attack from anonymous!");
            }
            
            @Override
            public void defend() {
                System.out.println("Quick dodge!");
            }
            
            @Override
            public void useSpecialAbility() {
                System.out.println("Stealth strike!");
            }
        });
    }
    
    public static void performAction(Character c) {
        System.out.println("Performing action sequence:");
        c.attack();
        c.defend();
        c.useSpecialAbility();
    }
}
```

### 📊 Anonymous Class vs Lambda (Java 8+)

```java
// Anonymous class (verbose)
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
};

// Lambda expression (concise) - for functional interfaces only
Runnable r2 = () -> System.out.println("Running");

// When to use anonymous class:
// - Implementing interface with multiple methods
// - Extending a class (not just interface)
// - Need to add fields or additional methods
// - More complex initialization needed
```

---

## 🔁 Comparison Table: When to Use What?

| Type | Scope | Tied to Instance? | Outer Access | Use Case |
|------|-------|-------------------|--------------|----------|
| **Member Inner** | Class-wide | ✅ Yes | All members | Object components (Engine in Car) |
| **Static Nested** | Class-wide | ❌ No | Static only | Helper/Builder classes |
| **Local Inner** | Method-local | ✅ Yes | All + locals | Method-specific helpers |
| **Anonymous** | One-time inline | ✅ Yes | All + finals | Callbacks, event handlers |

---

## 🎯 Practical Guidelines

### ✅ Use Member Inner Class When:
- The inner class is **strongly associated** with outer instance
- Need access to outer instance's private members
- Example: `ArrayList.Itr` (Iterator implementation)

### ✅ Use Static Nested Class When:
- The inner class doesn't need access to outer instance
- Utility/helper class related to outer class
- Example: `Integer.IntegerCache`, `Builder` pattern

### ✅ Use Local Inner Class When:
- Logic is only relevant within a single method
- Need to create multiple related objects within method
- Complex method-specific algorithm with helper class

### ✅ Use Anonymous Inner Class When:
- One-time implementation needed
- Implementing callbacks/event handlers
- Short, simple overrides
- Example: button click handlers in GUI

---

## 📝 Key Takeaways

1. **Inner classes** help organize code logically
2. **Member inner** classes have access to outer class members
3. **Static nested** classes don't need outer instance
4. **Local inner** classes are method-scoped helpers
5. **Anonymous** classes are one-time implementations
6. **All inner classes** (except static) can access outer members
7. **Local and anonymous** classes can only access final/effectively final variables

**Remember:** Inner classes are powerful but use them judiciously - they increase complexity and can make code harder to understand if overused!
