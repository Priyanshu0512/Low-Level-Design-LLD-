In software development, we often require classes that can only have **one object**

>Singleton Pattern is a creational design pattern that guarantees a class has only one instance and provides a global point of access to it.

- It involves only one class which is responsible for instantiating itself, making sure it creates not more than one instance.
- To implement the singleton pattern, we must prevent external objects from creating instances of the singleton class. Only the singleton class should be permitted to create its own objects.

Additionally, we need to provide a method for external objects to access the singleton object.

![[Screenshot 2025-08-18 at 4.49.09 PM.png]]

Key Points - 
- The `instance` class variable holds the one and only instance of the Singleton class.
- The `Singleton()` constructor is declared as private, preventing external objects from creating new instances.
- The `getInstance()` method is a static class method, making it accessible to the external world.

---

## Implementation


### 1. Lazy Implementation

```java

class LazySingleton {
    // The single instance, initially null
    private static LazySingleton instance;

    // Private constructor to prevent instantiation
    private LazySingleton() {}

    // Public method to get the instance
    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }
        return instance;
    }
}

```

> This implementation is not thread-safe. If multiple threads call `getInstance()` simultaneously when `instance` is null, it's possible to create multiple instances.

---

### 2.Thread-Safe Singleton

This approach is similar to lazy initialization but also ensures that the singleton is **thread-safe**.
This is achieved by making the `getInstance()` method **synchronized** ensuring only one thread can execute this method at a time. **When a thread enters the synchronized method, it acquires a lock on the class object. Other threads must wait until the method is executed.**

```java
class ThreadSafeSingleton {
    private static ThreadSafeSingleton instance;

    private ThreadSafeSingleton() {}


    public static synchronized ThreadSafeSingleton getInstance() {
        if (instance == null) {
            instance = new ThreadSafeSingleton();
        }

        return instance;
    }
}
```

- The **synchronization** keyword ensures that only one thread can perform the (`instance == null)` check and create the object.

>Although this approach is straightforward, using `synchronized` can cause substantial overhead and reduce performance, which can be a bottleneck if called frequently.

---

### 3. Double-Checked Locking 

This is achieved by making the `getInstance()` method **synchronized** ensuring only one thread can execute this method at a time.
When a thread enters the synchronized method, it acquires a lock on the class object. Other threads must wait until the method is executed.

>Role of `volatile`- It prevents instruction reordering during object creation.  
   Without it, another thread might see a partially constructed `DoubleCheckedSingleton`.

```java
class DoubleCheckedSingleton {
    // The single instance, initially null, marked as volatile
    private static volatile DoubleCheckedSingleton instance;

    private DoubleCheckedSingleton() {}


    public static DoubleCheckedSingleton getInstance() {
        // First check (not synchronized)
        if (instance == null) {
            // Synchronize on the class object
            synchronized (DoubleCheckedSingleton.class) {
                // Second check (synchronized)
                if (instance == null) {
                    instance = new DoubleCheckedSingleton();
                }
            }
        }
        // Return the instance (either newly created or existing)
        return instance;
    }
}```

#### Why **two checks** are needed

1. **First check (outside synchronized)**
    `if (instance == null) { ... }`
    - This is for **performance**.
    - If the instance is already created, we avoid entering the synchronized block (which is expensive).
2. **Second check (inside synchronized)**
```java
    synchronized (DoubleCheckedSingleton.class) {     
	    if (instance == null) {         
		    instance = new DoubleCheckedSingleton();
		} 
	}```
 -  Without this **second check**, multiple threads could still create multiple instances.
    - Example scenario:
        - Thread A passes the first check (`instance == null`).
        - Thread B also passes the first check before A enters synchronized.
        - Both enter the synchronized block one after another.
        - If we don’t check again inside, both would construct new objects → breaking singleton.

---

### 4. Enum Singleton

In this method, the singleton is declared as an enum rather than a class.
Java ensures that only one instance of an enum value is created, even in a multithreaded environment.
The Enum Singleton pattern is the most robust and concise way to implement a singleton in Java.

```java
enum EnumSingleton {
    INSTANCE;

    // Public method
    public void doSomething() {
        // Add any singleton logic here
    }
}```


---

### 5. Eager Initialization

In this method, we rely on the JVM to create the singleton instance when the class is loaded. The JVM guarantees that the instance will be created before any thread access the instance variable.

```java
class EagerSingleton {
    // The single instance, created immediately
    private static final EagerSingleton instance = new EagerSingleton();

    private EagerSingleton() {}

    public static EagerSingleton getInstance() {
        return instance;
    }
}
```

- `static` variable ensures there's only one instance shared across all instances of the class.
- `final` prevents the instance from being reassigned after initialization.

>While it is inherently thread-safe, it could potentially waste resources if the singleton instance is never used by the client application.

---

### Examples

- `Logger Classes`: Many logging frameworks use the Singleton pattern to provide a global logging object. This ensures that log messages are consistently handled and written to the same output stream.
- `Database Connection Pools`: Connection pools help manage and reuse database connections efficiently. A Singleton can ensure that only one pool is created and used throughout the application.
- `Cache Objects`: In-memory caches are often implemented as Singletons to provide a single point of access for cached data across the application.
- `Thread Pools:` Thread pools manage a collection of worker threads. A Singleton ensures that the same pool is used throughout the application, preventing resource overuse.
- `File System:` File systems often use Singleton objects to represent the file system and provide a unified interface for file operations.
- `Print Spooler:` In operating systems, print spoolers manage printing tasks. A single instance coordinates all print jobs to prevent conflicts.

---

