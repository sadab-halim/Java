# Multithreading and Concurrency

# Introduction
**Concurrency** is the ability of a program to execute multiple tasks seemingly at the same time. This can be achieved through multiprocessing (using multiple processes) or multithreading (using multiple threads within a single process).
---

# Processes vs Thread: The Core Distinction
## Processes
A **process** is an independent execution unit that has its own isolated memory space. Think of it as a separate, self-contained program running on your computer.

### Key Characteristics of a Process
- **Isolation**: Each process has its own dedicated memory address space, meaning it operates independently and remains isolated from other processes. One process cannot directly access the memory of another.
- **Resource Ownership**: A process owns its resources, including memory (code, data, heap, stack), file handles, network connections, etc.
- **Heavyweight**: Creating and managing processes is relatively "heavyweight" in terms of system resources (CPU, memory) and time.
- **Communication**: Processes communicate through Inter-Process Communication (IPC) mechanisms like pipes, sockets, shared memory, message queues, etc., which are typically slower and more complex than inter-thread communication.
- **Fault Tolerance**: If one process crashes, it generally does not affect other processes, due to their isolation.

## Threads
A **thread** is a lightweight execution unit within a process. Multiple threads can exist within the same process and share its resources.

### Key Characteristics of a Thread
- **Shared Memory**: Threads within the same process share the process's memory space, including the code segment, data segment, heap, and open file descriptors. This shared memory allows for efficient data exchange between threads.
- **Individual Stack and Program Counter**: Each thread has its own program counter (to keep track of the next instruction to execute), its own set of registers, and its own execution stack (for local variables and method call frames).
- **Lightweight**: Creating and managing threads is significantly "lighter" than processes, as they share many resources of their parent process.
- **Communication**: Threads communicate directly through shared memory, which is faster and easier than IPC.
- **Less Fault Tolerant**: If one thread crashes, it can potentially corrupt the shared memory of the entire process, leading to the crash of the whole application.
- **Context Switching**: Switching between threads (context switching) within the same process is much faster than switching between processes, as the CPU doesn't need to change memory address spaces.

### Analogy
Imagine a large company
- **Process**: The entire company building, with its own independent finances, departments, and operations, isolated from other companies.
- **Thread**: Individual employees working within that company. They share the company's resources (office space, computers, shared files) but each employee has their own desk (stack), to-do list (program counter), and personal belongings (registers).

---

# Java's Memory Model
The Java Memory Model (JMM) defines how threads interact with memory and how changes made by one thread become visible to other threads. This is crucial for understanding and writing correct concurrent programs in Java, as modern CPUs and compilers often reorder instructions for performance optimization.

## Main Memory and Working Memory
The JMM conceptualizes memory into two main areas:
- **Main Memory**: This represents the shared memory where all shared variables (instance fields, static fields, and array elements) reside.
- **Working Memory (Thread Local Cache)**: Each thread has its own working memory (often implemented as CPU caches or registers). Threads primarily operate on copies of shared variables in their working memory.

### The Problem
When a thread modifies a shared variable in its working memory, the changes are not immediately guaranteed to be visible to other threads. Another thread might be reading a stale copy of the variable from its own working memory. This can lead to data inconsistency and subtle, hard-to-debug bugs.

## Happens-Before Principle
The JMM introduces the "happens-before" principle to define a partial ordering of memory operations. If action A happens-before action B, then the effects of A are visible to B, and A must appear to B to occur before B. This is the cornerstone of ensuring correct synchronization.

Some key happens-before relationships:
- **Program Order Rule**: Within a single thread, each action happens-before every subsequent action in that thread's program order.
- **Monitor Lock Rule `(synchronized)`**: An unlock on a monitor (achieved by exiting a `synchronized` block or method) happens-before every subsequent lock on the same monitor. This ensures that changes made within a `synchronized` block by one thread are visible to another thread acquiring the same lock.
- **Volatile Variable Rule**: A write to a `volatile` variable happens-before every subsequent read of that same `volatile` variable. This guarantees visibility for `volatile` variables.
- **Thread Start Rule**: The `start()` of a thread happens-before any action in the started thread.
- **Thread Termination Rule**: All actions in a thread happen-before any other thread detects that thread has terminated (e.g., by calling `join()` or `isAlive()`).
- **Transitivity**: If A happens-before B, and B happens-before C, then A happens-before C.

---

# Threads in Java
Java provides built-in support for multithreading. Every Java application has at least one thread: the **main thread**, which executes the `main()` method.

## Creating Threads
There are two primary ways to create threads in Java:
### 1. Extending the `Thread` class
```java
// Beginner Example: Extending Thread class
class MyThread extends Thread {
    private String threadName;

    public MyThread(String name) {
        this.threadName = name;
        System.out.println("Creating " + threadName);
    }

    public void run() {
        System.out.println("Running " + threadName);
        try {
            for (int i = 4; i > 0; i--) {
                System.out.println("Thread: " + threadName + ", " + i);
                // Pause thread for a bit
                Thread.sleep(50);
            }
        } catch (InterruptedException e) {
            System.out.println("Thread " + threadName + " interrupted.");
        }
        System.out.println("Thread " + threadName + " exiting.");
    }

    public static void main(String[] args) {
        System.out.println("Main thread started.");
        MyThread thread1 = new MyThread("JavaThread-1");
        thread1.start(); // Invokes run() method in a new thread of execution

        MyThread thread2 = new MyThread("JavaThread-2");
        thread2.start(); // Invokes run() method in another new thread

        System.out.println("Main thread finished starting other threads.");
    }
}
```

#### Explanation
- We create a class `MyThread` that extends `java.lang.Thread`.
- We override the `run()` method, which contains the code that will be executed by the new thread.
- In `main()`, we create instances of `MyThread` and call their `start()` method. Crucially, never call `run()` directly to start a new thread; it will simply execute the `run()` method in the current thread. `start()` allocates a new call stack and then invokes `run()`.

#### Use Cases
Simple, self-contained tasks where your class doesn't need to inherit from any other class.

### 2. Implementing the `Runnable` Interface
This is generally the preferred way, as Java does not support multiple inheritance.
```java
// Beginner Example: Implementing Runnable interface
class MyRunnable implements Runnable {
    private String threadName;

    public MyRunnable(String name) {
        this.threadName = name;
        System.out.println("Creating " + threadName);
    }

    public void run() {
        System.out.println("Running " + threadName);
        try {
            for (int i = 4; i > 0; i--) {
                System.out.println("Runnable: " + threadName + ", " + i);
                Thread.sleep(50);
            }
        } catch (InterruptedException e) {
            System.out.println("Runnable " + threadName + " interrupted.");
        }
        System.out.println("Runnable " + threadName + " exiting.");
    }

    public static void main(String[] args) {
        System.out.println("Main thread started.");
        Runnable runnable1 = new MyRunnable("JavaRunnable-1");
        Thread thread1 = new Thread(runnable1);
        thread1.start();

        Runnable runnable2 = new MyRunnable("JavaRunnable-2");
        Thread thread2 = new Thread(runnable2);
        thread2.start();

        System.out.println("Main thread finished starting other runnables.");
    }
}
```

#### Explanation
- We create a class `MyRunnable` that implements `java.lang.Runnable`.
- It also overrides the `run()` method.
- In `main()`, we create an instance of `MyRunnable`, then pass it to a `Thread` constructor, and finally call `start()` on the `Thread` object.

#### Use Case
When your class already extends another class, or when you want to separate the task logic from the thread management. This promotes better design and reusability of the task logic.

## Thread Lifecycle and States
A thread in Java goes through several states during its lifetime:

- **NEW**: A thread that has been created but has not yet been started.
```java
Thread myThread = new Thread(new MyRunnable("New Thread"));
// myThread is in NEW state
```

- **RUNNABLE**: A thread that is either executing or ready to execute and waiting for CPU time. It becomes runnable after `start()` is called.
```java
myThread.start(); // myThread is now in RUNNABLE state
```

- **RUNNING**: The thread scheduler (part of the OS and JVM) has selected this thread to execute, and it is currently using the CPU. (Note: `RUNNABLE` and `RUNNING` are often grouped as `RUNNABLE` by `Thread.getState()`).

- **BLOCKED**: A thread that is blocked waiting for a monitor lock (e.g., waiting to enter a `synchronized` block/method that another thread holds the lock for).

- **WAITING**: A thread that is waiting indefinitely for another thread to perform a particular action (e.g., calling `Object.wait()`, `Thread.join()`, `Lock.lock()`). It will not return to the runnable state until another thread explicitly wakes it up (e.g., using `Object.notify()` or `Object.notifyAll()`).

- **TIMED_WAITING**: A thread that is waiting for another thread to perform an action for a specified waiting time (e.g., `Thread.sleep(long millis)`, `Object.wait(long millis)`, `Lock.tryLock(long timeout, TimeUnit unit)`).

- **TERMINATED (DEAD)**: A thread that has completed its execution (either by finishing its run() method or by an uncaught exception). Once a thread is terminated, it cannot be restarted.

### State Diagram
```
       New
        |
        | start()
        V
     Runnable
      / | \
     |  |  | (OS scheduler)
     |  |  |
     V  V  V
    Running
      / | \
     |  |  |
     |  |  | sleep(), wait(time), join(time) -> TIMED_WAITING
     |  |  | wait(), join()                  -> WAITING
     |  |  | waiting for monitor lock       -> BLOCKED
     V  V  V
   Blocked / Waiting / Timed Waiting
     |
     | (Resource available / notify() / time elapsed)
     V
     Runnable
     |
     | (run() method completes)
     V
   Terminated

```

## Thread Synchronization
When multiple threads access shared resources, it's crucial to ensure **thread safety** to prevent **race conditions** (where the outcome depends on the unpredictable timing of thread execution) and **data inconsistency**. Java provides mechanisms for synchronization:

### `synchronized` keyword
The `synchronized` keyword can be used to protect critical sections of code or entire methods, ensuring that only one thread can execute that code at a time. It uses an intrinsic lock (also known as a monitor lock or mutex).

#### Synchronized Methods
When applied to an instance method, the lock is on the object instance (`this`). <br>
When applied to a static method, the lock is on the Class object.

```java
// Intermediate Example: Synchronized Method
class Counter {
    private int count = 0;

    // Synchronized method
    public synchronized void increment() {
        count++;
        System.out.println(Thread.currentThread().getName() + " incremented to: " + count);
    }

    public int getCount() {
        return count;
    }
}

class Incrementer implements Runnable {
    private Counter counter;

    public Incrementer(Counter counter) {
        this.counter = counter;
    }

    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            counter.increment();
            try {
                Thread.sleep(10); // Simulate some work
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Counter sharedCounter = new Counter();

        Thread t1 = new Thread(new Incrementer(sharedCounter), "Thread-A");
        Thread t2 = new Thread(new Incrementer(sharedCounter), "Thread-B");

        t1.start();
        t2.start();

        t1.join(); // Wait for t1 to finish
        t2.join(); // Wait for t2 to finish

        System.out.println("Final Count: " + sharedCounter.getCount());
    }
}
```

**Explanation**: Without `synchronized`, `count++` (which is a read-modify-write operation) would be susceptible to race conditions. With `synchronized`, only one thread can execute `increment()` on the `sharedCounter` object at any given time, ensuring correct incrementing.

#### Synchronized Blocks
Provides finer-grained control over locking, allowing you to synchronize only a specific part of the code. The lock is on the object specified in the parentheses.

```java
// Intermediate Example: Synchronized Block
class Account {
    private double balance;
    private final Object lock = new Object(); // A dedicated lock object

    public Account(double initialBalance) {
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        synchronized (lock) { // Acquire lock on 'lock' object
            System.out.println(Thread.currentThread().getName() + " depositing " + amount);
            double newBalance = balance + amount;
            // Simulate some processing time
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            balance = newBalance;
            System.out.println(Thread.currentThread().getName() + " new balance: " + balance);
        } // Release lock
    }

    public double getBalance() {
        return balance;
    }

    public static void main(String[] args) throws InterruptedException {
        Account userAccount = new Account(1000);

        Runnable depositTask = () -> {
            for (int i = 0; i < 5; i++) {
                userAccount.deposit(100);
            }
        };

        Thread t1 = new Thread(depositTask, "Depositor-1");
        Thread t2 = new Thread(depositTask, "Depositor-2");

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final Account Balance: " + userAccount.getBalance());
    }
}
```

**Explanation**: The `synchronized (lock)` block ensures that only one thread can modify the `balance` at a time. Using a dedicated `lock` object rather than `this` can prevent unintended blocking if `Account` had other synchronized methods that don't need to be mutually exclusive with `deposit`.

### `volatile` keyword
The `volatile` keyword guarantees visibility of changes to variables across threads. When a variable is declared `volatile`:
- **Visibility**: All writes to a `volatile` variable are immediately written to main memory, and all reads from a `volatile` variable are immediately read from main memory. This bypasses thread-local caches.
- **Ordering**: It prevents reordering of instructions around the `volatile` variable by the compiler and CPU. Specifically, a write to a `volatile` variable happens-before any subsequent read of that variable.

**Important Note**: `volatile` only guarantees visibility and ordering, not atomicity. For compound operations (like `count++`), `volatile` is insufficient; you still need `synchronized` or atomic classes.

```java
// Intermediate Example: Volatile Keyword
class VolatileCounter {
    public volatile boolean stop = false;

    public void runCounter() {
        System.out.println(Thread.currentThread().getName() + " starting counter.");
        long count = 0;
        while (!stop) { // Reads 'stop' from main memory
            count++;
        }
        System.out.println(Thread.currentThread().getName() + " stopped at count: " + count);
    }

    public static void main(String[] args) throws InterruptedException {
        VolatileCounter vc = new VolatileCounter();

        Thread counterThread = new Thread(() -> vc.runCounter(), "CounterThread");
        counterThread.start();

        // Give the counter thread some time to run
        Thread.sleep(100);

        // Change 'stop' from main thread
        vc.stop = true; // Writes 'stop' to main memory, making it visible to counterThread
        System.out.println(Thread.currentThread().getName() + " setting stop to true.");

        counterThread.join(); // Wait for counterThread to finish
        System.out.println("Main thread finished.");
    }
}
```

**Explanation**:  Without `volatile`, the `counterThread` might cache the value of `stop` in its working memory and never see the change made by the main thread, leading to an infinite loop. `volatile` ensures that the `stop` flag's update is immediately visible.

### `wait()`, `notify()`, `notifyAll()`
These methods (part of `Object` class) are used for inter-thread communication and coordination within `synchronized` blocks. They allow threads to temporarily release a lock and wait for a condition to become true, and then be notified by another thread when that condition is met.

```java
// Intermediate Example: wait(), notify(), notifyAll() (Producer-Consumer)
class Message {
    private String msg;
    private boolean isEmpty = true;

    public synchronized String take() {
        while (isEmpty) {
            try {
                wait(); // Release lock and wait until notified
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        isEmpty = true;
        notifyAll(); // Notify waiting producers
        return msg;
    }

    public synchronized void put(String msg) {
        while (!isEmpty) {
            try {
                wait(); // Release lock and wait until notified
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        isEmpty = false;
        this.msg = msg;
        notifyAll(); // Notify waiting consumers
    }
}

class Producer implements Runnable {
    private Message message;

    public Producer(Message message) {
        this.message = message;
    }

    @Override
    public void run() {
        String[] messages = {"Hello", "World", "Java", "Concurrency", "DONE"};
        for (String msg : messages) {
            message.put(msg);
            System.out.println("Produced: " + msg);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}

class Consumer implements Runnable {
    private Message message;

    public Consumer(Message message) {
        this.message = message;
    }

    @Override
    public void run() {
        String msg;
        do {
            msg = message.take();
            System.out.println("Consumed: " + msg);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        } while (!msg.equals("DONE"));
    }
}

public class ProducerConsumerExample {
    public static void main(String[] args) {
        Message message = new Message();
        new Thread(new Producer(message)).start();
        new Thread(new Consumer(message)).start();
    }
}
```

**Explanation**: This classic Producer-Consumer problem demonstrates `wait()` and `notifyAll()`.
- The `take()` method waits if the message buffer is empty (`isEmpty` is true) and releases the lock on `message`.
- The `put()` method waits if the message buffer is full (`isEmpty` is false) and releases the lock.
- When a producer puts a message, it notifies all waiting consumers. When a consumer takes a message, it notifies all waiting producers.
- `wait()`, `notify()`, `notifyAll()` must be called within a `synchronized` block/method because they operate on the monitor lock of the object.

### `join()` method
The `join()` method allows one thread to wait for the completion of another thread.
```java
// Intermediate Example: join()
class Task implements Runnable {
    private String name;

    public Task(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        System.out.println(name + " started.");
        try {
            Thread.sleep(2000); // Simulate long-running task
        } catch (InterruptedException e) {
            System.out.println(name + " interrupted.");
        }
        System.out.println(name + " finished.");
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new Task("Task-1"));
        Thread t2 = new Thread(new Task("Task-2"));

        t1.start();
        t2.start();

        System.out.println("Main thread waiting for Task-1 to finish...");
        t1.join(); // Main thread waits for t1 to complete

        System.out.println("Main thread waiting for Task-2 to finish...");
        t2.join(); // Main thread waits for t2 to complete

        System.out.println("All tasks completed. Main thread exiting.");
    }
}
```

**Explanation**: The main thread will pause its execution after starting `t1` and `t2` until `t1` completes, then it will wait for `t2` to complete, and only then will it print "All tasks completed."

### Executor Framework (Thread Pools)
Directly managing threads (creating, starting, stopping) for every task is inefficient and resource-intensive, especially for applications with many short-lived tasks. Thread Pools address this by managing a pool of reusable threads. The Executor Framework (introduced in Java 5) is the primary way to use thread pools.
- `Executor`: A simple interface for executing `Runnable` tasks.
- `ExecutorService`: An extension of `Executor` that provides methods for managing the lifecycle of the executor, submitting tasks, and obtaining results.
- `Executors`: A utility class that provides factory methods for commonly used ExecutorService configurations.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.Callable;
import java.util.concurrent.TimeUnit;
import java.util.List;
import java.util.ArrayList;

// Advanced Example: ExecutorService (Thread Pool)
class MyTask implements Runnable {
    private String name;

    public MyTask(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " executing " + name);
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println(Thread.currentThread().getName() + " finished " + name);
    }
}

class MyCallable implements Callable<String> {
    private String name;

    public MyCallable(String name) {
        this.name = name;
    }

    @Override
    public String call() throws Exception {
        System.out.println(Thread.currentThread().getName() + " executing callable " + name);
        try {
            Thread.sleep(150);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            throw new InterruptedException("Callable was interrupted!");
        }
        System.out.println(Thread.currentThread().getName() + " finished callable " + name);
        return "Result from " + name;
    }
}

public class ExecutorServiceExample {
    public static void main(String[] args) throws Exception {
        // 1. Fixed Thread Pool: A fixed number of threads always available
        ExecutorService fixedPool = Executors.newFixedThreadPool(3);
        System.out.println("\n--- Fixed Thread Pool Example ---");
        for (int i = 0; i < 5; i++) {
            fixedPool.execute(new MyTask("Task-" + (i + 1)));
        }
        fixedPool.shutdown(); // Initiates an orderly shutdown
        fixedPool.awaitTermination(1, TimeUnit.SECONDS); // Wait for tasks to complete

        // 2. Cached Thread Pool: Creates new threads as needed, reuses idle ones.
        // Good for applications with many short-lived tasks.
        ExecutorService cachedPool = Executors.newCachedThreadPool();
        System.out.println("\n--- Cached Thread Pool Example ---");
        List<Future<String>> futures = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            futures.add(cachedPool.submit(new MyCallable("Callable-" + (i + 1))));
        }
        for (Future<String> future : futures) {
            System.out.println("Callable Result: " + future.get()); // .get() blocks until result is available
        }
        cachedPool.shutdown();
        cachedPool.awaitTermination(1, TimeUnit.SECONDS);

        // 3. Single Thread Executor: A single thread for sequential execution of tasks
        ExecutorService singlePool = Executors.newSingleThreadExecutor();
        System.out.println("\n--- Single Thread Executor Example ---");
        for (int i = 0; i < 3; i++) {
            singlePool.execute(() -> System.out.println(Thread.currentThread().getName() + " processing single task."));
        }
        singlePool.shutdown();
        singlePool.awaitTermination(1, TimeUnit.SECONDS);

        // 4. Scheduled Thread Pool: For scheduling tasks to run after a delay or periodically
        // (Not shown in detail here, but useful for recurring tasks)
        // ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(1);
        // scheduledPool.scheduleAtFixedRate(new MyTask("Scheduled Task"), 0, 500, TimeUnit.MILLISECONDS);
        // scheduledPool.shutdown(); // Don't forget to shut down
    }
}
```

#### Explanation
- `execute(Runnable)`: Submits a `Runnable` task for execution. It doesn't return a result.
- `submit(Callable)`: Submits a `Callable` task (which can return a result and throw an exception) and returns a `Future` object.
- `Future`: Represents the result of an asynchronous computation. `future.get()` blocks until the task completes and its result is available.
- `shutdown()`: Initiates an orderly shutdown, allowing previously submitted tasks to complete, but no new tasks will be accepted.
- `awaitTermination()`: Blocks until all tasks have completed execution after a shutdown request, or the timeout occurs, or the current thread is interrupted, whichever happens first.

---

### Locks (Explicit Locks)
The `java.util.concurrent.locks` package provides more flexible and powerful locking mechanisms than the `synchronized` keyword. `ReentrantLock` is a common example.

```java
import java.util.concurrent.locks.ReentrantLock;

// Advanced Example: ReentrantLock
class SharedResource {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();

    public void increment() {
        lock.lock(); // Acquire the lock
        try {
            count++;
            System.out.println(Thread.currentThread().getName() + " incremented to: " + count);
        } finally {
            lock.unlock(); // Release the lock in a finally block
        }
    }

    public int getCount() {
        return count;
    }

    public static void main(String[] args) throws InterruptedException {
        SharedResource resource = new SharedResource();

        Runnable task = () -> {
            for (int i = 0; i < 5; i++) {
                resource.increment();
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final Count with ReentrantLock: " + resource.getCount());
    }
}
```

#### Explanation
- `ReentrantLock` offers more features than `synchronized`, such as:
  - **Fairness**: Can optionally provide a fair ordering policy (threads acquire the lock in the order they requested it).
  - `tryLock()`: Attempts to acquire the lock without blocking.
  - `lockInterruptibly()`: Acquires the lock unless the current thread is interrupted.
- It's crucial to release the lock in a `finally` block to prevent deadlocks if an exception occurs within the `try` block.

### Atomic Variables
The `java.util.concurrent.atomic` package provides classes that support atomic operations on single variables. These classes use low-level, hardware-supported atomic instructions (like compare-and-swap - CAS) to achieve thread-safe operations without explicit

```java
import java.util.concurrent.atomic.AtomicInteger;

// Advanced Example: AtomicInteger
class AtomicCounter {
    private AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet(); // Atomically increments and gets the new value
        System.out.println(Thread.currentThread().getName() + " incremented to: " + count.get());
    }

    public int getCount() {
        return count.get();
    }

    public static void main(String[] args) throws InterruptedException {
        AtomicCounter atomicCounter = new AtomicCounter();

        Runnable task = () -> {
            for (int i = 0; i < 5; i++) {
                atomicCounter.increment();
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread t1 = new Thread(task, "AtomicThread-1");
        Thread t2 = new Thread(task, "AtomicThread-2");

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final Count with AtomicInteger: " + atomicCounter.getCount());
    }
}
```

#### Explanation
`AtomicInteger` ensures that `incrementAndGet()` is an atomic operation, meaning it completes as a single, indivisible unit, even with multiple threads. This avoids the need for `synchronized` or `ReentrantLock` for simple counter operations. Other atomic classes include `AtomicLong`, `AtomicBoolean`, `AtomicReference`, etc.

### Concurrent Collections
The `java.util.concurrent` package also offers thread-safe collection classes that are optimized for concurrent access, generally outperforming synchronized wrappers (like `Collections.synchronizedList()`) in highly concurrent environments.
- `ConcurrentHashMap`: A thread-safe hash map that allows concurrent reads and concurrent writes to different segments of the map.
- `CopyOnWriteArrayList`: A thread-safe variant of `ArrayList` where all mutative operations (add, set, remove, etc.) create a new copy of the underlying array. Reads do not require locking. Best for situations where reads vastly outnumber writes.
- `BlockingQueue` implementations (e.g., `ArrayBlockingQueue`, `LinkedBlockingQueue`): Queues that block producers when the queue is full and consumers when the queue is empty. Ideal for producer-consumer patterns.

```java
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.Map;
import java.util.List;

// Advanced Example: Concurrent Collections
public class ConcurrentCollectionsExample {

    public static void main(String[] args) throws InterruptedException {
        // ConcurrentHashMap Example
        System.out.println("\n--- ConcurrentHashMap Example ---");
        Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
        ExecutorService mapExecutor = Executors.newFixedThreadPool(5);

        for (int i = 0; i < 10; i++) {
            final int taskId = i;
            mapExecutor.submit(() -> {
                concurrentMap.put("Key-" + taskId, taskId);
                System.out.println(Thread.currentThread().getName() + " put: Key-" + taskId + " -> " + taskId);
            });
        }
        mapExecutor.shutdown();
        mapExecutor.awaitTermination(1, TimeUnit.SECONDS);
        System.out.println("ConcurrentHashMap size: " + concurrentMap.size());
        System.out.println("ConcurrentHashMap: " + concurrentMap);

        // CopyOnWriteArrayList Example
        System.out.println("\n--- CopyOnWriteArrayList Example ---");
        List<String> copyOnWriteList = new CopyOnWriteArrayList<>();
        ExecutorService listExecutor = Executors.newFixedThreadPool(5);

        listExecutor.submit(() -> {
            copyOnWriteList.add("Item 1");
            copyOnWriteList.add("Item 2");
            System.out.println(Thread.currentThread().getName() + " added items. List: " + copyOnWriteList);
        });

        listExecutor.submit(() -> {
            // Readers see the consistent state without contention
            System.out.println(Thread.currentThread().getName() + " reading list: " + copyOnWriteList);
            try {
                Thread.sleep(50); // Give writer a chance to add
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            System.out.println(Thread.currentThread().getName() + " reading list again: " + copyOnWriteList);
        });
        listExecutor.shutdown();
        listExecutor.awaitTermination(1, TimeUnit.SECONDS);

        // BlockingQueue Example (Simple Producer-Consumer with ArrayBlockingQueue)
        System.out.println("\n--- BlockingQueue Example ---");
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(5); // Capacity of 5

        Runnable producer = () -> {
            try {
                for (int i = 0; i < 10; i++) {
                    String data = "Data-" + i;
                    queue.put(data); // Blocks if queue is full
                    System.out.println(Thread.currentThread().getName() + " produced: " + data + ", Queue size: " + queue.size());
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        Runnable consumer = () -> {
            try {
                for (int i = 0; i < 10; i++) {
                    String data = queue.take(); // Blocks if queue is empty
                    System.out.println(Thread.currentThread().getName() + " consumed: " + data + ", Queue size: " + queue.size());
                    Thread.sleep(200);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        ExecutorService queueExecutor = Executors.newFixedThreadPool(2);
        queueExecutor.submit(producer);
        queueExecutor.submit(consumer);

        queueExecutor.shutdown();
        queueExecutor.awaitTermination(5, TimeUnit.SECONDS);
    }
}
```

#### Explanation:
- `ConcurrentHashMap` allows multiple threads to read and write without blocking each other for independent segments.
- `CopyOnWriteArrayList` is useful when iteration is much more common than modification. Iterators created on `CopyOnWriteArrayList` will not throw ConcurrentModificationException.
- `BlockingQueue` simplifies producer-consumer patterns by handling the waiting/notification logic automatically. `put()` and `take()` methods block when necessary

### `ThreadLocal`
`ThreadLocal` variables provide a way to create a per-thread copy of a variable. Each thread that accesses a `ThreadLocal` variable gets its own independently initialized copy of the variable. This is useful for avoiding synchronization overhead when data doesn't need to be shared between threads but needs to be unique to a thread.

```java
// Advanced Example: ThreadLocal
class UserContext {
    public static final ThreadLocal<String> currentUser = new ThreadLocal<>();

    public void processRequest(String userId) {
        currentUser.set(userId); // Set user for this thread
        System.out.println(Thread.currentThread().getName() + " processing for user: " + currentUser.get());
        // Simulate some operations
        try {
            Thread.sleep(50);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        anotherMethod();
        currentUser.remove(); // Clean up the thread-local variable
    }

    private void anotherMethod() {
        // Access current user without passing it around
        System.out.println(Thread.currentThread().getName() + " in another method, user: " + currentUser.get());
    }

    public static void main(String[] args) throws InterruptedException {
        UserContext service = new UserContext();
        ExecutorService executor = Executors.newFixedThreadPool(2);

        executor.submit(() -> service.processRequest("UserA"));
        executor.submit(() -> service.processRequest("UserB"));

        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.SECONDS);
    }
}
```

#### Explanation
Each thread (from the `ExecutorService`) gets its own `currentUser` value. When `Thread-1` sets `currentUser` to "UserA", `Thread-2`'s currentUser remains unaffected. This avoids passing userId as a parameter through multiple method calls. It's crucial to `remove()` `ThreadLocal` variables to prevent memory leaks, especially in thread pools where threads are reused.

## Common Pitfalls in Java Concurrency
Writing correct and efficient concurrent programs is notoriously difficult. Here are some common pitfalls:
- **Race Conditions**: Multiple threads accessing and modifying shared data concurrently without proper synchronization, leading to unpredictable results. (Example: `count++` without `synchronized` or `AtomicInteger`).
- **Deadlock**: Two or more threads are blocked indefinitely, each waiting for the other to release a resource that it needs.

```java
// Simple Deadlock Example
public class DeadlockExample {
    private static Object lock1 = new Object();
    private static Object lock2 = new Object();

    public static void main(String[] args) {
        // Thread 1 acquires lock1 then lock2
        new Thread(() -> {
            synchronized (lock1) {
                System.out.println("Thread 1: Acquired lock1");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                System.out.println("Thread 1: Waiting for lock2");
                synchronized (lock2) {
                    System.out.println("Thread 1: Acquired lock2");
                }
            }
        }).start();

        // Thread 2 acquires lock2 then lock1
        new Thread(() -> {
            synchronized (lock2) {
                System.out.println("Thread 2: Acquired lock2");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                System.out.println("Thread 2: Waiting for lock1");
                synchronized (lock1) {
                    System.out.println("Thread 2: Acquired lock1");
                }
            }
        }).start();
    }
}
```

**Resolution**: Consistent lock ordering. If both threads always try to acquire `lock1` then `lock2`, deadlock is avoided.
- **Livelock**: Threads are not blocked but are continuously changing their state in response to other threads, preventing any actual progress.
- **Starvation**: A thread repeatedly loses the race for a resource or CPU time and never gets to execute. This can happen with unfair locks or high-priority threads constantly monopolizing the CPU.
- **Visibility Problems**: Changes made by one thread to shared variables are not visible to other threads due to caching, leading to stale data. (Resolved by `volatile` or `synchronized`).
- **Lost Updates**: When multiple threads try to update the same shared variable, and one thread's update is overwritten by another, resulting in the update being "lost."
- **Spurious Wakeups**: `wait()` can sometimes return even if `notify()` or `notifyAll()` was not called. Always use `wait()` inside a loop to re-check the condition: `while (condition is false) { wait(); }`.
- **Not handling** `InterruptedException`: Threads can be interrupted while sleeping or waiting. It's important to catch `InterruptedException` and decide whether to stop the thread or re-interrupt it.
- **Excessive Synchronization**: Over-synchronizing can lead to reduced parallelism and degraded performance. Identify critical sections precisely.
- **Incorrect use of** `ThreadLocal` **in Thread Pools**: If `ThreadLocal` variables are not `remove()`d after use, they can retain old data when threads are reused from a pool, leading to subtle bugs or memory leaks.

## Best Practices for Java Concurrency
- **Prefer** `java.util.concurrent` **utilities**: Use `ExecutorService`, `ConcurrentHashMap`, `Atomic` classes, `BlockingQueue`, `CountDownLatch`, `Semaphore`, `CyclicBarrier`, etc., over lower-level `Thread` and `synchronized` where possible. They are designed for concurrency and are generally more robust and efficient.
- **Minimize Shared Mutable State**: The less shared mutable state, the fewer synchronization problems. Consider immutability or thread confinement (`ThreadLocal`).
- **Use Immutable Objects**: Immutable objects are inherently thread-safe because their state cannot change after creation.
- **Identify Critical Sections**: Only synchronize the minimum amount of code necessary to protect shared mutable data.
- **Consistent Lock Ordering**: To prevent deadlocks, ensure that if your application needs to acquire multiple locks, it always acquires them in a consistent, predefined order.
- **Use** `volatile` **sparingly**: For simple flag variables where only visibility (not atomicity) is needed. Don't use it as a replacement for full synchronization.
- **Handle** `InterruptedException` **gracefully**: Decide on a policy for handling interruptions (e.g., stop the thread, re-interrupt, log).
- **Avoid Nested Locks**: Reduce the chance of deadlocks. If unavoidable, use `tryLock()` with timeouts to detect and recover from potential deadlocks.
- **Proper Thread Pool Sizing**: Choose appropriate thread pool types and sizes based on the nature of your tasks (CPU-bound vs. I/O-bound).
  - **CPU-bound tasks**: `corePoolSize = number of CPU cores + 1 (or simply number of CPU cores)`.
  - **I/O-bound tasks**: `corePoolSize = number of CPU cores * (1 + (Wait Time / CPU Time))`.
- **Shutdown** `ExecutorService`: Always call `shutdown()` or `shutdownNow()` to gracefully terminate the thread pool and release resources.
- **Monitor and Profile**: Use tools like JConsole, VisualVM, or commercial APM tools to monitor thread activity, identify bottlenecks, and diagnose concurrency issues.
- **Structured Concurrency (Java 21+)**: For newer Java versions, explore the concepts of Virtual Threads and Structured Concurrency, which aim to simplify concurrent programming by treating concurrent tasks as a single unit of work.

## Performance Considerations
While concurrency can improve performance, it also introduces overhead:
- **Synchronization Overhead**: Acquiring and releasing locks, even with volatile or atomic operations, incurs a performance cost. Too much synchronization can lead to contention, where threads spend more time waiting for locks than doing actual work, potentially making a concurrent program slower than its sequential counterpart.
- **Context Switching**: Switching the CPU's execution context from one thread to another has a cost (saving registers, loading new ones, cache invalidation). Excessive context switching (due to many threads or fine-grained tasks) can degrade performance.
- **Cache Coherency**: In multi-core systems, when one core modifies data in its cache, other cores with cached copies of that data need to invalidate their caches and fetch the fresh data from main memory, leading to performance hits. `volatile` variables specifically interact with this.
- **Memory Footprint**: Each thread requires its own stack space. Creating too many threads can exhaust memory. Thread pools mitigate this by reusing threads.
- **False Sharing**: Occurs when different threads access independent variables that happen to reside in the same cache line. Modifying one variable invalidates the entire cache line for other cores, even though they are accessing different data. This can be mitigated by careful data structure design or padding.
- **Amdahl's Law**: A formula that states the theoretical speedup achievable by a parallel program is limited by the sequential portion of the program. Even with infinite processors, the speedup can't exceed `1 / (1 - P)`, where `P` is the parallelizable portion. This means focusing on parallelizing the largest, most time-consuming parts is crucial.

---

# Introduction: Deeper Thread Control
## Importance
- **Thread Joining**: Allows one thread to wait for another thread to complete its execution. This is vital for tasks that have dependencies, ensuring that results from one task are ready before another begins.
- **Daemon Threads**: Provides a mechanism for background, non-critical services. They are automatically terminated by the JVM when all non-daemon threads have finished, preventing the JVM from waiting indefinitely for background tasks.
- **Thread Priority**: Offers a hint to the thread scheduler about the relative importance of a thread, potentially influencing which thread gets CPU time when multiple threads are runnable.

## Thread Joining (`join()` method)
The `join()` method of the `Thread` class allows a calling thread to pause its execution until the thread on which `join()` is called terminates. It's a way to establish a **happens-before** relationship: all actions in the joined thread happen-before the calling thread resumes execution

### Overview and Importance
Imagine you have a main task that relies on the results of several sub-tasks performed by other threads. Without `join()`, the main task might proceed before the sub-tasks are complete, leading to incorrect results or errors. `join()` provides a simple, effective way to synchronize the completion of threads.

#### Syntax
```java
threadObject.join();
threadObject.join(long millis); // Waits for up to 'millis' milliseconds
threadObject.join(long millis, int nanos); // Waits for up to 'millis' milliseconds + 'nanos' nanoseconds
```

The `join()` methods can throw an `InterruptedException` if the calling thread is interrupted while waiting

### Use Cases and Implementation
#### Beginner Level: Basic Synchronization
```java
// Beginner Example: Basic Thread Joining
class SimpleWorker implements Runnable {
    private String name;
    private long delay;

    public SimpleWorker(String name, long delay) {
        this.name = name;
        this.delay = delay;
    }

    @Override
    public void run() {
        System.out.println(name + " started.");
        try {
            Thread.sleep(delay); // Simulate work
        } catch (InterruptedException e) {
            System.out.println(name + " was interrupted.");
            Thread.currentThread().interrupt(); // Re-interrupt the current thread
        }
        System.out.println(name + " finished.");
    }

    public static void main(String[] args) {
        System.out.println("Main thread started.");

        Thread worker1 = new Thread(new SimpleWorker("Worker-1", 2000));
        Thread worker2 = new Thread(new SimpleWorker("Worker-2", 1000));

        worker1.start();
        worker2.start();

        try {
            System.out.println("Main thread waiting for Worker-1 to finish...");
            worker1.join(); // Main thread waits for worker1
            System.out.println("Main thread waiting for Worker-2 to finish...");
            worker2.join(); // Main thread waits for worker2
        } catch (InterruptedException e) {
            System.out.println("Main thread interrupted while joining.");
            Thread.currentThread().interrupt();
        }

        System.out.println("All workers completed. Main thread exiting.");
    }
}
```

**Explanation**: The `main` thread starts `worker1` and `worker2`. Without `join()`, "All workers completed. Main thread exiting." might be printed before the workers actually finish. By calling `worker1.join()` and `worker2.join()`, the main thread explicitly waits for each worker to complete its `run()` method before proceeding.

#### Intermediate Level: Waiting with Timeout
```java
// Intermediate Example: Thread Joining with Timeout
class TimedWorker implements Runnable {
    private String name;
    private long delay;

    public TimedWorker(String name, long delay) {
        this.name = name;
        this.delay = delay;
    }

    @Override
    public void run() {
        System.out.println(name + " started.");
        try {
            Thread.sleep(delay); // Simulate work
            System.out.println(name + " completed its task.");
        } catch (InterruptedException e) {
            System.out.println(name + " was interrupted and finished early.");
            Thread.currentThread().interrupt();
        }
    }

    public static void main(String[] args) {
        System.out.println("Main thread started.");

        Thread importantTask = new Thread(new TimedWorker("ImportantTask", 5000)); // Takes 5 seconds
        importantTask.start();

        long startTime = System.currentTimeMillis();
        try {
            System.out.println("Main thread trying to join ImportantTask for 2 seconds...");
            importantTask.join(2000); // Wait for a maximum of 2 seconds
        } catch (InterruptedException e) {
            System.out.println("Main thread interrupted while joining.");
            Thread.currentThread().interrupt();
        }

        long elapsedTime = System.currentTimeMillis() - startTime;
        if (importantTask.isAlive()) {
            System.out.println("ImportantTask is still running after " + elapsedTime + "ms. Main thread proceeds.");
            // Optionally, try to interrupt the importantTask if it's taking too long
            // importantTask.interrupt();
        } else {
            System.out.println("ImportantTask finished within " + elapsedTime + "ms. Main thread proceeds.");
        }

        System.out.println("Main thread exiting.");
    }
}
```

**Explanation**: The `main` thread tries to wait for `ImportantTask` for a maximum of 2 seconds. Since `ImportantTask` takes 5 seconds, the `main` thread will resume after 2 seconds without `ImportantTask` having completed. This is useful for implementing timeouts or for allowing a main thread to proceed with other work if a sub-task is taking too long.

#### Advanced Level: Collaborative Shutdown
```java
// Advanced Example: Collaborative Shutdown with Joining
import java.util.concurrent.atomic.AtomicBoolean;

class DataProcessor implements Runnable {
    private String name;
    private AtomicBoolean running;

    public DataProcessor(String name, AtomicBoolean running) {
        this.name = name;
        this.running = running;
    }

    @Override
    public void run() {
        System.out.println(name + " started processing data.");
        while (running.get() && !Thread.currentThread().isInterrupted()) {
            // Simulate data processing
            try {
                System.out.println(name + ": Processing batch...");
                Thread.sleep(500);
            } catch (InterruptedException e) {
                System.out.println(name + " interrupted while processing. Shutting down.");
                Thread.currentThread().interrupt(); // Re-interrupt
                break; // Exit loop
            }
        }
        System.out.println(name + " finished processing and stopped.");
    }
}

public class CollaborativeShutdown {
    public static void main(String[] args) throws InterruptedException {
        AtomicBoolean systemRunning = new AtomicBoolean(true); // Shared flag for graceful shutdown

        Thread processor1 = new Thread(new DataProcessor("Processor-1", systemRunning));
        Thread processor2 = new Thread(new DataProcessor("Processor-2", systemRunning));

        processor1.start();
        processor2.start();

        // Let processors run for a while
        Thread.sleep(3000);

        System.out.println("Main thread signalling processors to shut down...");
        systemRunning.set(false); // Signal all processors to stop gracefully

        // Join to wait for them to complete their current tasks and exit
        System.out.println("Main thread waiting for Processor-1 to join...");
        processor1.join(1000); // Wait for up to 1 second for Processor-1

        if (processor1.isAlive()) {
            System.out.println("Processor-1 did not shut down gracefully in time. Interrupting.");
            processor1.interrupt(); // Force interrupt if it doesn't shut down in time
            processor1.join(); // Wait again for the interrupted thread to truly terminate
        } else {
            System.out.println("Processor-1 gracefully shut down.");
        }

        System.out.println("Main thread waiting for Processor-2 to join...");
        processor2.join(1000);

        if (processor2.isAlive()) {
            System.out.println("Processor-2 did not shut down gracefully in time. Interrupting.");
            processor2.interrupt();
            processor2.join();
        } else {
            System.out.println("Processor-2 gracefully shut down.");
        }

        System.out.println("All system components shut down. Main thread exiting.");
    }
}
```

**Explanation**: This example demonstrates a common pattern for graceful application shutdown. The `main` thread signals worker threads to stop using a shared `AtomicBoolean`. It then uses `join()` with a timeout to wait for them to finish. If a worker doesn't stop within the timeout, the `main` thread can resort to `interrupt()`ing it, and then joining again to ensure termination. This pattern ensures resources are released properly and prevents the main application from hanging.

### Common Pitfalls with `join()`
- **Deadlock**: If thread A joins thread B, and thread B somehow tries to join thread A (directly or indirectly), it will result in a deadlock.
- **Indefinite Waiting**: Calling `join()` without a timeout on a thread that never terminates will cause the calling thread to wait indefinitely.
- `InterruptedException` **Handling**: Forgetting to handle `InterruptedException` or handling it improperly can lead to unexpected behavior or hide bugs. Always `Thread.currentThread().interrupt();` if you catch it and can't fully handle it, so that higher-level code can be aware of the interruption.

### Best Practices for `join()`
- **Use with Timeout**: For most real-world scenarios, use `join(long millis)` to avoid indefinite waits and build more resilient applications.
- **Handle** `InterruptedException`: Always wrap `join()` calls in a `try-catch (InterruptedException e)` block.
- **Clean Shutdown**: `join()` is excellent for waiting for worker threads to complete their current tasks during a graceful application shutdown.
- **Prefer Higher-Level Constructs**: For complex task dependencies and orchestrations, consider `ExecutorService` with `Future` objects (`future.get()` also effectively joins a task), `CountDownLatch`, or `CyclicBarrier` from `java.util.concurrent` for more structured approaches.

## Daemon Threads
A **daemon thread** is a thread that runs in the background and provides services to user threads (non-daemon threads). The Java Virtual Machine (JVM) terminates itself when the only threads running are daemon threads.

### Overview and Importance
Think of daemon threads as background workers for the JVM itself, or for your application's internal services. For example, the garbage collector is a daemon thread. If you have a task that doesn't need to prevent the application from exiting (e.g., logging, monitoring, temporary file cleanup), it's a good candidate for a daemon thread.

#### Key Characteristics:
- **JVM Shutdown**: The JVM does **not** wait for daemon threads to finish. If all user threads die, daemon threads are abruptly terminated, without guaranteed `finally` blocks being executed or resources being released.
- **Service Providers**: They typically provide services to other threads.
- **Created by User Threads**: You can explicitly mark a thread as a daemon thread

#### Syntax
```java
threadObject.setDaemon(true); // Must be called BEFORE starting the thread
```

You can check if a thread is a daemon thread using `threadObject.isDaemon()`

### Use Cases and Implementation
#### Beginner Level: Basic Daemon Thread
```java
// Beginner Example: Basic Daemon Thread
class DaemonTask implements Runnable {
    @Override
    public void run() {
        while (true) { // This thread will run indefinitely
            System.out.println(Thread.currentThread().getName() + " is running (daemon).");
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + " interrupted.");
                break;
            }
        }
        System.out.println(Thread.currentThread().getName() + " finished."); // This might not be printed
    }

    public static void main(String[] args) throws InterruptedException {
        System.out.println("Main thread started.");

        Thread userThread = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + " is a user thread, doing some work.");
            try {
                Thread.sleep(2000); // Simulate user thread work
            } catch (InterruptedException e) {
                System.currentThread().interrupt();
            }
            System.out.println(Thread.currentThread().getName() + " finished.");
        }, "UserThread");


        Thread daemonThread = new Thread(new DaemonTask(), "DaemonThread");
        daemonThread.setDaemon(true); // Mark as daemon BEFORE starting

        userThread.start();
        daemonThread.start();

        // Main thread finishes its work and then exits,
        // which will cause the JVM to exit, terminating DaemonThread.
        System.out.println("Main thread exiting.");
    }
}
```

**Output Observation**: <br>
You will observe "DaemonThread is running (daemon)." repeatedly printed for a few seconds, then "UserThread finished.", and finally "Main thread exiting." The "DaemonThread finished." message will likely not be printed because the JVM terminates it abruptly as soon as the `UserThread` (the last non-daemon thread) finishes

**Explanation**: <br>
The `DaemonTask` is an infinite loop, normally preventing the JVM from exiting. However, by setting `daemonThread.setDaemon(true)`, we tell the JVM that this thread is not critical. When the `UserThread` (a non-daemon thread) completes, there are no more active user threads, so the JVM terminates, taking `daemonThread` with it.

#### Intermediate Level: Logging Service
```java
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

// Intermediate Example: Daemon Thread for Logging Service
class LogService implements Runnable {
    private final BlockingQueue<String> logQueue = new ArrayBlockingQueue<>(100);
    private volatile boolean running = true;
    private PrintWriter writer;

    public LogService(String logFile) throws IOException {
        writer = new PrintWriter(new FileWriter(logFile, true), true); // true for append, true for auto-flush
    }

    public void log(String message) {
        try {
            logQueue.put(message); // Blocks if queue is full
        } catch (InterruptedException e) {
            System.err.println("LogService: Failed to queue message: " + message);
            Thread.currentThread().interrupt();
        }
    }

    public void stopService() {
        running = false;
        // Optionally add a "poison pill" to the queue to unblock take()
        log("STOP_LOGGING_SIGNAL");
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " starting log service.");
        while (running || !logQueue.isEmpty()) { // Process remaining messages even after 'running' is false
            try {
                String message = logQueue.take(); // Blocks if queue is empty
                if ("STOP_LOGGING_SIGNAL".equals(message)) {
                    // This is our 'poison pill' to allow exiting gracefully
                    System.out.println(Thread.currentThread().getName() + " received stop signal.");
                    break;
                }
                writer.println(Thread.currentThread().getName() + " writing: " + message);
                // System.out.println(Thread.currentThread().getName() + " wrote: " + message); // For console visibility
            } catch (InterruptedException e) {
                System.err.println(Thread.currentThread().getName() + " interrupted while logging.");
                Thread.currentThread().interrupt();
                break;
            }
        }
        writer.close();
        System.out.println(Thread.currentThread().getName() + " log service stopped and writer closed.");
    }

    public static void main(String[] args) throws InterruptedException, IOException {
        System.out.println("Main thread started.");
        LogService logger = new LogService("application.log");

        Thread logThread = new Thread(logger, "LogDaemon");
        logThread.setDaemon(true); // Crucial: make it a daemon
        logThread.start();

        // Simulate application work
        for (int i = 0; i < 5; i++) {
            logger.log("Application event " + i);
            Thread.sleep(300);
        }

        System.out.println("Main thread finished its work. User threads will exit.");
        // We don't explicitly call logger.stopService() or logThread.join() here.
        // The daemon thread will be terminated by the JVM.
        // If we wanted a graceful shutdown with guaranteed log flush, it shouldn't be a daemon.
    }
}
```

**Explanation**: <br>
This `LogService` acts as a background logger. Messages are queued, and the `LogService` thread processes them. By making it a daemon thread, we ensure that the application won't hang waiting for the logger to finish if the main application threads have completed. However, a significant drawback is that if the JVM exits abruptly, the `writer.close()` might not be called, and some buffered log messages might be lost. If guaranteed delivery is needed, it should be a user thread, and you'd need to explicitly `join()` it during shutdown.

#### Advanced Level: Resource Cleanup (with caution)
```java
import java.io.File;
import java.util.concurrent.TimeUnit;

// Advanced Example: Daemon Thread for Temp File Cleanup (use with extreme caution)
class TempFileCleaner implements Runnable {
    private File tempDir;
    private long cleanupIntervalMillis;
    private volatile boolean running = true;

    public TempFileCleaner(String dirPath, long cleanupIntervalMillis) {
        this.tempDir = new File(dirPath);
        if (!tempDir.exists()) {
            tempDir.mkdirs();
        }
        this.cleanupIntervalMillis = cleanupIntervalMillis;
        System.out.println("TempFileCleaner initialized for: " + tempDir.getAbsolutePath());
    }

    public void stop() {
        running = false;
    }

    private void performCleanup() {
        File[] files = tempDir.listFiles();
        if (files != null) {
            for (File file : files) {
                if (System.currentTimeMillis() - file.lastModified() > TimeUnit.MINUTES.toMillis(1)) { // Older than 1 min
                    if (file.delete()) {
                        System.out.println(Thread.currentThread().getName() + ": Deleted old temp file: " + file.getName());
                    } else {
                        System.err.println(Thread.currentThread().getName() + ": Failed to delete temp file: " + file.getName());
                    }
                }
            }
        }
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " starting temp file cleanup service.");
        while (running && !Thread.currentThread().isInterrupted()) {
            performCleanup();
            try {
                Thread.sleep(cleanupIntervalMillis);
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + " interrupted, stopping cleanup.");
                Thread.currentThread().interrupt();
                break;
            }
        }
        System.out.println(Thread.currentThread().getName() + " temp file cleanup service stopped.");
    }

    public static void main(String[] args) throws InterruptedException {
        String tempDirPath = System.getProperty("java.io.tmpdir") + File.separator + "my_app_temp";
        System.out.println("Temp directory: " + tempDirPath);

        // Simulate creating some temp files
        new File(tempDirPath).mkdirs();
        try {
            new File(tempDirPath, "tempfile1.tmp").createNewFile();
            Thread.sleep(100);
            new File(tempDirPath, "tempfile2.tmp").createNewFile();
            Thread.sleep(100);
            new File(tempDirPath, "tempfile3.tmp").createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }

        TempFileCleaner cleaner = new TempFileCleaner(tempDirPath, 2000); // Clean every 2 seconds
        Thread cleanerThread = new Thread(cleaner, "TempCleanerDaemon");
        cleanerThread.setDaemon(true); // Make it a daemon
        cleanerThread.start();

        System.out.println("Main application running... (will exit in 5 seconds)");
        Thread.sleep(5000); // Main application runs for 5 seconds

        System.out.println("Main thread exiting. Daemon cleaner will be terminated.");
        // No explicit shutdown for cleaner. It will be abruptly killed.
    }
}
```

**Explanation:** <br>
This example shows a daemon thread cleaning up old temporary files. While it might seem convenient, using a daemon thread for resource cleanup carries significant risk. If the JVM exits abruptly, the `run()` method is not guaranteed to finish, meaning `performCleanup()` might not run one last time, potentially leaving uncleaned files. For critical cleanup, it's safer to use a non-daemon thread and explicitly manage its shutdown (e.g., using a shutdown hook or `join()`).

### Common Pitfalls with Daemon Threads
- **No Guaranteed Cleanup**: The biggest pitfall. Daemon threads are terminated abruptly. `finally` blocks are not guaranteed to execute. This means resources like open files, network connections, or database connections held by daemon threads might not be closed properly, leading to resource leaks.
- **Data Loss**: If a daemon thread is responsible for persisting data (e.g., logging, saving state), data loss can occur if the JVM exits before the daemon thread finishes writing.
- **Unexpected Termination**: Debugging can be harder as threads might disappear without completing their logic.
- `setDaemon()` **Order**: Calling `setDaemon(true)` after `start()` will result in an `IllegalThreadStateException`.

### Best Practices for Daemon Threads
- **Use for Non-Critical Background Services**: Only use daemon threads for tasks that don't need to prevent the JVM from exiting and where incomplete execution or resource leakage is acceptable or harmless. Examples include garbage collection, low-priority logging where some loss is acceptable, or monitoring agents.
- **No Critical Resource Handling**: Avoid using daemon threads for tasks that manage critical resources like file I/O, database connections, or network sockets, unless you have robust external mechanisms to handle potential resource leaks.
- **Graceful Shutdown for Critical Background Tasks**: If a background task must complete its work or release resources upon application shutdown, it should be a user thread. Implement a graceful shutdown mechanism (e.g., `volatile` flag, `ExecutorService` shutdown, shutdown hooks).
- **Always Call** `setDaemon(true)` **Before** `start()`: This is a strict requirement.

## Thread Priority
**Thread priority** is a hint to the thread scheduler about the relative importance of a thread. Threads with higher priority are given preference over threads with lower priority for CPU time.

### Overview and Importance
The Java specification defines 10 priority levels, from `MIN_PRIORITY` (1) to `MAX_PRIORITY` (10), with `NORM_PRIORITY` (5) being the default.

**Constants in** `java.lang.Thread`:
- `Thread.MIN_PRIORITY = 1`
- `Thread.NORM_PRIORITY = 5`
- `Thread.MAX_PRIORITY = 10`

#### Syntax
```java
threadObject.setPriority(int newPriority); // newPriority must be between MIN_PRIORITY and MAX_PRIORITY
```

You can get a thred's priority using `threadObject.getPriority()`

#### Importance (with caveats):
Thread priority is highly dependent on the underlying operating system's scheduling policies.
- **OS Dependency**: The JVM maps Java thread priorities to native OS thread priorities. How this mapping occurs and how the OS scheduler interprets these priorities varies between operating systems (e.g., Windows vs. Linux).
- **Non-Guaranteed**: Java thread priorities are not guarantees. They are merely hints. A higher-priority thread is more likely to be scheduled before a lower-priority thread, but it's not guaranteed to always run first or get more CPU time.
- **Fairness**: Some OS schedulers might prioritize fairness over strict priority adherence to prevent starvation of lower-priority threads.

### Use Cases and Implementation
#### Beginner Level: Setting and Observing Priority
```java
// Beginner Example: Setting and Observing Thread Priority
class PriorityTask implements Runnable {
    private String name;
    private int priority;
    private volatile long counter = 0;

    public PriorityTask(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }

    @Override
    public void run() {
        Thread.currentThread().setPriority(priority); // Set priority for the current thread
        long startTime = System.currentTimeMillis();
        while (System.currentTimeMillis() - startTime < 100) { // Run for 100ms
            counter++;
        }
        System.out.println(name + " (Priority: " + Thread.currentThread().getPriority() + ") counted " + counter + " in 100ms.");
    }

    public static void main(String[] args) throws InterruptedException {
        System.out.println("Main thread started.");

        Thread lowPriority = new Thread(new PriorityTask("LowPriority", Thread.MIN_PRIORITY), "LowPrioThread");
        Thread normalPriority = new Thread(new PriorityTask("NormalPriority", Thread.NORM_PRIORITY), "NormalPrioThread");
        Thread highPriority = new Thread(new PriorityTask("HighPriority", Thread.MAX_PRIORITY), "HighPrioThread");

        // Start all threads simultaneously
        lowPriority.start();
        normalPriority.start();
        highPriority.start();

        // Wait for them to finish (optional, but good for clean output)
        lowPriority.join();
        normalPriority.join();
        highPriority.join();

        System.out.println("Main thread exiting.");
    }
}
```

**Explanation:** <br>
You will likely see that `HighPriority` counted significantly more than `LowPriority`, and `NormalPriority` somewhere in between. However, on some systems or with different workloads, the difference might be negligible or inconsistent. This demonstrates that priority is a hint, not a strict guarantee.

#### Intermediate Level: Background vs Foreground Tasks
```java
// Intermediate Example: Priority for Background vs. Foreground
class WorkSimulator implements Runnable {
    private String type;
    private long iterationCount;

    public WorkSimulator(String type) {
        this.type = type;
        this.iterationCount = 0;
    }

    @Override
    public void run() {
        long startTime = System.currentTimeMillis();
        System.out.println(Thread.currentThread().getName() + " (" + type + ") started.");
        while (System.currentTimeMillis() - startTime < 2000) { // Run for 2 seconds
            // Simulate CPU-bound work
            for (int i = 0; i < 10000; i++) {
                double result = Math.sin(Math.cos(Math.tan(i))); // Expensive calculation
            }
            iterationCount++;
        }
        System.out.println(Thread.currentThread().getName() + " (" + type + ") finished after " + iterationCount + " iterations.");
    }

    public static void main(String[] args) throws InterruptedException {
        System.out.println("Main thread started.");

        // Foreground task (e.g., UI updates, critical data processing)
        Thread foregroundTask = new Thread(new WorkSimulator("Foreground"), "ForegroundTask");
        foregroundTask.setPriority(Thread.MAX_PRIORITY); // High priority

        // Background task (e.g., cleanup, less critical data processing)
        Thread backgroundTask = new Thread(new WorkSimulator("Background"), "BackgroundTask");
        backgroundTask.setPriority(Thread.MIN_PRIORITY); // Low priority

        foregroundTask.start();
        backgroundTask.start();

        foregroundTask.join();
        backgroundTask.join();

        System.out.println("All tasks finished. Main thread exiting.");
    }
}
```

**Explanation**: <br>
In this example, we attempt to give a "foreground" task higher priority over a "background" task that performs similar CPU-bound work. On most systems, the ForegroundTask will likely complete significantly more iterations. This pattern is sometimes used in applications where responsiveness for certain tasks (like UI) is more important than background processing.

#### Advanced Level: When Priorities Can Help (Carefully)
While general advice is to use priorities sparingly, there are niche scenarios where they might offer a slight performance edge if applied carefully, primarily in highly specialized real-time or embedded systems, or when profiling reveals consistent benefits.

For instance, if you have a producer-consumer setup and your consumers are constantly lagging, giving consumers a slightly higher priority might help them process items faster, reducing the queue backlog. However, this is usually better addressed by optimizing the processing logic or scaling consumer threads.

General recommendation for advanced usage: In enterprise-level Java applications, relying heavily on `Thread.setPriority()` is often discouraged due to its platform-dependency and unpredictable behavior. Modern Java concurrency features like `ExecutorService` and its various types (e.g., `newFixedThreadPool`, `newWorkStealingPool`) combined with well-designed task decomposition and efficient algorithms are far more effective and reliable for performance optimization than tinkering with thread priorities.

### Common Pitfalls with Thread Priority
- **Platform Dependency**: The biggest pitfall. Behavior is highly inconsistent across operating systems and JVM implementations. Code that works well on one OS might not on another.
- **Starvation**: Setting priorities aggressively can lead to lower-priority threads never getting CPU time (starvation), especially on non-preemptive or poorly configured schedulers.
- **Debugging Difficulty**: Priority-related bugs can be hard to reproduce and debug because they depend on unpredictable scheduling.
- **False Sense of Control**: Gives developers a false sense of precise control over thread execution, which the JVM and OS ultimately manage.
- **Not a Replacement for Synchronization**: Priorities do not guarantee thread safety. A higher-priority thread accessing shared resources still needs proper synchronization (`synchronized`, `Lock`, `Atomic`) to prevent race conditions.

### Best Practices for Thread Priority
- **Avoid Using Unless Absolutely Necessary**: Generally, avoid `Thread.setPriority()` in most enterprise applications. It's often a premature optimization and can introduce more problems than it solves.
- **Default Priority is Usually Fine**: Let threads run at `NORM_PRIORITY` (5) unless you have a compelling, highly specialized reason not to.
- **Test Thoroughly on Target Platforms**: If you must use priorities, test your application extensively on all target operating systems and hardware configurations.
- **Prefer Task-Based Concurrency**: Focus on proper task decomposition, efficient algorithms, and utilizing the `java.util.concurrent` package's robust features (thread pools, concurrent collections, `Future`s) for performance and responsiveness. These provide more reliable and portable control over concurrency.
- **Don't Use for Thread Safety**: Priorities do not replace synchronization mechanisms.
- **Consider Virtual Threads (Java 21+)**: With the advent of Virtual Threads (Project Loom), the concept of OS-level thread priority becomes even less relevant for most application developers. Virtual Threads are scheduled by the JVM, and their scheduling is typically optimized for throughput rather than explicit priority.

---

# Introduction: Beyond `synchronized`
We previously explored the `synchronized` keyword, `volatile`, and `wait()/notify()`. While powerful for basic synchronization, they have limitations:
- `synchronized` **limitations**:
  - **No fairness guarantee**: It doesn't guarantee which waiting thread gets the lock next.
  - **No** `tryLock()`: Cannot attempt to acquire a lock without blocking, nor acquire it with a timeout.
  - **No interruptible lock acquisition**: A thread blocked on `synchronized` cannot be interrupted.
  - **Single condition variable**: A single object's monitor (`wait`/`notify`) might not be flexible enough for complex waiting conditions.
  - **Read/Write exclusivity**: `synchronized` provides mutual exclusion (only one thread can access at a time), which is overly restrictive for read-heavy operations where multiple readers could safely access shared data concurrently.

The `java.util.concurrent.locks` package (introduced in Java 5) addresses these limitations by providing a more flexible and powerful set of lock implementations and condition variables.

#### Why is it important?
These advanced locks offer:
- **Finer-grained control**: More options for acquiring and releasing locks, handling interruptions, and managing waiting conditions.
- **Improved performance**: In high-contention scenarios, they can sometimes outperform `synchronized` due to more optimized underlying implementations (e.g., using CAS operations).
- **Greater flexibility**: Support for multiple condition variables, separate read/write locks, and optimistic locking.

## The `Lock` Interface and `Condition` Interface
The `Lock` interface is the core abstraction in the `java.util.concurrent.locks` package, providing a more general-purpose locking capability than `synchronized` methods and statements. The `Condition` interface allows threads to wait for specific conditions to be met, serving as a replacement for `Object.wait()` and `Object.notify()`.

### The `Lock` Interface
#### Overview
The `Lock` interface defines methods for acquiring and releasing locks. Its implementations provide more sophisticated control over locking mechanisms.

#### Key Methods:
- `void lock()`: Acquires the lock. If the lock is not available, the current thread is disabled for thread scheduling purposes and lies dormant until the lock has been acquired. (Equivalent to entering a `synchronized` block).
- `void lockInterruptibly() throws InterruptedException`: Acquires the lock unless the current thread is interrupted.
- `boolean tryLock()`: Attempts to acquire the lock without blocking. Returns `true` on success, `false` otherwise.
- `boolean tryLock(long time, TimeUnit unit) throws InterruptedException`: Attempts to acquire the lock within a given waiting time. Returns true on success, false if the timeout expires.
- `void unlock()`: Releases the lock. (Equivalent to exiting a `synchronized` block). Crucially, `unlock()` must be called in a `finally` block to ensure the lock is always released, even if an exception occurs.

#### Importance
Provides explicit control over locking, enabling features like timed lock acquisition and interruptible locks, which are not possible with `synchronized`

### The `Condition` Interface
#### Overview
A `Condition` instance is bound to a `Lock` object. It provides a means for one thread to suspend execution (to wait) until notified by another thread that some state condition may now be true.

#### Key Methods:
- `void await() throws InterruptedException`: Causes the current thread to wait until it is signalled or interrupted. Releases the lock associated with this `Condition`.
- `void awaitUninterruptibly()`: Causes the current thread to wait until signalled, without being interrupted.
- `boolean await(long time, TimeUnit unit) throws InterruptedException`: Causes the current thread to wait until it is signalled, interrupted, or the specified waiting time elapses.
- `void signal()`: Wakes up one waiting thread.
- `void signalAll()`: Wakes up all waiting threads

#### Importance
Replaces `Object.wait()` and `Object.notify()` for `Lock` implementations. A single `Lock` can have multiple `Condition` objects, allowing threads to wait for different conditions on the same shared resource, leading to more flexible and efficient notification patterns (e.g., separate conditions for "buffer not empty" and "buffer not full").

## `ReentrantLock` : A More Powerful `synchronized`
`ReentrantLock` is an implementation of the `Lock` interface that provides the same mutual exclusion capabilities as `synchronized`, but with extended features. "Reentrant" means that a thread that already holds the lock can acquire it again without blocking itself. This is also true for `synchronized` blocks.

### Overview and Importance
`ReentrantLock` is often preferred over `synchronized` when you need more control, flexibility, or want to avoid some of the performance overheads of intrinsic locks in high-contention scenarios (though performance differences can be subtle and workload-dependent).

#### Features beyond `synchronized`
- **Fairness**: Can be constructed with a `fair` parameter (`true` for fair, `false` for non-fair, default is `false`). A fair lock grants access to the longest-waiting thread. Non-fair locks might grant access to a newly requesting thread if the lock becomes available, potentially leading to "barging" (a newly arrived thread jumping ahead of waiting threads) and better throughput.
- **Interruptible lock acquisition**: `lockInterruptibly()`.
- **Non-blocking lock acquisition**: `tryLock()` and `tryLock(timeout)`.
- **Multiple** `Condition` **objects**: Can create multiple `Condition` instances associated with a single `ReentrantLock`.

### Syntax, Use Cases, and Implementation
#### Beginner Level: Basic `ReentrantLock`
```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

// Beginner Example: Basic ReentrantLock (similar to synchronized)
class CounterWithLock {
    private int count = 0;
    private final Lock lock = new ReentrantLock(); // Non-fair by default

    public void increment() {
        lock.lock(); // Acquire the lock
        try {
            count++;
            System.out.println(Thread.currentThread().getName() + " incremented to: " + count);
        } finally {
            lock.unlock(); // Release the lock in a finally block
        }
    }

    public int getCount() {
        return count;
    }

    public static void main(String[] args) throws InterruptedException {
        CounterWithLock sharedCounter = new CounterWithLock();

        Runnable incrementTask = () -> {
            for (int i = 0; i < 5; i++) {
                sharedCounter.increment();
                try {
                    Thread.sleep(10); // Simulate some work
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread t1 = new Thread(incrementTask, "Thread-A");
        Thread t2 = new Thread(incrementTask, "Thread-B");

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final Count with ReentrantLock: " + sharedCounter.getCount());
    }
}
```

**Explanation**: This example directly replaces the `synchronized` keyword with `ReentrantLock`. The `lock.lock()` acquires the lock, and `lock.unlock()` releases it. The finally block is crucial to ensure the lock is always released, preventing deadlocks. The functionality here is equivalent to a `synchronized` method/block.

#### Intermediate Level: `tryLock()` and Fairness
```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

// Intermediate Example: tryLock() and Fairness
class ATM {
    private double balance = 1000.0;
    private final Lock atmLock = new ReentrantLock(true); // Fair lock

    public boolean withdraw(double amount) {
        // Try to acquire the lock with a timeout
        try {
            if (atmLock.tryLock(500, TimeUnit.MILLISECONDS)) { // Wait up to 500ms
                try {
                    if (balance >= amount) {
                        System.out.println(Thread.currentThread().getName() + " trying to withdraw " + amount);
                        Thread.sleep(100); // Simulate processing
                        balance -= amount;
                        System.out.println(Thread.currentThread().getName() + " withdrew " + amount + ". New balance: " + balance);
                        return true;
                    } else {
                        System.out.println(Thread.currentThread().getName() + " tried to withdraw " + amount + " but insufficient funds.");
                        return false;
                    }
                } finally {
                    atmLock.unlock();
                }
            } else {
                System.out.println(Thread.currentThread().getName() + " could not acquire ATM lock within timeout. Try again later.");
                return false;
            }
        } catch (InterruptedException e) {
            System.out.println(Thread.currentThread().getName() + " was interrupted while waiting for ATM lock.");
            Thread.currentThread().interrupt();
            return false;
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ATM sharedATM = new ATM();

        Runnable withdrawTask = () -> {
            for (int i = 0; i < 3; i++) {
                sharedATM.withdraw(300);
                try {
                    Thread.sleep(Math.random() > 0.5 ? 50 : 150); // Random delay
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread customer1 = new Thread(withdrawTask, "Customer-1");
        Thread customer2 = new Thread(withdrawTask, "Customer-2");
        Thread customer3 = new Thread(withdrawTask, "Customer-3");

        customer1.start();
        customer2.start();
        customer3.start();

        customer1.join();
        customer2.join();
        customer3.join();

        System.out.println("Final ATM Balance: " + sharedATM.balance);
    }
}
```

**Explanation**: <br>
This example uses `atmLock.tryLock(500, TimeUnit.MILLISECONDS)`. If a thread cannot acquire the lock within 500 milliseconds, it gives up and prints a message, rather than blocking indefinitely. This is useful for preventing deadlocks or ensuring responsiveness. The `ReentrantLock(true)` constructor creates a fair lock, meaning threads acquire the lock in roughly the order they requested it, which can reduce starvation but might lower overall throughput

#### Advanced Level: Multiple Conditions (Producer - Consumer with `Condition`)
```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

// Advanced Example: Producer-Consumer with ReentrantLock and Conditions
class MessageQueue {
    private final Queue<String> queue = new LinkedList<>();
    private final int CAPACITY = 5;
    private final Lock lock = new ReentrantLock();
    // Conditions associated with this lock
    private final Condition notEmpty = lock.newCondition(); // For consumers to wait when queue is empty
    private final Condition notFull = lock.newCondition();  // For producers to wait when queue is full

    public void put(String message) throws InterruptedException {
        lock.lock(); // Acquire the lock
        try {
            while (queue.size() == CAPACITY) {
                System.out.println(Thread.currentThread().getName() + " Queue is FULL. Waiting...");
                notFull.await(); // Producer waits here, releases the lock
            }
            queue.add(message);
            System.out.println(Thread.currentThread().getName() + " Produced: " + message + ". Queue size: " + queue.size());
            notEmpty.signalAll(); // Signal all waiting consumers that queue is not empty
        } finally {
            lock.unlock(); // Release the lock
        }
    }

    public String take() throws InterruptedException {
        lock.lock(); // Acquire the lock
        try {
            while (queue.isEmpty()) {
                System.out.println(Thread.currentThread().getName() + " Queue is EMPTY. Waiting...");
                notEmpty.await(); // Consumer waits here, releases the lock
            }
            String message = queue.remove();
            System.out.println(Thread.currentThread().getName() + " Consumed: " + message + ". Queue size: " + queue.size());
            notFull.signalAll(); // Signal all waiting producers that queue is not full
            return message;
        } finally {
            lock.unlock(); // Release the lock
        }
    }
}

class ProducerC implements Runnable {
    private MessageQueue mq;

    public ProducerC(MessageQueue mq) {
        this.mq = mq;
    }

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            try {
                mq.put("Message-" + i);
                Thread.sleep(100);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
    }
}

class ConsumerC implements Runnable {
    private MessageQueue mq;

    public ConsumerC(MessageQueue mq) {
        this.mq = mq;
    }

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            try {
                String message = mq.take();
                Thread.sleep(200); // Simulate processing time
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
    }
}

public class ProducerConsumerWithConditions {
    public static void main(String[] args) throws InterruptedException {
        MessageQueue mq = new MessageQueue();

        Thread producerThread = new Thread(new ProducerC(mq), "Producer");
        Thread consumerThread = new Thread(new ConsumerC(mq), "Consumer");

        producerThread.start();
        consumerThread.start();

        producerThread.join();
        consumerThread.join();

        System.out.println("Producer and Consumer finished.");
    }
}
```

#### Explanation
This producer-consumer example uses `ReentrantLock` and two `Condition` objects: `notEmpty` and `notFull`.
- `notFull.await()`: Producers wait on this condition when the queue is full. When a consumer takes an item, it signals `notFull.signalAll()`.
- `notEmpty.await()`: Consumers wait on this condition when the queue is empty. When a producer puts an item, it signals `notEmpty.signalAll()`. This clear separation of concerns (different conditions for different waiting scenarios) makes the code more readable and can improve performance compared to a single `wait()`/`notifyAll()` on `Object`, especially if there are many different types of threads waiting for various conditions.

### Common Pitfalls with `ReentrantLock`
- **Forgetting** `unlock()`: The most common and dangerous pitfall. If unlock() is not called (especially not in a finally block), the lock will never be released, leading to a permanent deadlock.
- **Acquiring the wrong lock**: If `lock()` is called on one instance and `unlock()` on another, the lock will never truly be released.
- **Deadlock with** `lock()`: Similar to `synchronized`, improper lock ordering can lead to deadlocks. `tryLock()` with timeouts can help mitigate this.
- **Ignoring** `InterruptedException`: If using `lockInterruptibly()`, it's important to properly handle or re-interrupt the thread if an `InterruptedException` occurs.
- **Fairness vs. Throughput**: Choosing `ReentrantLock(true)` for fairness can sometimes decrease overall throughput, as it introduces overhead to manage the waiting queue. The default non-fair lock often performs better in high-contention scenarios.

### Best Practices for `ReentrantLock`
- Always use `finally` for `unlock()`: This is non-negotiable.
  ```java
  lock.lock();
  try {
    // Critical section
  } finally {
    lock.unlock();
  }
  ```
  
- **Consistent Lock Ordering**: If acquiring multiple `ReentrantLock` instances, always acquire them in the same order across all threads to prevent deadlocks.
- **Prefer** `tryLock()` **with timeout**: For scenarios where indefinite blocking is unacceptable, use `tryLock(long timeout, TimeUnit unit)`.
  **One** `ReentrantLock` **per shared resource**: Typically, you'd associate one `ReentrantLock` with a single shared resource or a set of closely related resources that need mutual exclusion.
  **Choose Fairness Wisely**: Use fair locks only if you have a strict requirement to prevent thread starvation. In most cases, the default non-fair lock offers better performance.
  **Use** `Condition` **objects for complex waiting**: Leverage `Condition` objects for sophisticated producer-consumer patterns or other scenarios where threads wait for specific state changes.

## `ReentrantReadWriteLock`: Optimizing for Reels
`ReentrantReadWriteLock` is another implementation of the `Lock` interface that allows for a more flexible locking strategy than mutual exclusion. It maintains a pair of associated `Lock`s: one for read-only operations and one for write operations

### Overview and Importance
The key idea is that multiple threads can read shared data concurrently, but only one thread can write to it at a time. If a writer is present, no readers or other writers can acquire the lock. This is crucial for performance in read-heavy applications.

#### Rules:
- **Read Lock (shared lock)**: Can be held by multiple reader threads concurrently, as long as no writer thread holds the write lock.
- **Write Lock (exclusive lock)**: Can only be held by one writer thread at a time. When a writer holds the write lock, no readers or other writers can acquire their respective locks.

#### Importance:
Significantly improves concurrency for data structures that are frequently read but infrequently modified (e.g., caches, configuration data, shared lookup tables).

#### Syntax
```java
ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
Lock readLock = rwLock.readLock();  // Get the read lock
Lock writeLock = rwLock.writeLock(); // Get the write lock
```

Then use `readLock.lock()/unlock()` for read operations and `writeLock.lock()/unlock()` for write operations, always in a `finally` block.

### Syntax, Use Cases, and Implementation
#### Beginner Level: Basic Read/Write Operations
```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReentrantReadWriteLock;
import java.util.concurrent.locks.Lock;

// Beginner Example: Basic ReentrantReadWriteLock
class SharedCache {
    private final Map<String, String> cache = new HashMap<>();
    private final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();

    public String get(String key) {
        readLock.lock(); // Acquire read lock
        try {
            System.out.println(Thread.currentThread().getName() + " reading key: " + key);
            Thread.sleep(50); // Simulate read time
            return cache.get(key);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            return null;
        } finally {
            readLock.unlock(); // Release read lock
        }
    }

    public void put(String key, String value) {
        writeLock.lock(); // Acquire write lock
        try {
            System.out.println(Thread.currentThread().getName() + " writing key: " + key + ", value: " + value);
            Thread.sleep(100); // Simulate write time
            cache.put(key, value);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            writeLock.unlock(); // Release write lock
        }
    }

    public static void main(String[] args) throws InterruptedException {
        SharedCache sharedCache = new SharedCache();

        Runnable reader = () -> {
            for (int i = 0; i < 5; i++) {
                String value = sharedCache.get("data");
                System.out.println(Thread.currentThread().getName() + " got value: " + value);
                try {
                    Thread.sleep(20);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Runnable writer = () -> {
            for (int i = 0; i < 2; i++) {
                sharedCache.put("data", "value-" + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread writerThread = new Thread(writer, "Writer-1");
        Thread reader1 = new Thread(reader, "Reader-1");
        Thread reader2 = new Thread(reader, "Reader-2");

        writerThread.start();
        reader1.start();
        reader2.start();

        writerThread.join();
        reader1.join();
        reader2.join();

        System.out.println("Final cache state: " + sharedCache.cache);
    }
}
```

**Explanation**: Notice how multiple reader threads can execute `get()` concurrently. However, when `writerThread` calls `put()`, both readers will be blocked until the writer releases the write lock. This demonstrates the core benefit: allowing parallel reads while maintaining exclusive writes.

#### Intermediate Level: Read Lock Downgrading
A common pattern is to hold a write lock, perform a write, and then immediately acquire a read lock while still holding the write lock before releasing the write lock. This ensures consistency for the subsequent read

```java
import java.util.concurrent.locks.ReentrantReadWriteLock;
import java.util.concurrent.locks.Lock;

// Intermediate Example: Read Lock Downgrading
class ConfigManager {
    private String configValue = "Initial Config";
    private final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();

    public String getConfig() {
        readLock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " reading config: " + configValue);
            return configValue;
        } finally {
            readLock.unlock();
        }
    }

    public void updateConfig(String newConfig) {
        writeLock.lock(); // Acquire write lock
        try {
            System.out.println(Thread.currentThread().getName() + " acquiring write lock to update config.");
            this.configValue = newConfig;
            System.out.println(Thread.currentThread().getName() + " config updated to: " + newConfig);

            // Downgrading: Acquire read lock before releasing write lock
            readLock.lock(); // This will succeed because current thread holds the write lock
            System.out.println(Thread.currentThread().getName() + " Write lock downgraded to read lock.");
        } finally {
            writeLock.unlock(); // Release write lock
        L:  // Now only read lock is held by this thread
            System.out.println(Thread.currentThread().getName() + " Write lock released. Still holding read lock.");
        }
        // ... now perform operations under read lock ...
        try {
            String currentConfig = getConfig(); // This will use the already held readLock
            System.out.println(Thread.currentThread().getName() + " Verified config after update: " + currentConfig);
        } finally {
            readLock.unlock(); // Release the read lock
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ConfigManager manager = new ConfigManager();

        Runnable updater = () -> {
            try {
                Thread.sleep(50); // Give readers a head start
                manager.updateConfig("New Config " + System.currentTimeMillis());
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        Runnable reader = () -> {
            for (int i = 0; i < 3; i++) {
                manager.getConfig();
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread updaterThread = new Thread(updater, "Updater");
        Thread reader1 = new Thread(reader, "Reader-1");
        Thread reader2 = new Thread(reader, "Reader-2");

        reader1.start();
        reader2.start();
        updaterThread.start();

        reader1.join();
        reader2.join();
        updaterThread.join();

        System.out.println("Final Config: " + manager.getConfig());
    }
}
```

**Explanation**: <br>
The `updateConfig` method first acquires the write lock. After updating the config, it acquires the read lock before releasing the write lock. This is called "downgrading" from a write lock to a read lock. It ensures that the thread can continue to read the newly updated value without contention, and also ensures that no other writer can jump in between the update and the subsequent read verification. Finally, the write lock is released, and then the read lock.

#### Advanced Level: Read Lock Upgrading (Impossible with `ReentrantReadWriteLock`)
It's important to understand that ReentrantReadWriteLock does not support upgrading a read lock to a write lock directly. If a thread holds a read lock and then attempts to acquire the write lock, it will deadlock, because the write lock cannot be acquired while any read locks are held, and the current thread is holding one. You would need to release the read lock first, then acquire the write lock, but this opens a window for other threads to modify the data.

This is where `StampedLock` offers more flexibility

### Common Pitfalls with `ReentrantReadWriteLock`
- **No Read Lock Upgrading**: Attempting to upgrade a read lock to a write lock will lead to deadlock.
- **Incorrect Lock Usage**: Using the read lock for write operations (or vice versa) defeats the purpose and breaks thread safety.
- **Forgetting** `unlock()`: Just like `ReentrantLock`, failure to release locks in a `finally` block leads to deadlocks.
- **Starvation**: Writers can starve readers or vice versa, especially with the non-fair mode (default). For example, if there's a continuous stream of writes, readers might never get a chance. The fair mode can mitigate this but might reduce throughput

### Best Practices for `ReentrantReadWriteLock`
- **Separate Read and Write Operations**: Clearly delineate methods that only read from those that modify.
- **Always use** `finally` **for** `unlock()`: Ensure locks are released.
- **Consider fairness**: If writer starvation is a concern, consider a fair `ReentrantReadWriteLock(true)`. However, often the performance cost of fairness outweighs the benefit unless starvation is a critical functional requirement.
- **Downgrading is possible and useful**: From write to read lock.
- **Upgrading is not possible**: If you need to upgrade, you must release the read lock and then acquire the write lock, leaving a window of vulnerability. For this, `StampedLock` might be a better choice.

## `StampedLock`: The Next Generation
`StampedLock` (introduced in Java 8) is a more advanced read-write lock that offers three modes for controlling read/write access:
1. **Writing**: Exclusive and blocking.
2. **Reading**: Non-exclusive and blocking.
3. **Optimistic Reading**: Non-exclusive and non-blocking, but requires validation

### Overview and Importance
`StampedLock` provides a performance advantage over `ReentrantReadWriteLock` in many scenarios, especially for read-heavy workloads, by introducing **optimistic reading**

#### Key Features:
- **Stamps**: All lock acquisition methods return a `long` value called a "stamp." This stamp represents the current state of the lock. To release the lock, you must provide the correct stamp.
- **Exclusive Write Lock (**`writeLock()`**)**: Behaves like `ReentrantReadWriteLock`'s write lock. Returns a stamp.
- **Shared Read Lock (**`readLock()`**)**: Behaves like `ReentrantReadWriteLock`'s read lock. Returns a stamp.
- **Optimistic Read Lock (**`tryOptimisticRead()`**)**: The most innovative feature. It's a non-blocking read that doesn't acquire any lock. It returns a stamp if the lock is not currently held for writing, or zero if it is. After performing the read operation, you must `validate()` the stamp. If validation fails, it means a write occurred during the read, and you need to re-read under a full read lock (or write lock, if modification is needed).
- **Lock Upgrading/Downgrading**: `StampedLock` supports both downgrading a write lock to a read lock, and attempting to upgrade a read lock to a write lock (`tryConvertToWriteLock()`).

#### Importance
`StampedLock` aims to maximize concurrency by allowing optimistic reads, which incur almost no overhead when there are no writes. This makes it ideal for highly concurrent, read-dominant applications where occasional re-reads are acceptable.

### Syntax, Use Cases, and Implementation
#### Beginner Level: Basic `StampedLock` (Write and Read)
```java
import java.util.concurrent.locks.StampedLock;

// Beginner Example: Basic StampedLock (Write and Read)
class Coordinate {
    private double x, y;
    private final StampedLock lock = new StampedLock();

    public void move(double deltaX, double deltaY) {
        long stamp = lock.writeLock(); // Acquire write lock
        try {
            System.out.println(Thread.currentThread().getName() + " acquired write lock.");
            Thread.sleep(50); // Simulate write operation
            x += deltaX;
            y += deltaY;
            System.out.println(Thread.currentThread().getName() + " moved to (" + x + ", " + y + ")");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            lock.unlockWrite(stamp); // Release write lock with the stamp
            System.out.println(Thread.currentThread().getName() + " released write lock.");
        }
    }

    public double distanceFromOrigin() {
        long stamp = lock.readLock(); // Acquire read lock
        try {
            System.out.println(Thread.currentThread().getName() + " acquired read lock.");
            Thread.sleep(20); // Simulate read operation
            return Math.sqrt(x * x + y * y);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            return 0.0;
        } finally {
            lock.unlockRead(stamp); // Release read lock with the stamp
            System.out.println(Thread.currentThread().getName() + " released read lock.");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Coordinate p = new Coordinate();

        Runnable writer = () -> {
            for (int i = 0; i < 3; i++) {
                p.move(1.0, 1.0);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Runnable reader = () -> {
            for (int i = 0; i < 5; i++) {
                double distance = p.distanceFromOrigin();
                System.out.println(Thread.currentThread().getName() + " calculated distance: " + String.format("%.2f", distance));
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread writerThread = new Thread(writer, "Writer-1");
        Thread reader1 = new Thread(reader, "Reader-1");
        Thread reader2 = new Thread(reader, "Reader-2");

        writerThread.start();
        reader1.start();
        reader2.start();

        writerThread.join();
        reader1.join();
        reader2.join();

        System.out.println("Final coordinates: (" + p.x + ", " + p.y + ")");
    }
}
```

**Explanation**: This example shows the basic `writeLock()` and `readLock()` usage, similar to `ReentrantReadWriteLock`. The key difference is that `lock()` methods return a stamp, which must be passed to the corresponding `unlock()` method.

#### Intermediate Level: Optimistic Reading
```java
import java.util.concurrent.locks.StampedLock;

// Intermediate Example: Optimistic Reading with StampedLock
class Point {
    private double x, y;
    private final StampedLock sl = new StampedLock();

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // A method for calculating distance using optimistic read
    public double distanceFromOriginOptimistic() {
        long stamp = sl.tryOptimisticRead(); // Try optimistic read
        double currentX = x; // Read x
        double currentY = y; // Read y

        // Validate the stamp after reading
        if (!sl.validate(stamp)) { // If validation fails, a write occurred during read
            System.out.println(Thread.currentThread().getName() + " Optimistic read failed. Acquiring full read lock.");
            stamp = sl.readLock(); // Acquire a full read lock
            try {
                currentX = x; // Re-read x
                currentY = y; // Re-read y
            } finally {
                sl.unlockRead(stamp); // Release the full read lock
            }
        }
        System.out.println(Thread.currentThread().getName() + " Optimistic/Full read successful: (" + currentX + ", " + currentY + ")");
        return Math.sqrt(currentX * currentX + currentY * currentY);
    }

    public void move(double deltaX, double deltaY) {
        long stamp = sl.writeLock();
        try {
            System.out.println(Thread.currentThread().getName() + " Moving point...");
            Thread.sleep(100); // Simulate write time
            x += deltaX;
            y += deltaY;
            System.out.println(Thread.currentThread().getName() + " Point moved to (" + x + ", " + y + ")");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            sl.unlockWrite(stamp);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Point p = new Point(0, 0);

        Runnable reader = () -> {
            for (int i = 0; i < 5; i++) {
                double distance = p.distanceFromOriginOptimistic();
                System.out.println(Thread.currentThread().getName() + " calculated distance: " + String.format("%.2f", distance));
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Runnable writer = () -> {
            for (int i = 0; i < 2; i++) {
                p.move(10.0, 10.0);
                try {
                    Thread.sleep(200); // Slower writer
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread reader1 = new Thread(reader, "Reader-1");
        Thread reader2 = new Thread(reader, "Reader-2");
        Thread writerThread = new Thread(writer, "Writer-1");

        reader1.start();
        reader2.start();
        writerThread.start();

        reader1.join();
        reader2.join();
        writerThread.join();

        System.out.println("Final point: (" + p.x + ", " + p.y + ")");
    }
}
```

**Explanation**: <br>
The `distanceFromOriginOptimistic()` method first attempts an `optimistic` read. If the `validate()` call returns `true`, it means no write happened during the read, and the read values are consistent. If `validate()` returns `false`, a write occurred, and the read is invalid. In this case, the method acquires a full `readLock()`, re-reads the values, and then unlocks. This pattern is highly efficient for workloads with very few writes

#### Advanced Level: Upgrading Read Lock to Write Lock
```java
import java.util.concurrent.locks.StampedLock;

// Advanced Example: Upgrading Read Lock to Write Lock with StampedLock
class CacheItem {
    private String data = "initial";
    private int version = 0; // Simulate versioning for cache invalidation
    private final StampedLock sl = new StampedLock();

    // Read operation (optimistic first, then full read if needed)
    public String readData() {
        long stamp = sl.tryOptimisticRead();
        String currentData = data;
        int currentVersion = version;

        if (!sl.validate(stamp)) {
            // Optimistic read failed, acquire full read lock
            stamp = sl.readLock();
            try {
                currentData = data;
                currentVersion = version;
            } finally {
                sl.unlockRead(stamp);
            }
        }
        System.out.println(Thread.currentThread().getName() + " (Read) Data: " + currentData + ", Version: " + currentVersion);
        return currentData;
    }

    // Try to update data, possibly upgrading from a read lock
    public void updateDataConditionally(String newData) {
        long stamp = sl.readLock(); // Start with a read lock
        try {
            if (data.equals(newData)) {
                System.out.println(Thread.currentThread().getName() + " Data is already " + newData + ". No update needed.");
                return;
            }
            // Try to upgrade to write lock. If successful, we don't need to re-read.
            long writeStamp = sl.tryConvertToWriteLock(stamp);
            if (writeStamp != 0L) { // Successfully upgraded
                stamp = writeStamp;
                System.out.println(Thread.currentThread().getName() + " Successfully upgraded to write lock.");
                this.data = newData;
                this.version++;
                System.out.println(Thread.currentThread().getName() + " Data updated to: " + data + ", Version: " + version);
            } else {
                // Could not upgrade, release read lock and acquire write lock from scratch
                System.out.println(Thread.currentThread().getName() + " Could not upgrade. Releasing read lock, acquiring write lock.");
                sl.unlockRead(stamp); // Release the read lock
                stamp = sl.writeLock(); // Acquire a new write lock
                try {
                    // Re-check condition after acquiring write lock
                    if (data.equals(newData)) { // Another thread might have updated it
                        System.out.println(Thread.currentThread().getName() + " Data already updated by another. Aborting update.");
                        return;
                    }
                    this.data = newData;
                    this.version++;
                    System.out.println(Thread.currentThread().getName() + " Data updated after full write lock: " + data + ", Version: " + version);
                } finally {
                    sl.unlockWrite(stamp);
                }
            }
        } finally {
            // Ensure any held lock (read or write from upgrade path) is released
            if (StampedLock.isWriteLockStamp(stamp)) { // Check if it's a write stamp
                sl.unlockWrite(stamp);
            } else { // It must be a read stamp (or an invalid stamp which is fine)
                sl.unlockRead(stamp);
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        CacheItem cache = new CacheItem();

        Runnable readerTask = () -> {
            for (int i = 0; i < 3; i++) {
                cache.readData();
                try { Thread.sleep(50); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
            }
        };

        Runnable updaterTask = () -> {
            for (int i = 0; i < 2; i++) {
                String newData = "UPDATED-" + i;
                cache.updateDataConditionally(newData);
                try { Thread.sleep(150); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
            }
        };

        Thread reader1 = new Thread(readerTask, "Reader-1");
        Thread reader2 = new Thread(readerTask, "Reader-2");
        Thread updater1 = new Thread(updaterTask, "Updater-1");
        Thread updater2 = new Thread(updaterTask, "Updater-2");

        reader1.start();
        reader2.start();
        updater1.start();
        updater2.start();

        reader1.join();
        reader2.join();
        updater1.join();
        updater2.join();

        System.out.println("Final cache data: " + cache.readData());
    }
}
```

**Explanation**:
The `updateDataConditionally` method shows the `tryConvertToWriteLock(stamp)` mechanism.
1. It initially acquires a `readLock`.
2. It then checks a condition (`data.equals(newData)`).
3. If an update is needed, it attempts to "upgrade" the read lock to a write lock using `sl.tryConvertToWriteLock(stamp)`.
4. If the upgrade is successful (returns a non-zero stamp), the current thread now holds the write lock, and the update can proceed.
5. If the upgrade fails (returns `0L`), it means another writer acquired the lock, or there are other active readers preventing the upgrade. In this case, the thread must release its read lock, then acquire a full write lock, and re-check the condition before proceeding with the update.

This `tryConvertToWriteLock` is powerful for cases where a read-only path might transition to a write path conditionally, minimizing contention.

### Common Pitfalls with `StampedLock`
- **Complexity**: `StampedLock` is significantly more complex to use correctly than `ReentrantLock` or `ReentrantReadWriteLock` due to the stamp management and optimistic read validation.
- **Forgetting** `unlock()`: Failing to unlock (using the correct stamp!) will lead to deadlocks.
- **Incorrect** `validate()`: Forgetting to `validate()` an optimistic read or misinterpreting its result leads to data inconsistency.
- **Non-reentrant by default (mixed modes)**: A thread holding a read lock cannot acquire a write lock directly without releasing the read lock (unless using `tryConvertToWriteLock`). A thread holding a write lock can acquire a read lock. This is different from `ReentrantReadWriteLock` where read/write locks are individually reentrant.
- **No support for Conditions**: `StampedLock` does not support the `Condition` interface. If you need explicit waiting/signaling, you might need to combine `StampedLock` with other `java.util.concurrent` utilities or fall back to ReentrantLock.
- **Starvation**: Can still lead to writer starvation if there are continuous readers and `tryConvertToWriteLock` repeatedly fails.

### Best Practices for `StampedLock`
- **Use When Performance is Critical**: Only use `StampedLock` when profiling indicates that ReentrantReadWriteLock is a bottleneck and you have a very read-heavy workload.
- **Prioritize Optimistic Reads**: The primary benefit of `StampedLock` is the optimistic read. Design your code to leverage this as much as possible, falling back to full read locks only when validation fails.
- **Careful Stamp Management**: Always store the stamp returned by `lock()` methods and pass it to the corresponding `unlock()` method.
- `try...finally` for Unlocking: Always ensure `unlock` calls are in a `finally` block.
- **Understand** `tryConvertToWriteLock()` **nuances**: Be aware that it's an attempt to upgrade, and if it fails, you must revert to a full write lock acquisition and re-check conditions.
- **Prefer simpler locks first**: For most general-purpose applications, `synchronized` or `ReentrantLock` are sufficient and easier to use correctly.
- **Avoid for conditional waiting**: If you need `Condition` functionality, `StampedLock` is not the right choice.

## Performance Considerations
- `synchronized`: Generally performs well for low to moderate contention. JVM optimizations (e.g., biased locking, thin locks) make it very efficient. Can be slower in high contention due to OS-level mutex contention.
- `ReentrantLock`: Can outperform `synchronized` in high contention due to its user-space implementation and often using Compare-And-Swap (CAS) operations. Fairness introduces overhead.
- `ReentrantReadWriteLock`: Provides a significant performance boost for read-heavy workloads by allowing concurrent reads. The overhead comes from managing two internal locks and the read/write state.
- `StampedLock`: Offers the best read performance in very read-heavy scenarios due to optimistic reads having minimal overhead. However, its write operations are relatively more expensive than `ReentrantReadWriteLock`'s writes, and failed optimistic reads incur the cost of re-reading under a full lock. Its complexity can also lead to more difficult-to-diagnose performance issues if not used correctly.

---

# Introduction: The Drive for High Concurrency
In multi-threaded programming, shared mutable state is the root of many problems, including race conditions, deadlocks, and data inconsistency. Traditional synchronization mechanisms like the `synchronized` keyword and `java.util.concurrent.locks.Lock` implementations provide mutual exclusion, ensuring that only one thread can access a critical section at a time. While effective, these "blocking" mechanisms have drawbacks:
- **Overhead**: Acquiring and releasing locks involves context switching and potentially entering kernel mode, which can be costly.
- **Contention**: When many threads try to acquire the same lock, they block, leading to reduced parallelism and degraded performance.
- **Deadlocks**: Incorrect lock ordering or forgetting to release locks can lead to situations where threads indefinitely wait for each other.
- **Livelocks/Starvation**: Threads might repeatedly try to acquire a lock but never succeed, or some threads might get repeatedly bypassed by others.

**Lock-Free (Concurrency)** (also known as non-blocking concurrency) offers an alternative approach. Instead of blocking threads, it relies on low-level atomic operations (primarily **Compare-and-Swap - CAS**) provided by modern CPUs. The goal is to allow threads to make progress without being held up by other threads, even if those threads are suspended or crash.

#### Why is it important?
- **Increased Throughput**: By reducing or eliminating blocking, more threads can make progress concurrently, leading to higher system throughput, especially on multi-core processors.
- **Reduced Latency**: Operations can complete faster as threads don't wait for locks.
- **Immunity to Deadlocks**: Since no explicit locks are held, lock-free algorithms are inherently immune to deadlocks caused by lock contention.
- **Resilience to Thread Suspension**: If a thread performing a lock-free operation is suspended, other threads can still make progress. This is not true for lock-based systems where suspending a thread holding a lock can halt the entire application

While lock-free algorithms are powerful, they are often more complex to design and implement correctly than lock-based ones. Java provides the `volatile` keyword and classes in `java.util.concurrent.atomic` package to facilitate lock-free programming

## Volatile Variables: Ensuring Visibility
The `volatile` keyword is a lightweight synchronization mechanism that addresses the visibility problem in multi-threaded environments. It does not guarantee atomicity for compound operations, but it ensures that changes to a variable are immediately visible to all threads.

### Overview and Importance
In a multi-core CPU architecture, each CPU core has its own cache. When a thread modifies a variable, the change might initially be stored only in that thread's local CPU cache, not immediately written back to main memory. Other threads running on different cores might read an outdated value from their own caches.

The `volatile` keyword solves this:
- **Visibility**: When a variable is declared `volatile`, any write to it is immediately written to main memory, and any read from it is always read from main memory (bypassing CPU caches). This ensures that all threads see the most up-to-date value.
- **Ordering Guarantees (Memory Barriers)**: `volatile` also prevents the compiler and CPU from reordering instructions around `volatile` variable accesses. Specifically:
  - A write to a `volatile` variable happens-before any subsequent read of that variable.
  - Reads and writes of other non-volatile variables that occur before a `volatile` write are guaranteed to be flushed to main memory before the `volatile` write occurs.
  - Reads and writes of other non-volatile variables that occur after a `volatile` read are guaranteed to happen after the `volatile` read, which has already read the latest value from main memory.

**Importance**: `volatile` is crucial for flags, status indicators, or other single variables where one thread writes and other threads primarily read, and immediate visibility of the change is critical.

### Syntax, Use Cases, and Implementation
#### Syntax
Simply prepend the `volatile` keyword to a variable declaration:
```java
public volatile boolean stopRequested;
public volatile int counter; // NOT ATOMIC for increment, only visible
```

#### Beginner Level: Simple Flag for Thread Control
```java
// Beginner Example: volatile for a control flag
class Worker implements Runnable {
    private volatile boolean stopRequested = false; // Ensures visibility of stopRequested

    public void requestStop() {
        stopRequested = true; // Write to volatile variable
    }

    public boolean isStopRequested() {
        return stopRequested; // Read from volatile variable
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " started.");
        while (!stopRequested) { // Repeatedly checks the volatile flag
            // Simulate some work
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                // If interrupted, also consider stopping
                stopRequested = true;
            }
        }
        System.out.println(Thread.currentThread().getName() + " stopped.");
    }

    public static void main(String[] args) throws InterruptedException {
        Worker worker = new Worker();
        Thread workerThread = new Thread(worker, "WorkerThread");

        workerThread.start();

        // Let the worker run for a bit
        Thread.sleep(500);

        // Request the worker thread to stop
        System.out.println(Thread.currentThread().getName() + " requesting worker to stop.");
        worker.requestStop(); // This change will be immediately visible

        workerThread.join(); // Wait for the worker thread to finish
        System.out.println(Thread.currentThread().getName() + " Worker thread finished.");
    }
}
```

**Explanation**: <br>
Without `volatile`, the `WorkerThread` might cache the `stopRequested` flag as `false` and never see the update made by the `main` thread, leading to an infinite loop. volatile ensures that when main thread sets `stopRequested = true`, `WorkerThread` immediately sees this updated value, allowing it to terminate gracefully

#### Intermediate Level: Visibility of Other Variables (Happens-Before)
```java
// Intermediate Example: volatile's happens-before guarantee for other variables
class Config {
    private int configValue = 0;
    private String configName = "Default";
    private volatile boolean initialized = false; // Volatile flag for visibility and ordering

    public void initialize(int value, String name) {
        configValue = value; // Write to non-volatile
        configName = name;   // Write to non-volatile
        // The write to 'initialized' acts as a memory barrier.
        // All writes *before* it are guaranteed to be visible *after* it.
        initialized = true;  // Write to volatile
        System.out.println(Thread.currentThread().getName() + " initialized config. configValue=" + configValue + ", configName=" + configName);
    }

    public void displayConfig() {
        // The read of 'initialized' acts as a memory barrier.
        // All reads *after* it are guaranteed to see values written *before* it.
        if (initialized) { // Read from volatile
            System.out.println(Thread.currentThread().getName() + " Displaying config: Value=" + configValue + ", Name=" + configName);
        } else {
            System.out.println(Thread.currentThread().getName() + " Config not yet initialized.");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Config appConfig = new Config();

        Thread initializerThread = new Thread(() -> {
            try {
                Thread.sleep(100); // Simulate some delay
                appConfig.initialize(42, "ApplicationConfig");
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "InitializerThread");

        Thread readerThread1 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                appConfig.displayConfig();
                try {
                    Thread.sleep(70);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        }, "ReaderThread-1");

        Thread readerThread2 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                appConfig.displayConfig();
                try {
                    Thread.sleep(80);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        }, "ReaderThread-2");

        readerThread1.start();
        readerThread2.start();
        initializerThread.start();

        initializerThread.join();
        readerThread1.join();
        readerThread2.join();
    }
}
```

**Explanation**: <br>
When `initializerThread` sets `initialized = true`, due to `volatile`'s happens-before guarantee, all preceding writes (`configValue = value`, `configName = name`) are guaranteed to be visible to `readerThread1` and `readerThread2` after they read `initialized` as `true`. Without `volatile` on `initialized`, readers might see `initialized` as `true` but still read stale `configValue` or `configName` from their caches.

#### Advanced Level: Double-Checked Locking (Corrected with `volatile`)
This pattern is for lazy initialization of singletons, and `volatile` is crucial for its correctness.
```java
// Advanced Example: Double-Checked Locking with volatile
class Singleton {
    // volatile ensures that the instance is fully constructed before being visible
    // and prevents instruction reordering issues (e.g., partial construction visibility).
    private static volatile Singleton instance;

    private Singleton() {
        // Simulate complex initialization
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println(Thread.currentThread().getName() + " Singleton instance created.");
    }

    public static Singleton getInstance() {
        if (instance == null) { // First check: no lock, for performance
            synchronized (Singleton.class) { // Acquire lock only if instance is null
                if (instance == null) { // Second check: ensure instance is still null after acquiring lock
                    instance = new Singleton(); // This must be visible atomically
                }
            }
        }
        return instance;
    }

    public void doSomething() {
        System.out.println(Thread.currentThread().getName() + " doing something with Singleton " + this.hashCode());
    }

    public static void main(String[] args) throws InterruptedException {
        Runnable task = () -> {
            Singleton s = Singleton.getInstance();
            s.doSomething();
        };

        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");
        Thread t3 = new Thread(task, "Thread-3");

        t1.start();
        t2.start();
        t3.start();

        t1.join();
        t2.join();
        t3.join();
    }
}
```

**Explanation**: <br>
In double-checked locking, `volatile` on the `instance` variable is critical. Without `volatile`, the JVM or CPU might reorder the operations within the `new Singleton()` constructor. A thread might see a non-null `instance` reference before the `Singleton` object is fully initialized (e.g., its fields are set to default values but not yet the correct ones). If another thread then accesses this partially constructed object, it can lead to bugs. `volatile` guarantees that the write to `instance` occurs only after the `Singleton` object is fully constructed, ensuring visibility of the correctly initialized object.

### Common Pitfalls with `volatile`
- **Does NOT guarantee atomicity**: This is the most common misunderstanding. `volatile` only ensures visibility and ordering. Operations like `i++` (read `i`, increment, write `i`) are not atomic, even if `i` is `volatile`. A race condition can still occur where two threads read the same value, increment, and then write back, causing an update to be lost.
- **Only for single variables**: `volatile` applies to individual variables. It cannot ensure atomicity or consistency across multiple variables (e.g., if you have volatile int x; volatile int y; and you want to ensure x and y are updated together atomically).
- **Not a replacement for `synchronized` or `Lock`**: For compound operations or guarding complex critical sections, full synchronization mechanisms are required.
- **Memory Barriers Cost**: While lighter than locks, `volatile` still introduces memory barrier instructions, which have a performance cost (though typically less than full locks).

### Best Practices for `volatile`
- **Use for simple flags/status**: Ideal for variables that act as flags (e.g., `stopRequested`, `initialized`) where one thread writes and others read.
- **Single-writer scenarios**: Best when only one thread writes to the `volatile` variable and multiple threads read it. If multiple threads write, you often need stronger atomicity guarantees.
- **Understand its limitations**: Always remember that `volatile` is about visibility and ordering, not atomicity.
- **Prefer Atomic Classes**: For atomic read-modify-write operations (like incrementing a counter), prefer java.util.concurrent.atomic classes over volatile on primitive types.

## Compare-and-Swap (CAS): The Atomic Primitive
**Compare-and-Swap (CAS)** is a low-level atomic operation that is the foundation for many lock-free algorithms and the `java.util.concurrent.atomic` package.

### Overview and Importance
CAS is a machine-level instruction (supported by most modern CPUs) that takes three operands:
1. **Memory Location (V)**: The address of the variable to be updated.
2. **Expected Value (A)**: The value the caller expects to find at `V`.
3. **New Value (B)**: The value to be written to `V` if the `Expected Value` matches the `Current Value`.

The CAS operation atomically performs the following:
- It reads the `Current Value` at `V`.
- It compares the `Current Value` with the Expected Value (A).
- If they are equal, it writes the N`ew Value (B)` to `V`.
- If they are not equal, it does nothing (does not write `B` to `V`).
- It returns the `Current Value` at `V` (before the potential write), or a boolean indicating success/failure.

The key is that this entire "read-compare-write" sequence happens **atomically** at the hardware level, meaning no other thread can interrupt it.

#### Importance
CAS allows for lock-free updates to shared variables. Instead of blocking threads with locks, threads repeatedly try to update a variable using CAS. If the CAS fails (meaning another thread already modified the variable), the thread simply retries the operation, usually by re-reading the latest value and recalculating the new value. This busy-waiting (also known as a "spin lock" loop when combined with retries) avoids context switching and potential deadlocks associated with traditional locks.

### Syntax, Use Cases, and Implementation
n Java, you don't directly access the raw CAS instruction. Instead, it's exposed through the java.util.concurrent.atomic classes.

#### Beginner Level: Conceptual Understanding of CAS Loop
Imagine you want to atomically increment a counter:
- Non-atomic increment (problematic):
  ```
  int counter = 0; // Shared variable
  counter++; // This is NOT atomic: read, increment, write
  ```
- Conceptual CAS-based increment:
  ```java
  // Conceptual (pseudo-code) for atomic increment using CAS
  int value;
  do {
  value = counter; // Read current value
  } while (!CAS(counter, value, value + 1)); // Try to swap: if counter is still 'value', set to 'value+1'
  // If CAS fails, loop again (another thread changed counter in between read and CAS attempt)
  ```

This "read-modify-write" loop is the fundamental pattern for lock-free algorithms using CAS.

#### Intermediate Level: Basic `AtomicInteger.compareAndSet()`
```java
import java.util.concurrent.atomic.AtomicInteger;

// Intermediate Example: AtomicInteger using compareAndSet for custom logic
class MyAtomicCounter {
    private AtomicInteger count = new AtomicInteger(0);

    // Custom increment logic that uses compareAndSet
    public void incrementOddOnly() {
        int oldValue;
        int newValue;
        do {
            oldValue = count.get(); // Get the current value
            if (oldValue % 2 != 0) { // Only increment if current value is odd
                newValue = oldValue + 1;
            } else {
                // If it's even, just return without changing (or set to 0, or whatever logic)
                System.out.println(Thread.currentThread().getName() + " Skipped increment, value was even: " + oldValue);
                return; // Or break out of loop if no change is desired
            }
        } while (!count.compareAndSet(oldValue, newValue)); // Atomically compare and set
        // If compareAndSet returns false, it means count changed, so loop and retry
        System.out.println(Thread.currentThread().getName() + " Incremented to: " + count.get());
    }

    public int getCount() {
        return count.get();
    }

    public static void main(String[] args) throws InterruptedException {
        MyAtomicCounter counter = new MyAtomicCounter();

        Runnable task = () -> {
            for (int i = 0; i < 5; i++) {
                counter.incrementOddOnly();
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final count: " + counter.getCount());
    }
}
```

**Explanation**: <br>
This example shows how `compareAndSet(expectedValue, newValue)` is used. The `do-while` loop ensures that if another thread modifies `count` between `count.get()` and `count.compareAndSet()`, the `compareAndSet` will fail, and the loop will retry with the latest value. This allows for custom, atomic read-modify-write operations.

#### Advanced Level: Implementing a Lock-Free Stack (Conceptual with `AtomicReference`)
Implementing complex lock-free data structures is challenging. The `ABA problem` is a common issue where a value changes from A to B and back to A, causing a CAS operation to incorrectly succeed. `AtomicStampedReference` can mitigate this by associating a "stamp" (version number) with the reference

```java
import java.util.concurrent.atomic.AtomicReference;
import java.util.concurrent.atomic.AtomicStampedReference; // For ABA problem

// Advanced Example: A simplified Lock-Free Stack using AtomicReference
// NOTE: This basic implementation is susceptible to the ABA problem.
// For a production-ready lock-free stack, AtomicStampedReference or a memory reclamation scheme is needed.

class Node<T> {
    final T value;
    volatile Node<T> next; // next can be volatile for visibility

    Node(T value) {
        this.value = value;
    }
}

class LockFreeStack<T> {
    // AtomicReference to the top of the stack.
    // AtomicStampedReference is generally preferred for linked data structures to prevent ABA.
    private AtomicReference<Node<T>> head = new AtomicReference<>();
    // private AtomicStampedReference<Node<T>> head = new AtomicStampedReference<>(null, 0); // For ABA solution

    public void push(T value) {
        Node<T> newHead = new Node<>(value);
        Node<T> oldHead;
        do {
            oldHead = head.get(); // Read the current head
            newHead.next = oldHead; // Point new node to old head
            // Try to atomically set newHead as the head.
            // If another thread modified head, this CAS will fail, and we retry.
        } while (!head.compareAndSet(oldHead, newHead));
        System.out.println(Thread.currentThread().getName() + " Pushed: " + value);
    }

    public T pop() {
        Node<T> oldHead;
        Node<T> newHead;
        do {
            oldHead = head.get(); // Read the current head
            if (oldHead == null) {
                System.out.println(Thread.currentThread().getName() + " Stack is empty.");
                return null; // Stack is empty
            }
            newHead = oldHead.next; // New head will be the next node
            // Try to atomically set newHead as the head.
            // If another thread modified head, this CAS will fail, and we retry.
        } while (!head.compareAndSet(oldHead, newHead));
        System.out.println(Thread.currentThread().getName() + " Popped: " + oldHead.value);
        return oldHead.value;
    }

    public static void main(String[] args) throws InterruptedException {
        LockFreeStack<Integer> stack = new LockFreeStack<>();

        Runnable pushTask = () -> {
            for (int i = 0; i < 5; i++) {
                stack.push(Thread.currentThread().hashCode() * 10 + i);
                try {
                    Thread.sleep(Math.random() > 0.5 ? 10 : 20);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Runnable popTask = () -> {
            for (int i = 0; i < 3; i++) {
                stack.pop();
                try {
                    Thread.sleep(Math.random() > 0.5 ? 15 : 25);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread pusher1 = new Thread(pushTask, "Pusher-1");
        Thread pusher2 = new Thread(pushTask, "Pusher-2");
        Thread popper1 = new Thread(popTask, "Popper-1");

        pusher1.start();
        pusher2.start();
        popper1.start();

        pusher1.join();
        pusher2.join();
        popper1.join();

        // Pop remaining items
        while (stack.pop() != null) {}
    }
}
```

**Explanation**: <br>
This `LockFreeStack` attempts to implement stack operations using `AtomicReference`.
- `push`: A new `Node` is created. It then enters a `do-while` loop, reading the current `head`, setting the `newHead.next` to the `oldHead`, and attempting a `compareAndSet`. If `compareAndSet` succeeds, the new node is the head. If it fails, another thread interfered, so it retries.
- `pop`: Similar logic. It reads the current `head`, determines the `newHead` (the next node), and attempts to `compareAndSet` the `head` to `newHead`

**ABA Problem (Crucial for linked data structure)**:
Consider a scenario for `pop()`:
1. Thread A reads `head` as `Node(A)`
2. Thread A is preempted
3. Thread B `pop`s `Node(A)`
4. Thread C `push`es `Node(A)` back onto the stack (perhaps `Node(A)` was recycled or a new node with the same memory address was created and pushed).
5. Thread A resumes. It tries `head.compareAndSet(Node(A), newHead)`. Since the current `head` is still `Node(A)` (even though it's a different `Node(A)` (even though it's a different `Node(A)` instance or the same instance but logically a different "A"), the CAS succeeds! This is a bug because the stack has changed logically.

`AtomicStampedReference` solves this by requiring not just the object reference to match, but also an integer "stamp" (version) that must match. Every time the reference is updated, the stamp is incremented.
```java
// Example of how AtomicStampedReference would be used for push:
// private AtomicStampedReference<Node<T>> head = new AtomicStampedReference<>(null, 0);
// public void push(T value) {
//     Node<T> newHead = new Node<>(value);
//     int[] stampHolder = new int[1]; // To store the current stamp
//     Node<T> oldHead;
//     do {
//         oldHead = head.get(stampHolder); // Get the current head AND its stamp
//         newHead.next = oldHead;
//         // Compare expected head AND expected stamp with current head AND current stamp
//         // If both match, update head and increment stamp
//     } while (!head.compareAndSet(oldHead, newHead, stampHolder[0], stampHolder[0] + 1));
// }
```

### Common Pitfalls with CAS
- **ABA Problem**: As discussed, a value might change from A to B and back to A, causing a CAS to succeed incorrectly. Use `AtomicStampedReference` or `AtomicMarkableReference` for reference types, or ensure the value cannot be reused.
- **Spinning/Busy-Waiting**: If contention is high, threads repeatedly failing CAS and retrying can consume CPU cycles inefficiently, leading to higher CPU usage than lock-based approaches. This is a trade-off: no context switching vs. wasted cycles.
- **Complexity**: Designing correct lock-free algorithms, especially for complex data structures, is extremely hard and error-prone. It's often best left to experts (e.g., those who develop `java.util.concurrent` classes).
- **Non-Blocking vs Wait-Free vs Obstruction-Free**:
  - **Obstruction-Free**: If a thread runs by itself (other threads suspended), it will complete its operation in a finite number of steps.
  - **Lock-Free**: Guarantees system-wide progress. At least one thread will make progress, even if others are suspended or fail. (CAS-based algorithms are typically lock-free). Individual threads might still starve
  - **Wait-Free**: Guarantees per-thread progress. Every thread will complete its operation in a finite number of steps, regardless of other threads' execution speed or failures. This is the strongest guarantee but also the hardest to achieve

### Best Practices for CAS
- **Prefer** `java.util.concurrent.atomic` **classes**: For single-variable atomic operations (counters, flags, single references), use the provided atomic classes (`AtomicInteger`, `AtomicLong`, `AtomicBoolean`, `AtomicReference`). They handle the CAS loops and underlying complexities for you.
- **Understand ABA if implementing custom structures**: If you venture into building your own lock-free data structures using `compareAndSet`, thoroughly understand the ABA problem and use `AtomicStampedReference` or other techniques to mitigate it.
- **Benchmark**: Don't assume lock-free is always faster. In low contention, `synchronized` or `ReentrantLock` might perform better due to less spinning. Benchmark under realistic contention levels.
- **Simplicity first**: Start with simpler synchronization (synchronized, Locks). Only move to lock-free if performance profiling identifies contention as a significant bottleneck and you understand the inherent complexities.
- **Embrace** `getAndUpdate`**/**`updateAndGet` **(Java 8+)**: These methods in atomic classes simplify common CAS patterns by taking a `UnaryOperator` (lambda) for the update logic, making the `do-while` loop implicit and less error-prone.

## Atomic Variables: Pre-built Lock-Free Operations
The `java.util.concurrent.atomic` package provides a set of classes that support atomic operations on single variables, typically implemented using CAS internally. These classes offer a higher-level, safer, and easier-to-use API than directly implementing CAS loops

### Overview and Importance
These classes wrap primitive types or object references and provide methods that perform common operations (like incrementing, setting, or comparing-and-setting) atomically, without requiring explicit locks.

#### Key Atomic Classes:
- `AtomicBoolean`: An `boolean` value that may be updated atomically.
- `AtomicInteger`: An `int` value that may be updated atomically.
- `AtomicLong`: A `long` value that may be updated atomically.
- `AtomicReference<V>`: An object reference that may be updated atomically.
- `AtomicIntegerArray`, `AtomicLongArray`, `AtomicReferenceArray`: For atomic operations on array elements.
- `AtomicMarkableReference<V>`: Associates a single boolean "mark" with a reference, atomically updatable. Useful for logical deletion without ABA issues.
- `AtomicStampedReference<V>`: Associates an integer "stamp" (version number) with a reference, atomically updatable. The primary solution for the ABA problem.
- `LongAdder`, `DoubleAdder`: (Java 8+) High-performance alternatives to `AtomicLong`/`AtomicInteger` for sum aggregation, especially under very high contention. They reduce contention by using multiple internal counters.
- `LongAccumulator`, `DoubleAccumulator`: (Java 8+) More generalized versions of `Adders` that can accumulate values using a custom function.

**Importance**: <br>
These classes are the go-to solution for atomic operations on single variables in Java, providing thread safety with better performance than locks in many scenarios and without the risk of deadlocks.

### Syntax, Use Cases, and Implementation
#### Beginner Level: Atomic Counters
```java
import java.util.concurrent.atomic.AtomicInteger;

// Beginner Example: AtomicInteger as a thread-safe counter
class SafeCounter {
    private AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet(); // Atomically increments and returns the new value
        // count.getAndIncrement(); // Atomically increments and returns the old value
    }

    public int getCount() {
        return count.get(); // Atomically gets the current value
    }

    public static void main(String[] args) throws InterruptedException {
        SafeCounter counter = new SafeCounter();

        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        };

        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");
        Thread t3 = new Thread(task, "Thread-3");

        t1.start();
        t2.start();
        t3.start();

        t1.join();
        t2.join();
        t3.join();

        System.out.println("Final Atomic Counter value: " + counter.getCount());
        // Expected output: 3000
    }
}
```

**Explanation**: <br>
The `incrementAndGet()` method of `AtomicInteger` uses CAS internally to atomically increment the counter. This guarantees that no updates are lost, even with multiple threads concurrently calling `increment()`, unlike a simple `int++` or `volatile int++`.

#### Intermediate Level: Atomic Reference for Immutable Objects
```java
import java.util.concurrent.atomic.AtomicReference;

// Intermediate Example: AtomicReference for managing an immutable object
final class Configuration { // Immutable class
    final String serverUrl;
    final int timeout;

    public Configuration(String serverUrl, int timeout) {
        this.serverUrl = serverUrl;
        this.timeout = timeout;
    }

    @Override
    public String toString() {
        return "Configuration [serverUrl=" + serverUrl + ", timeout=" + timeout + "]";
    }
}

class LiveConfigManager {
    private AtomicReference<Configuration> currentConfig =
        new AtomicReference<>(new Configuration("http://initial.url", 5000));

    public Configuration getConfig() {
        return currentConfig.get(); // Atomically gets the current reference
    }

    public void updateConfig(String newUrl, int newTimeout) {
        Configuration oldConfig;
        Configuration newConfig = new Configuration(newUrl, newTimeout);
        do {
            oldConfig = currentConfig.get(); // Get the current config
            // Try to set the new config only if the current config is still oldConfig
        } while (!currentConfig.compareAndSet(oldConfig, newConfig));
        System.out.println(Thread.currentThread().getName() + " Updated config to: " + newConfig);
    }

    public static void main(String[] args) throws InterruptedException {
        LiveConfigManager manager = new LiveConfigManager();

        Runnable reader = () -> {
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + " Read config: " + manager.getConfig());
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Runnable writer = () -> {
            for (int i = 0; i < 2; i++) {
                manager.updateConfig("http://new.url/" + i, 6000 + i * 100);
                try {
                    Thread.sleep(150);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread reader1 = new Thread(reader, "Reader-1");
        Thread reader2 = new Thread(reader, "Reader-2");
        Thread writerThread = new Thread(writer, "Writer-1");

        reader1.start();
        reader2.start();
        writerThread.start();

        reader1.join();
        reader2.join();
        writerThread.join();

        System.out.println("Final config: " + manager.getConfig());
    }
}
```

**Explanation**: <br>
`AtomicReference` is ideal for updating shared references to immutable objects. Instead of modifying the object in place (which would require external synchronization), you create a new immutable object and then atomically swap the reference using `compareAndSet()`. This ensures that readers always see a consistent, fully formed `Configuration` object, even during updates.

#### Advanced Level: `LongAdder` for Extreme Contention
For simple sum aggregations under very high contention, `LongAdder` (and `DoubleAdder`) often outperform ``AtomicLong``.

```java
import java.util.concurrent.atomic.LongAdder;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

// Advanced Example: LongAdder for high-contention counter
class HighContentionCounter {
    private LongAdder totalCount = new LongAdder();

    public void increment() {
        totalCount.increment(); // Internally, this distributes updates to different cells
    }

    public long getCount() {
        return totalCount.sum(); // Sums up all internal cells to get the total
    }

    public static void main(String[] args) throws InterruptedException {
        HighContentionCounter counter = new HighContentionCounter();
        int numThreads = 10;
        int incrementsPerThread = 1_000_000;
        ExecutorService executor = Executors.newFixedThreadPool(numThreads);

        long startTime = System.nanoTime();

        for (int i = 0; i < numThreads; i++) {
            executor.submit(() -> {
                for (int j = 0; j < incrementsPerThread; j++) {
                    counter.increment();
                }
            });
        }

        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);

        long endTime = System.nanoTime();
        long duration = (endTime - startTime) / 1_000_000; // milliseconds

        System.out.println("Final LongAdder count: " + counter.getCount());
        System.out.println("Time taken with LongAdder: " + duration + " ms");

        // For comparison, let's try with AtomicLong
        AtomicInteger atomicCount = new AtomicInteger(0);
        startTime = System.nanoTime();
        ExecutorService atomicExecutor = Executors.newFixedThreadPool(numThreads);

        for (int i = 0; i < numThreads; i++) {
            atomicExecutor.submit(() -> {
                for (int j = 0; j < incrementsPerThread; j++) {
                    atomicCount.incrementAndGet();
                }
            });
        }
        atomicExecutor.shutdown();
        atomicExecutor.awaitTermination(1, TimeUnit.MINUTES);

        endTime = System.nanoTime();
        duration = (endTime - startTime) / 1_000_000; // milliseconds

        System.out.println("Final AtomicInteger count: " + atomicCount.get());
        System.out.println("Time taken with AtomicInteger: " + duration + " ms");
    }
}
```

**Explanation**: <br>
`LongAdder` is designed for scenarios where many threads are incrementing a shared counter. Instead of a single `AtomicLong` that becomes a hot spot (high contention), `LongAdder` maintains a set of internal variables (cells). When threads contend, they increment different cells. When the sum is needed, all cells are summed up. This significantly reduces contention, often leading to better performance in very high-load, write-heavy scenarios compared to `AtomicLong`. The output from running this code will typically show `LongAdder` being faster for many threads.

### Commmon Pitfalls with Atomic Variables
- **Not a general synchronization mechanism**: Atomic classes only provide atomicity for operations on single variables. They cannot be used to protect a critical section involving multiple variables or complex logic. For that, you still need `synchronized` or `Lock`.
- **Performance vs. Simplicity**: While often faster than locks, atomic operations can still incur CPU overhead due to memory barriers and spin-waiting. Don't use them everywhere without justification.
- **ABA Problem (for AtomicReference/StampedReference)**: If you're working with object references and the object itself might be logically reused, be aware of the ABA problem and use `AtomicStampedReference` or `AtomicMarkableReference` as appropriate.
- **Read-Modify-Write Loops**: Correctly implementing `do-while` loops with compareAndSet is crucial. Errors here can lead to lost updates or incorrect state. Leverage `getAndUpdate`/`updateAndGet` where possible

### Best Practices for Atomic Variables
- **Use for single-variable atomicity**: They are perfectly suited for atomic counters, flags, and single-value registers.
- **Prefer** `AtomicInteger`, `AtomicLong`, etc., over `volatile` for compound ops: If you need an atomic increment (`i++`), use `AtomicInteger.incrementAndGet()` instead of `volatile int i; i++;`.
- **Leverage** `getAndUpdate`/`updateAndGet`: For complex read-modify-write operations, these Java 8+ methods simplify the CAS loop.
- **Use** `LongAdder`/`DoubleAdder` **for high-contention sums**: For sum aggregations, these classes are generally preferred over `AtomicLong`/`AtomicInteger` if contention is expected to be very high.
- **Consider immutability with** `AtomicReference`: When managing shared objects, combine `AtomicReference` with immutable objects to simplify reasoning about thread safety.
- **Profile to justify**: Only switch from simpler synchronization to atomic variables if profiling reveals a performance bottleneck due to contention on shared single variables.

## Performance Comparison: `synchronized` vs `Atomic` vss `volatile`
The choice among these mechanisms involves a trade-off between performance, complexity, and the specific thread safety guarantees required
- `synchronized` / `Lock` **(Blocking)**:
  - **Guarantees**: Mutual exclusion (only one thread in critical section) and visibility (happens-before relationship).
  - **Performance**: Can involve context switching overhead. Performs well at low to moderate contention. At very high contention, threads frequently block and unblock, which can lead to significant performance degradation. JVM optimizations like biased locking and thin locks make `synchronized` quite efficient in uncontended or lightly contended scenarios
  - **Use Cases**: Protecting complex critical sections, multi-variable invariants, or when explicit waiting/notification (`wait`/`notify` or `Condition`) is needed.
- `Atomic` **Variables (Lock-Free / Non-Blocking based on CAS)**:
  - **Guarantees**: Atomicity for single variable operations, and visibility. No mutual exclusion for broader critical sections.
  - **Performance**: Generally performs better than synchronized at moderate to high contention for single-variable updates because they avoid OS-level locking and context switching. They use CPU-level atomic instructions (CAS). However, spin-waiting in high contention can waste CPU cycles. LongAdder/DoubleAdder are optimized for very high contention for sums.
  - **Use Cases**: Atomic counters, flags, managing single shared references (especially to immutable objects), building advanced lock-free data structures.
- `volatile` **Variables (Visibility Only)**:
  - **Guarantees**: Visibility and memory ordering (happens-before). Does NOT guarantee atomicity for compound operations.
  - **Performance**: The lightest form of synchronization, incurring only memory barrier overhead. Faster than `Atomic` classes for simple reads/writes where atomicity isn't needed.
  - **Use Cases**: Simple flags for signaling between threads (e.g., `stopRequested`), ensuring visibility of configuration changes where no compound operations are involved.

#### Summary of Trade-offs:
| Feature                              | `synchronized` / `Lock`    | `Atomic` Variables | `volatile` Variables |
|--------------------------------------|----------------------------|--------------------|----------------------|
| Mutual Exclusion	                    | Yes	                       | No (for general code)	| No |
| Atomicity | Yes (for critical section) | Yes (for single var ops) | No (for compound ops)   |
| Visibility | Yes                        |	Yes |	Yes |
| Deadlock Risk	| Yes	| No (inherently)	| No |
| Starvation Risk	| Possible |	Possible (spinning)	| N/A |
| Complexity	| Moderate |	Moderate (for built-ins), High (for custom)	| Low |
| Performance (Low Contention) |	Good	| Good |	Excellent |
| Performance (High Contention)	| Degrades (blocking) |	Good (spinning, but better with Adders) |	Excellent (if applicable) |
| Common Use Case	| Shared resources with complex invariants, explicit waiting	| Counters, flags, single references	| Simple flags, status |

---

## Introduction: The Need for Thread Pools
In multithreaded applications, we often need to execute many tasks concurrently. A naive approach might be to create a new `Thread` object for each task. However, this strategy has significant drawbacks:
1. **High Overhead of Thread Creation/Destruction**: Creating and destroying threads is an expensive operation in terms of CPU and memory. Each thread requires its own stack, program counter, and other resources. For short-lived tasks, the overhead of managing threads can easily outweigh the benefits of concurrency.
2. **Resource Exhaustion**: An unbounded number of threads can quickly exhaust system resources (memory, CPU, file handles, network connections). This can lead to `OutOfMemoryError` or system instability.
3. **Difficulty in Management**: Managing individual threads (starting, stopping, monitoring) becomes increasingly complex as the number of tasks grows.
4. **No Workload Balancing**: It's hard to control how many tasks are running concurrently, potentially leading to thrashing if too many threads compete for CPU time.

**Thread Pools** provide a solution to these problems. A thread pool is a managed collection of worker threads that are kept alive and reused to execute submitted tasks. Instead of creating a new thread for every task, tasks are submitted to the pool, and the pool's threads pick up and execute these tasks.

**Why are Thread Pools important?** <br>
- **Reduced Overhead**: Threads are reused, eliminating the overhead of creating and destroying threads for each task.
- **Resource Management**: The pool limits the number of concurrent threads, preventing resource exhaustion and ensuring system stability.
- **Improved Responsiveness**: Tasks can start executing immediately if a thread is available, rather than waiting for a new thread to be created.
- **Centralized Management**: Provides a single point of control for managing thread lifecycle, statistics, and task queues.
- **Decoupling Task Submission from Execution**: The code that submits a task doesn't need to know how or when it will be executed.

In Java, the `java.util.concurrent` package (introduced in Java 5) provides the Executor framework, with ThreadPoolExecutor as its core implementation, to manage thread pools efficiently.

## The `Executor` Framework Hierarchy
Before diving into `ThreadPoolExecutor`, let's understand its place in the `Executor` framework:
- `Executor` **Interface**: The simplest interface, defining a single method `void execute(Runnable command)`. It decouples task submission from task execution. You submit a `Runnable` (a task) to an `Executor`, and it decides how to run it (e.g., in a new thread, in an existing thread, or on a different thread later).
- `ExecutorService` **Interface**: Extends `Executor` and adds methods for managing the lifecycle of the executor itself (e.g., `shutdown()`, `shutdownNow()`), and for submitting tasks that return results (`submit(Callable<T> task)`) or sets of tasks (`invokeAll()`, `invokeAny()`).
- `AbstractExecutorService` **Class**: Provides a base implementation for ExecutorService, handling some common logic.
- `ThreadPoolExecutor` **Class**: The most fundamental and configurable implementation of `ExecutorService`. It allows fine-grained control over pool size, queueing strategies, thread creation, and rejection policies.
- `Executors` **Utility Class**: Provides convenient static factory methods to create commonly used `ExecutorService` instances, which are often instances of `ThreadPoolExecutor` configured in specific ways

## `ThreadPoolExecutor`: The Heart of Thread Pools
`ThreadPoolExecutor` is the workhorse of Java's thread pool management. It's highly configurable, making it suitable for a wide range of use cases.

### Overview and Key Parameters
A `ThreadPoolExecutor` manages a pool of worker threads and a queue for tasks awaiting execution. It's configured with several key parameters that define its behavior:
1. `corePoolSize`: The minimum number of threads that should be kept alive in the pool, even if they are idle. These threads are created when new tasks are submitted if the `corePoolSize` hasn't been reached.
2. `maximumPoolSize`: The maximum number of threads the pool can contain. If the task queue is full and `corePoolSize` threads are busy, new threads up to `maximumPoolSize` will be created to handle incoming tasks.
3. `keepAliveTime`: When the number of threads in the pool exceeds `corePoolSize`, `keepAliveTime` specifies how long idle threads (exceeding `corePoolSize`) will wait for new tasks before terminating.
4. `unit`: The `TimeUnit` for the `keepAliveTime` argument (e.g., `TimeUnit.SECONDS`).
5. `workQueue`: A `BlockingQueue` that holds tasks submitted to the executor but not yet executed. When `corePoolSize` threads are busy, new tasks are added to this queue.
6. `threadFactory`: An optional factory to create new threads for the pool. This can be used to customize thread names, daemon status, priority, etc. If not specified, `Executors.defaultThreadFactory()` is used.
7. `handler` **(RejectedExecutionHandler)**: A strategy for handling tasks that cannot be executed (e.g., when the pool is shut down, or when `maximumPoolSize` threads are busy and the `workQueue` is full).

**How `ThreadPoolExecutor` processes tasks (simplified flow):**
1. If the number of threads is less than `corePoolSize`, a new thread is created to run the task.
2. If `corePoolSize` threads are running, the task is added to the workQueue.
3. If the `workQueue` is full, and the number of threads is less than `maximumPoolSize`, a new thread is created to run the task.
4. If `maximumPoolSize` threads are running and the `workQueue` is full, the `handler` rejects the task

### Syntax, Use Cases, and Implementation
#### Beginner Level: Using `Executors` Factory Methods
For basic use cases, `Executors` provides convenient factory methods that return pre-configured `ThreadPoolExecutor` instances

**Common `Executors` methods**:
- `Executors.newFixedThreadPool(int nThreads)`: Creates a thread pool with a fixed number of threads. Tasks are placed in an unbounded `LinkedBlockingQueue`. If `nThreads` are busy, new tasks wait in the queue. (behaves like `ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>())`)
- `Executors.newCachedThreadPool()`: Creates a thread pool that creates new threads as needed, but reuses previously constructed threads when they are available. Idle threads are terminated after 60 seconds. Uses a `SynchronousQueue`. Suitable for applications with many short-lived tasks. (behaves like `ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>())`)
- `Executors.newSingleThreadExecutor()`: Creates an executor that uses a single worker thread. Tasks are executed sequentially. Uses an unbounded `LinkedBlockingQueue`. (behaves like `ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>())`)
- `Executors.newScheduledThreadPool(int corePoolSize)`: Creates a thread pool that can schedule commands to run after a given delay, or to execute periodically.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

// Beginner Example: Using Executors.newFixedThreadPool
class SimpleTask implements Runnable {
    private String name;

    public SimpleTask(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " executing " + name);
        try {
            Thread.sleep(100 + (long)(Math.random() * 200)); // Simulate work
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            System.out.println(Thread.currentThread().getName() + " interrupted.");
        }
        System.out.println(Thread.currentThread().getName() + " finished " + name);
    }

    public static void main(String[] args) throws InterruptedException {
        // Create a fixed thread pool with 2 threads
        ExecutorService executor = Executors.newFixedThreadPool(2);

        // Submit multiple tasks
        for (int i = 1; i <= 5; i++) {
            executor.execute(new SimpleTask("Task-" + i));
        }

        // Shut down the executor. It will stop accepting new tasks,
        // but will complete all previously submitted tasks.
        executor.shutdown();

        // Wait for all tasks to complete (optional, but good practice for main thread)
        System.out.println("Main: All tasks submitted. Waiting for completion.");
        if (executor.awaitTermination(1, TimeUnit.MINUTES)) {
            System.out.println("Main: All tasks completed successfully.");
        } else {
            System.out.println("Main: Some tasks did not complete within the timeout.");
        }
    }
}
```

**Explanation**: <br>
The `newFixedThreadPool(2)` creates a pool with two threads. Tasks 1 and 2 will start immediately. When they are busy, Tasks 3, 4, 5 will be put into the queue. As threads finish a task, they pick up the next one from the queue. `executor.shutdown()` is crucial: it initiates a graceful shutdown, preventing new tasks from being submitted but allowing existing tasks to complete. `awaitTermination()` makes the main thread wait for the pool to shut down completely.

### Intermediate Level: Custom `ThreadPoolExecutor` Configuration
For more control, you directly instantiate `ThreadPoolExecutor`

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.RejectedExecutionHandler;
import java.util.concurrent.Executors;

// Intermediate Example: Custom ThreadPoolExecutor Configuration
class CustomTask implements Runnable {
    private String name;

    public CustomTask(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " is processing " + name);
        try {
            Thread.sleep(500); // Simulate longer work
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            System.out.println(Thread.currentThread().getName() + " interrupted while processing " + name);
        }
        System.out.println(Thread.currentThread().getName() + " finished " + name);
    }

    public static void main(String[] args) throws InterruptedException {
        int corePoolSize = 2;
        int maximumPoolSize = 4;
        long keepAliveTime = 10; // seconds
        int queueCapacity = 2; // Small queue to demonstrate rejection

        // 1. Define the work queue
        BlockingQueue<Runnable> workQueue = new ArrayBlockingQueue<>(queueCapacity);

        // 2. Define the rejected execution handler
        // ThreadPoolExecutor.AbortPolicy (default): throws RejectedExecutionException
        // ThreadPoolExecutor.CallerRunsPolicy: runs the rejected task in the caller's thread
        // ThreadPoolExecutor.DiscardOldestPolicy: discards the oldest unexecuted task
        // ThreadPoolExecutor.DiscardPolicy: discards the rejected task
        RejectedExecutionHandler rejectionHandler = new ThreadPoolExecutor.CallerRunsPolicy();

        // 3. Create the ThreadPoolExecutor
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            corePoolSize,
            maximumPoolSize,
            keepAliveTime,
            TimeUnit.SECONDS,
            workQueue,
            Executors.defaultThreadFactory(), // Optional: custom ThreadFactory
            rejectionHandler
        );

        System.out.println("Main: Submitting tasks...");
        for (int i = 1; i <= 10; i++) {
            String taskName = "Task-" + i;
            try {
                executor.execute(new CustomTask(taskName));
                System.out.println("Main: Submitted " + taskName + ". Queue size: " + workQueue.size());
            } catch (Exception e) {
                System.err.println("Main: Failed to submit " + taskName + ": " + e.getMessage());
            }
            Thread.sleep(50); // Simulate tasks being submitted over time
        }

        System.out.println("Main: All submissions attempted. Shutting down executor.");
        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);
        System.out.println("Main: Executor shut down.");
    }
}
```

**Explanation of Flow (with example values)**: <br>
- **Tasks 1, 2**: `corePoolSize` is 2. Threads are created and start executing these tasks. (Pool size becomes 2).
- **Tasks 3, 4**: `corePoolSize` threads are busy. `queueCapacity` is 2. Tasks 3 and 4 are added to `workQueue`. (Pool size remains 2, Queue size becomes 2).
- **Tasks 5, 6**: `corePoolSize` threads are busy, `workQueue` is full. `maximumPoolSize` is 4. New threads are created (up to `maximumPoolSize`). Tasks 5 and 6 start executing. (Pool size becomes 4, Queue size remains 2).
- **Tasks 7, 8, 9, 10**: `maximumPoolSize` threads are busy, `workQueue` is full. The `rejectionHandler` (`CallerRunsPolicy`) kicks in. The calling thread (main thread in this case) will execute these rejected tasks. You'll see "Main" in the thread name for rejected tasks.

This demonstrates precise control over thread creation, queueing, and handling of overloaded situations.

#### Advanced Level: Submitting `Callable` Tasks and `Future`s
`ExecutorService` allows submitting `Callable` tasks (which return a result) and retrieving results via `Future` objects

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;
import java.util.ArrayList;
import java.util.List;

// Advanced Example: Submitting Callable tasks and processing Futures
class FactorialCalculator implements Callable<Long> {
    private int number;

    public FactorialCalculator(int number) {
        this.number = number;
    }

    @Override
    public Long call() throws Exception {
        long result = 1;
        if (number < 0) {
            throw new IllegalArgumentException("Number must be non-negative.");
        }
        for (int i = 1; i <= number; i++) {
            result *= i;
            // Simulate work
            Thread.sleep(10);
        }
        System.out.println(Thread.currentThread().getName() + " calculated factorial of " + number + " as " + result);
        return result;
    }

    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(3); // Pool of 3 threads
        List<Future<Long>> futures = new ArrayList<>();

        // Submit multiple Callable tasks
        for (int i = 0; i <= 10; i++) {
            FactorialCalculator calculator = new FactorialCalculator(i);
            Future<Long> future = executor.submit(calculator); // Submit returns a Future
            futures.add(future);
        }

        System.out.println("Main: All factorial tasks submitted.");

        // Retrieve results from Futures
        for (Future<Long> future : futures) {
            try {
                // future.get() blocks until the task completes and returns its result
                Long result = future.get();
                System.out.println("Main: Retrieved result: " + result);
            } catch (ExecutionException e) {
                // If the task threw an exception, it's wrapped in ExecutionException
                System.err.println("Main: Task threw an exception: " + e.getCause().getMessage());
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                System.err.println("Main: Interrupted while waiting for future.");
            }
        }

        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);
        System.out.println("Main: Executor shut down. All results processed.");
    }
}
```

**Explanation**: <br>
This example demonstrates `Callable` and `Future`.
- `FactorialCalculator` implements `Callable<Long>`, meaning its `call()` method returns a Long result and can throw `Exception`.
- `executor.submit(calculator)` returns a `Future<Long>`.
- `future.get()` blocks until the corresponding `Callable` task completes and returns its result. If the task throws an exception, `get()` throws an `ExecutionException` with the original exception as its cause. `get()` can also throw `InterruptedException` if the calling thread is interrupted while waiting.

- This is a powerful pattern for asynchronously executing tasks that produce results.

## Common Pitfalls with Thread Pools
- **Unbounded Queues** (`newFixedThreadPool`, `newSingleThreadExecutor`): While convenient, using an unbounded queue (like `LinkedBlockingQueue` internally used by `newFixedThreadPool`) can lead to `OutOfMemoryError` if tasks are submitted faster than they can be processed and memory fills up with queued tasks.
- **Uncontrolled Thread Creation** (`newCachedThreadPool`): `newCachedThreadPool` creates new threads as needed, up to `Integer.MAX_VALUE`. If tasks are very short-lived and arrive in bursts, it can create a huge number of threads, potentially exhausting system resources.
- **Improper Shutdown**: Forgetting to call `executor.shutdown()` or `executor.shutdownNow()` can prevent your application from exiting gracefully, as the threads in the pool will remain active.
- **Ignoring** `RejectedExecutionHandler`: Not considering what happens when tasks are rejected can lead to unexpected behavior or lost work in high-load scenarios.
- **Deadlocks within the Pool**: If tasks submitted to the pool need to wait for other tasks also submitted to the same pool (and the pool is fixed-size or becomes saturated), it can lead to a deadlock where all threads are waiting for each other. For example, if task A submits task B to the same pool and waits for B, and the pool is full, B might never get a chance to run.
- **Exception Handling**: Exceptions thrown by `Runnable` tasks are caught by the `ThreadPoolExecutor` and often logged (or handled by the `UncaughtExceptionHandler` if set), but they don't propagate back to the submitting thread unless you use `submit()` with `Callable` and check the `Future.get()`. For `Runnable` tasks, you might need to wrap them with a try-catch block inside their `run()` method or set a custom `UncaughtExceptionHandler`.
- **Shared Mutable State**: Thread pools do not solve the problem of thread safety for shared mutable state. Tasks executed by the pool still need proper synchronization mechanisms (`synchronized`, `Lock`, `Atomic`) if they access common data.


## Best Practices for Thread Pools
- **Prefer** `ThreadPoolExecutor` **Constructor over** `Executors` **Factories for Production**: For production applications, always use the `ThreadPoolExecutor` constructor directly. This forces you to explicitly define the pool's parameters, giving you precise control and preventing issues like unbounded queues or excessive thread creation that `Executors` factory methods might implicitly introduce.
- **Choose** `corePoolSize` **and** `maximumPoolSize` carefully:
  - **CPU-bound tasks**: `corePoolSize` = `maximumPoolSize` = Number of CPU cores. (e.g., `Runtime.getRuntime().availableProcessors()`). This keeps the CPUs busy without excessive context switching.
  - **I/O-bound tasks**: `corePoolSize` can be much larger than CPU cores, as threads spend most of their time waiting for I/O. A common heuristic is `N_cores * (1 + WaitTime/ComputeTime)`. Start with `N_cores * 2` or `N_cores * 4` and tune with profiling.
  - Consider workload characteristics: bursty vs. steady, short-lived vs. long-lived tasks
- Choose the Right `BlockingQueue`:
  - `LinkedBlockingQueue` (unbounded, used by `newFixedThreadPool`): Good for long-lived tasks where queue size doesn't matter or for `newSingleThreadExecutor`. Risky if tasks can overwhelm consumers.
  - `ArrayBlockingQueue` (bounded): Preferred for fixed-size pools to prevent `OutOfMemoryError` due to excessive queueing. Provides backpressure.
  - `SynchronousQueue` (zero capacity, used by `newCachedThreadPool`): Transfers tasks directly from producer to consumer. If no consumer is immediately available, a new thread (up to `maximumPoolSize`) is created. If `maximumPoolSize` is reached, it rejects. Ideal for many short-lived, independent tasks.
- **Implement a** `RejectedExecutionHandler`: Always provide a meaningful `RejectedExecutionHandler` strategy (e.g., `CallerRunsPolicy` to apply backpressure, or a custom handler to log and queue externally).
- **Graceful Shutdown**: Always call `executor.shutdown()` when your application is gracefully shutting down. Use `awaitTermination()` to wait for tasks to complete. For immediate shutdown (e.g., critical error), use `executor.shutdownNow()`
- **Monitor and Tune**: Use JMX or custom metrics to monitor your thread pool's state (active threads, queue size, completed tasks) to identify bottlenecks and tune parameters.
- **Use** `Callable` **and** `Future` **for tasks with results**: This simplifies retrieving results and handling exceptions from background tasks.
- **Custom** `ThreadFactory`: For better debugging and monitoring, provide a custom `ThreadFactory` to give meaningful names to your worker threads (e.g., `new ThreadPoolExecutor(..., new ThreadFactoryBuilder().setNameFormat("MyWorker-%d").build())` from Guava).
- **Beware of shared mutable state**: Thread pools manage threads, but they don't automatically make your shared data thread-safe. Use appropriate synchronization
- **Consider ForkJoinPool (Java 7+)**: For divide-and-conquer problems, `ForkJoinPool` and `RecursiveTask`/`RecursiveAction` are often more efficient

## Performance Considerations
The performance of a ThreadPoolExecutor largely depends on its configuration and the nature of the tasks being executed.
- **CPU-bound tasks**:
  - Too few threads: Underutilization of CPU cores.
  - Too many threads: Excessive context switching, leading to degraded performance.
  - Optimal: `corePoolSize` and `maximumPoolSize` close to the number of available CPU cores.
- **I/O-bound tasks**:
  - Threads spend most time waiting. A small number of threads will underutilize I/O resources.
  - Optimal: More threads than CPU cores, allowing some threads to wait while others perform computations. Tune empirically
- `workQueue` **choice**:
  - **Bounded queues** (e.g., `ArrayBlockingQueue`) provide backpressure. If producers are much faster than consumers, they will block, slowing down the producers. This helps prevent resource exhaustion but can lead to producer throughput reduction
  - **Unbounded queues** (e.g., `LinkedBlockingQueue`) can hide performance problems by endlessly queuing tasks, potentially leading to `OutOfMemoryError`.
  - `SynchronousQueue` is suitable for scenarios where tasks are short and there's a strong desire to create new threads quickly for bursty workloads, or to reject if no consumer is immediately available
- `keepAliveTime`: For `CachedThreadPool` or pools where `maximumPoolSize` > `corePoolSize`, a longer `keepAliveTime` might keep idle threads around longer, potentially reducing thread creation overhead during subsequent bursts, but increasing memory usage for idle threads
- **Contention**: High contention for shared resources within tasks will limit the benefits of parallelism, regardless of pool size. Optimize tasks to reduce contention or use lock-free data structures where appropriate

## Advanced Concepts and Use Cases (Briefly)
- `CompletableFuture` **(Java 8+)**: Often used in conjunction with `ExecutorService` for building asynchronous, non-blocking pipelines of tasks.
- `ExecutorCompletionService`: Helps process `Future` results as they become available, rather than waiting for all tasks to complete or processing them in submission order.
- **Custom** `RejectedExecutionHandler`:
  - **Logging and Metrics**: Log rejected tasks for monitoring.
  - **Fallback mechanisms**: Submit to a different, less critical thread pool, or store in a persistent queue for later retry.
  - **Throttling**: Force the caller to wait for a period or retry, effectively providing backpressure.
- **Lifecycle Management in Real-world Applications**: In long-running applications (e.g., web servers), thread pools are typically initialized once at application startup and shut down gracefully when the application terminates. Frameworks like Spring often provide declarative ways to configure and manage thread pools.

---

## Introduction: Asynchronous Programming in Java
**Problems with purely synchronous execution**: <br>
1. **Responsiveness**: In GUI applications, a long-running task on the Event Dispatch Thread (EDT) can freeze the user interface.
2. **Resource Utilization**: In server applications, a thread blocking on an I/O operation (like a database query) holds onto valuable server resources without doing useful work, reducing the server's capacity to handle other requests.
3. **Throughput**: Overall application throughput can suffer if sequential execution bottlenecks are frequent.

**Asynchronous Programming** allows a program to initiate a long-running operation and then continue executing other tasks without waiting for the first operation to complete. When the long-running operation finishes, it can notify the original thread (or another designated thread) with its result or an error.

The Java `Executor` framework (introduced in Java 5) provided the foundation for asynchronous execution with `Runnable` and `ExecutorService`. However, `Runnable` doesn't return a value or throw checked exceptions, and `ExecutorService` only provides basic submission

This is where `Callable` and `Future` come in, providing a way to represent the result of an _asynchronous computation_. `CompletableFuture` (introduced in Java 8) takes this a step further, enabling powerful composition and chaining of asynchronous operations

**Why are `Callable`, `Future`, and `CompletableFuture` important?**
- **Non-Blocking Operations**: They allow the main thread (or other threads) to continue working while a background task runs, improving responsiveness and resource utilization.
- **Result Retrieval**: They provide mechanisms to get the result of an asynchronous computation, or handle exceptions that occurred during its execution.
- **Asynchronous Composition** (`CompletableFuture`): They enable building complex asynchronous workflows where tasks depend on each other, react to events, or run in parallel, all in a non-blocking fashion. This is crucial for microservices, reactive programming, and high-performance computing.
- **Error Handling**: Provide structured ways to handle exceptions that occur in asynchronous tasks.

## `Callable<V>` Interface: Tasks that Return Results
### Overview and Importance
Prior to Java 5, if you wanted to execute a task in a separate thread, you typically implemented the Runnable interface. However, Runnable's run() method has two limitations:
1. It cannot return a value.
2. It cannot throw checked exceptions (only unchecked RuntimeException).

The `Callable<V>` interface was introduced to overcome these limitations. It represents a task that returns a result of type V and can throw checked exceptions.

**Key Features**:
- `call()` **method**: Instead of `run()`, `Callable` defines a V `call()` throws `Exception` method
- **Generic Type** `V`: Specifies the type of the result that the `call()` method will return.

**Importance**: `Callable` is the first step towards truly asynchronous operations that produce a meaningful outcome, enabling more robust task execution.

### Syntax, Use Cases, and Implementation
```java
import java.util.concurrent.Callable;

public class MyCallableTask implements Callable<String> {
    private String taskName;

    public MyCallableTask(String taskName) {
        this.taskName = taskName;
    }

    @Override
    public String call() throws Exception {
        System.out.println(Thread.currentThread().getName() + " starting task: " + taskName);
        // Simulate work that might take time or throw an exception
        Thread.sleep(1000);
        if (Math.random() < 0.2) { // 20% chance of throwing an exception
            throw new Exception("Simulated failure for task: " + taskName);
        }
        String result = "Result of " + taskName;
        System.out.println(Thread.currentThread().getName() + " finished task: " + taskName);
        return result;
    }
}
```

### Beginner Level: Submitting a `Callable` to an `ExecutorService`
`Callable` tasks are typically submitted to an `ExecutorService` (which manages a thread pool). The `submit()` method of `ExecutorService` accepts a `Callable` and returns a `Future` object
```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.ExecutionException;

// Beginner Example: Submitting Callable and getting result via Future
public class CallableDemo {
    public static void main(String[] args) throws InterruptedException {
        // Create an ExecutorService with a fixed thread pool
        ExecutorService executor = Executors.newFixedThreadPool(2);

        // Create a Callable task
        Callable<String> task1 = new MyCallableTask("Data Processing 1");
        Callable<String> task2 = new MyCallableTask("Report Generation 2");

        System.out.println("Main: Submitting tasks...");

        // Submit the tasks to the executor and get Future objects
        Future<String> future1 = executor.submit(task1);
        Future<String> future2 = executor.submit(task2);

        System.out.println("Main: Tasks submitted. Doing other work...");
        // Simulate doing other work while tasks are running
        Thread.sleep(500);

        // Retrieve results from Futures
        try {
            System.out.println("Main: Waiting for Task 1 result...");
            String result1 = future1.get(); // Blocks until task1 completes
            System.out.println("Main: Task 1 result: " + result1);

            System.out.println("Main: Waiting for Task 2 result...");
            String result2 = future2.get(); // Blocks until task2 completes
            System.out.println("Main: Task 2 result: " + result2);

        } catch (ExecutionException e) {
            // This catches exceptions thrown by the Callable's call() method
            System.err.println("Main: Task failed with exception: " + e.getCause().getMessage());
        } catch (InterruptedException e) {
            // This catches if the main thread itself is interrupted while waiting
            System.err.println("Main: Interrupted while waiting for task completion.");
            Thread.currentThread().interrupt();
        }

        // Shut down the executor
        executor.shutdown();
        if (executor.awaitTermination(5, TimeUnit.SECONDS)) {
            System.out.println("Main: Executor shut down successfully.");
        } else {
            System.out.println("Main: Executor did not shut down within the timeout.");
            executor.shutdownNow(); // Force shutdown
        }
    }
}
```

**Explanation**: <br>
1. We define `MyCallableTask` to perform some work and return a `String`.
2. An `ExecutorService` (backed by a thread pool) is created.
3. `executor.submit(task)` schedules `task` for execution and immediately returns a `Future` object. This call is non-blocking.
4. The main thread can continue executing other code.
5. When the main thread needs the result, it calls `future.get()`. This call blocks the main thread until the Callable task completes.
6. If the `Callable` task throws an exception, `future.get()` re-throws it wrapped in an `ExecutionException`.

## `Future<V>` Interface: The Promise of a Result
### Overview and Importance
A `Future` represents the result of an asynchronous computation. It provides methods to check if the computation is complete, wait for its completion, and retrieve the result

**Key Methods**:
- `boolean isDone()`: Returns `true` if the task completed (normally, by throwing an exception, or by being cancelled).
- `boolean isCancelled()`: Returns `true` if the task was cancelled before it completed normally.
- `boolean cancel(boolean mayInterruptIfRunning)`: Attempts to cancel the execution of this task. `mayInterruptIfRunning` dictates whether the thread executing the task should be interrupted.
- `V get()`: Waits if necessary for the computation to complete, and then retrieves its result. Blocks indefinitely. Throws `InterruptedException` if the current thread was interrupted while waiting, or `ExecutionException` if the computation threw an exception.
- `V get(long timeout, TimeUnit unit)`: Waits if necessary for at most the given timeout for the computation to complete, and then retrieves its result. Throws `TimeoutException` if the wait timed out.

**Importance**: `Future` bridges the gap between submitting an asynchronous task and consuming its result later, providing a clear contract for managing the lifecycle and outcome of a background operation.

### Use Cases and Implementation
`Future` is intrinsically linked with `Callable` and `ExecutorService`, as shown in the previous example. Here, we highlight additional `Future` functionalities.

#### Intermediate Level: Checking Status and Timeout
```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeoutException;

// Intermediate Example: Future.isDone(), Future.cancel(), Future.get(timeout)
public class FutureFeaturesDemo {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(2);

        // Task that takes longer
        Callable<String> longRunningTask = () -> {
            System.out.println(Thread.currentThread().getName() + " started long-running task.");
            Thread.sleep(3000); // Simulate 3 seconds of work
            System.out.println(Thread.currentThread().getName() + " finished long-running task.");
            return "Long Task Done";
        };

        // Task that will be cancelled
        Callable<String> cancellableTask = () -> {
            System.out.println(Thread.currentThread().getName() + " started cancellable task.");
            try {
                Thread.sleep(5000); // Simulate 5 seconds of work
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + " was interrupted while sleeping.");
                Thread.currentThread().interrupt(); // Re-interrupt for best practice
                return "Cancellable Task Interrupted";
            }
            System.out.println(Thread.currentThread().getName() + " finished cancellable task normally.");
            return "Cancellable Task Done";
        };

        Future<String> futureLong = executor.submit(longRunningTask);
        Future<String> futureCancellable = executor.submit(cancellableTask);

        System.out.println("Main: Tasks submitted. Doing other work...");

        // Check status periodically
        int attempts = 0;
        while (!futureLong.isDone() && attempts < 10) {
            System.out.println("Main: Long task not yet done. Doing something else. (Attempt " + (++attempts) + ")");
            Thread.sleep(500);
        }

        // Try to get result with a timeout
        try {
            System.out.println("Main: Attempting to get long task result with 1-second timeout...");
            String result = futureLong.get(1, TimeUnit.SECONDS); // This will timeout if task takes > 1 sec
            System.out.println("Main: Long task result: " + result);
        } catch (TimeoutException e) {
            System.err.println("Main: Long task timed out!");
        } catch (ExecutionException e) {
            System.err.println("Main: Long task failed: " + e.getCause().getMessage());
        }

        // Cancel a task
        System.out.println("Main: Attempting to cancel cancellable task...");
        boolean cancelled = futureCancellable.cancel(true); // true means interrupt if running
        System.out.println("Main: Cancellable task cancelled status: " + cancelled);

        try {
            // Attempt to get result from cancelled task
            String result = futureCancellable.get();
            System.out.println("Main: Cancellable task result: " + result);
        } catch (java.util.concurrent.CancellationException e) {
            System.err.println("Main: Caught CancellationException for cancellable task, as expected.");
        } catch (ExecutionException e) {
            System.err.println("Main: Cancellable task failed: " + e.getCause().getMessage());
        }

        executor.shutdown();
        executor.awaitTermination(5, TimeUnit.SECONDS);
        System.out.println("Main: Executor shut down.");
    }
}
```

**Explanation**: <br>
- `future.isDone()`: Allows polling the status of the task without blocking.
- `future.get(timeout, unit)`: Provides a timeout mechanism. If the task doesn't complete within the specified time, a `TimeoutException` is thrown, preventing indefinite blocking.
- `future.cancel(true)`: Attempts to cancel the task. If the task is already running, `true` indicates that the thread executing it should be interrupted. It's up to the task to properly handle `InterruptedException` (e.g., by checking `Thread.currentThread().isInterrupted()` periodically if not performing blocking I/O). If cancelled, `future.get()` will throw a `CancellationException`.

### Common Pitfalls with `Future`
- **Blocking** `get()`: The primary drawback of `Future` is that `get()` is a blocking call. If you have multiple `Future`s and call `get()` on them sequentially, you lose the benefits of asynchronous execution, as each call blocks the calling thread until its respective task finishes.
- **No Asynchronous Callbacks**: `Future` doesn't provide a direct way to attach a callback function to be executed when the task completes, without blocking the calling thread. You have to actively poll (`isDone()`) or block (`get()`).
- **Limited Composition**: Combining multiple `Future`s (e.g., "run A and B in parallel, then C when both A and B are done") is cumbersome with `Future` alone, often requiring manual threading or additional `ExecutorService` submissions.
- **Error Handling Complexity**: While `ExecutionException` captures the task's error, handling it requires a `try-catch` block around every `get()` call, which can be verbose for complex flows.

## `CompletableFuture`: Enabling Asynchronous Composition (Java 8+)
`CompletableFuture` is a powerful addition in Java 8 that dramatically simplifies asynchronous programming. It implements `Future` and provides a rich API for chaining, combining, and composing multiple asynchronous operations in a non-blocking, reactive style. It solves the limitations of `Future` by allowing you to define what happens next after a task completes, rather than having to wait for it

### Overview and Importance
`CompletableFuture` extends `Future` and implements `CompletionStage`. CompletionStage defines a contract for an asynchronous computation that can be explicitly completed (or completed with an exception) and whose result can be used to trigger other dependent functions

**Key Concepts**:
- **CompletionStage**: Represents a stage in an asynchronous computation. Each stage can have zero or more dependent stages.
- **Non-Blocking**: You don't call `get()` immediately. Instead, you attach callbacks that will execute when the `CompletableFuture` completes.
- **Functional Style**: Leverages Java 8 Lambdas and Streams for concise and expressive asynchronous code.
- **Composition**: Allows chaining multiple asynchronous operations in complex ways:
  - **Transformation**: `thenApply()`, `thenApplyAsync()`
  - **Consumption**: `thenAccept()`, `thenAcceptAsync()`
  - **Chaining** (sequential): `thenCompose()`, `thenComposeAsync()`
  - **Combining** (parallel): `thenCombine()`, `thenCombineAsync()`
  - **Error Handling**: `exceptionally()`, `handle()`
  - **AnyOf/AllOf**: `anyOf()`, `allOf()`

**Importance**: `CompleteableFuture` is a cornerstone for building modern, highly scalable, and responsive applications, especially in microservices architectures where network calls are frequent and need to be handled asynchronously.

### Syntax, Use Cases, and Implementation
#### Beginner Level: Running Tasks Asynchronously and Attaching Callbacks
```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

// Beginner Example: Running a task and attaching a callback
public class CompletableFutureBeginner {

    public static void main(String[] args) throws InterruptedException {
        // Option 1: Run with ForkJoinPool.commonPool() (default for async methods)
        // System.out.println("Main: Starting task with default common pool.");
        // CompletableFuture.runAsync(() -> {
        //     System.out.println(Thread.currentThread().getName() + " running first task.");
        //     try { Thread.sleep(1000); } catch (InterruptedException e) {}
        //     System.out.println(Thread.currentThread().getName() + " finished first task.");
        // }).thenRun(() -> {
        //     System.out.println(Thread.currentThread().getName() + " running callback after first task.");
        // });

        // Option 2: Run with a custom ExecutorService
        ExecutorService customExecutor = Executors.newFixedThreadPool(2);

        System.out.println("Main: Starting task with custom executor.");
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " running computation (supplyAsync).");
            try { Thread.sleep(1500); } catch (InterruptedException e) {}
            return "Computation Result";
        }, customExecutor); // Supply a custom executor

        // Attach a callback to consume the result
        future.thenAccept(result -> {
            System.out.println(Thread.currentThread().getName() + " consumed result: " + result);
        });

        // Another callback to perform a transformation
        future.thenApply(result -> {
            return "Transformed " + result;
        }).thenAccept(transformedResult -> {
            System.out.println(Thread.currentThread().getName() + " consumed transformed result: " + transformedResult);
        });

        System.out.println("Main: Tasks initiated. Doing other work...");
        Thread.sleep(2000); // Allow time for async tasks to run

        customExecutor.shutdown();
        customExecutor.awaitTermination(5, TimeUnit.SECONDS);
        System.out.println("Main: Program End.");
    }
}
```

**Explanation**: <br>
1. `CompletableFuture.runAsync(Runnable task)`: Executes a `Runnable` task asynchronously. It returns a `CompletableFuture<Void>` because `Runnable` doesn't return a value.
2. `CompletableFuture.supplyAsync(Supplier<T> supplier)`: Executes a `Supplier` (a functional interface that supplies a result) asynchronously. It returns a `CompletableFuture<T>` where `T` is the type supplied by the `Supplier`.
3. **Default Executor**: By default, `runAsync` and `supplyAsync` use the `ForkJoinPool.commonPool()`. You can optionally pass an `Executor` as a second argument to specify a custom thread pool.
4. `thenAccept(Consumer<T> action)`: Attaches a `Consumer` to the `CompletableFuture`. This consumer will be executed when the `CompletableFuture` completes successfully, receiving its result. It returns a new `CompletableFuture<Void>`.
5. `thenApply(Function<T, R> fn)`: Attaches a `Function` to the `CompletableFuture`. This function transforms the result of the previous stage and returns a new `CompletableFuture<R>` representing the transformed result.

#### Intermediate Level: Chaining and Combining CompleteableFutures
```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

// Intermediate Example: Chaining (thenCompose) and Combining (thenCombine)
public class CompletableFutureIntermediate {

    // Simulates fetching user details from a remote service
    private static CompletableFuture<String> fetchUserDetails(int userId) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " Fetching user details for ID: " + userId);
            try { Thread.sleep(500); } catch (InterruptedException e) {}
            return "User " + userId + ": Alice";
        });
    }

    // Simulates fetching order details for a user
    private static CompletableFuture<String> fetchOrderDetails(String userDetails) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " Fetching orders for " + userDetails);
            try { Thread.sleep(700); } catch (InterruptedException e) {}
            return userDetails + " has Order #12345";
        });
    }

    // Simulates fetching product details for an order
    private static CompletableFuture<String> fetchProductDetails(String orderDetails) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " Fetching product for " + orderDetails);
            try { Thread.sleep(600); } catch (InterruptedException e) {}
            return orderDetails + " and Product 'Laptop'";
        });
    }

    // Simulates a parallel computation
    private static CompletableFuture<Integer> calculateDiscount(String userDetails) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " Calculating discount for " + userDetails);
            try { Thread.sleep(800); } catch (InterruptedException e) {}
            return userDetails.contains("Alice") ? 10 : 5; // Alice gets more discount
        });
    }

    public static void main(String[] args) throws InterruptedException {
        // Scenario 1: Chaining dependent async operations (sequential flow)
        // fetchUserDetails -> fetchOrderDetails -> fetchProductDetails
        System.out.println("Main: --- Chaining (thenCompose) Demo ---");
        CompletableFuture<String> chainedFuture = fetchUserDetails(101)
            .thenCompose(CompletableFutureIntermediate::fetchOrderDetails) // Chaining. flatMap equivalent
            .thenCompose(CompletableFutureIntermediate::fetchProductDetails);

        chainedFuture.thenAccept(finalResult -> {
            System.out.println(Thread.currentThread().getName() + " Final Chained Result: " + finalResult);
        }).exceptionally(ex -> {
            System.err.println(Thread.currentThread().getName() + " Chained process failed: " + ex.getMessage());
            return null; // Return null or some default if an exception occurs
        });

        Thread.sleep(2000); // Give time for the first chain to run

        // Scenario 2: Combining independent async operations (parallel flow)
        // fetchUserDetails AND calculateDiscount -> thenCombine their results
        System.out.println("\nMain: --- Combining (thenCombine) Demo ---");
        CompletableFuture<String> userFuture = fetchUserDetails(102);
        CompletableFuture<Integer> discountFuture = calculateDiscount("User 102: Bob");

        CompletableFuture<String> combinedFuture = userFuture
            .thenCombine(discountFuture, (userDetails, discount) -> {
                System.out.println(Thread.currentThread().getName() + " Combining user and discount results.");
                return userDetails + " gets " + discount + "% discount!";
            });

        combinedFuture.thenAccept(finalResult -> {
            System.out.println(Thread.currentThread().getName() + " Final Combined Result: " + finalResult);
        }).exceptionally(ex -> {
            System.err.println(Thread.currentThread().getName() + " Combined process failed: " + ex.getMessage());
            return null;
        });

        // Ensure all tasks complete
        CompletableFuture.allOf(chainedFuture, combinedFuture).join(); // Blocks main until both are done
        System.out.println("Main: All CompletableFutures finished.");
    }
}
```

**Explanation**: <br>
1. `thenCompose(Function<T, CompletionStage<U>> fn)`: Used for chaining dependent asynchronous operations. If `WorkspaceUserDetails` returns `CompletableFuture<String>`, and `WorkspaceOrderDetails` also returns a `CompletableFuture<String>`, `thenCompose` "flattens" the nested `CompletableFuture<CompletableFuture<String>>` into a single `CompletableFuture<String>`. This is similar to `flatMap` in Streams.
2. `thenCombine(CompletionStage<U> other, BiFunction<T, U, R> fn)`: Used for combining results from two independent `CompletableFutures`. Both futures run in parallel, and when both complete, the `BiFunction` is applied to their results. It returns a new `CompletableFuture<R>`.
3. `exceptionally(Function<Throwable, T> fn)`: Provides a way to handle exceptions. If the previous stage completes exceptionally, the `Function` is executed, and its result becomes the successful result of this `CompletableFuture`.
4. `CompletableFuture.allOf(CompletableFuture<?>... cfs)`: Returns a new `CompletableFuture<Void>` that is completed when all of the given `CompletableFutures` complete. Useful when you need to wait for multiple independent tasks to finish.
5. `join()`: A blocking call, similar to `Future.get()`, but it re-throws an unchecked `CompletionException` if the `CompletableFuture` completed exceptionally. It's often used at the very end of a chain in `main` methods for simple demos, but generally avoided in production code to maintain non-blocking behavior

#### Advanced Level: Error Handling, AnyOf, Custom Executors, and Timeouts
```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.function.Function;
import java.util.function.Supplier;

// Advanced Example: Error Handling, AnyOf, Custom Executors, and Timeouts
public class CompletableFutureAdvanced {

    private static ExecutorService customExecutor = Executors.newFixedThreadPool(5); // Custom executor

    // Simulates an API call that might succeed or fail
    private static CompletableFuture<String> callExternalService(String serviceName, long delayMillis, boolean fail) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " Calling " + serviceName + "...");
            try {
                Thread.sleep(delayMillis);
                if (fail) {
                    throw new RuntimeException("Failed to call " + serviceName);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                throw new RuntimeException(serviceName + " interrupted", e);
            }
            return "Response from " + serviceName;
        }, customExecutor); // Use custom executor
    }

    public static void main(String[] args) throws InterruptedException {
        // Scenario 1: Advanced Error Handling (handle and exceptionally)
        System.out.println("Main: --- Advanced Error Handling Demo ---");
        CompletableFuture<String> f1 = callExternalService("ServiceA", 1000, true) // This one will fail
            .thenApply(result -> "Processed: " + result) // This won't run if ServiceA fails
            .exceptionally(ex -> { // Catches exception from any previous stage
                System.err.println(Thread.currentThread().getName() + " Recovered from error: " + ex.getMessage());
                return "Fallback Data"; // Provides a fallback value
            });

        CompletableFuture<String> f2 = callExternalService("ServiceB", 1500, false) // This one will succeed
            .handle((result, ex) -> { // Handles both success and failure
                if (ex != null) {
                    System.err.println(Thread.currentThread().getName() + " Handle: ServiceB failed: " + ex.getMessage());
                    return "Error Data from Handle";
                } else {
                    System.out.println(Thread.currentThread().getName() + " Handle: ServiceB succeeded.");
                    return "Transformed by Handle: " + result;
                }
            });

        // Block until results are ready (for demo)
        System.out.println("Main: Result f1: " + f1.join());
        System.out.println("Main: Result f2: " + f2.join());

        // Scenario 2: `anyOf` - get result from the fastest completing future
        System.out.println("\nMain: --- AnyOf Demo ---");
        CompletableFuture<String> fastService = callExternalService("FastService", 500, false);
        CompletableFuture<String> slowService = callExternalService("SlowService", 2000, false);
        CompletableFuture<String> failingService = callExternalService("FailingService", 300, true);

        CompletableFuture<Object> fastestResultFuture = CompletableFuture.anyOf(fastService, slowService, failingService);

        fastestResultFuture.thenAccept(result -> {
            System.out.println(Thread.currentThread().getName() + " Fastest result: " + result);
        }).exceptionally(ex -> {
            System.err.println(Thread.currentThread().getName() + " AnyOf failed: " + ex.getMessage());
            return null;
        });

        // Scenario 3: Timeout for a CompletableFuture (using thenApply and thenApply to timeout)
        System.out.println("\nMain: --- Timeout Demo ---");
        CompletableFuture<String> longRunningTask = callExternalService("LongRunningTask", 4000, false);

        CompletableFuture<String> futureWithTimeout = longRunningTask.applyToEither(
            CompletableFuture.supplyAsync(() -> {
                try {
                    Thread.sleep(2000); // 2-second timeout
                } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
                throw new java.util.concurrent.CompletionException(new java.util.concurrent.TimeoutException("Operation timed out"));
            }, customExecutor),
            Function.identity() // If longRunningTask completes first, use its result
        ).exceptionally(ex -> {
            if (ex.getCause() instanceof TimeoutException) {
                System.err.println(Thread.currentThread().getName() + " Detected timeout: " + ex.getCause().getMessage());
                return "Default Value on Timeout";
            }
            throw new RuntimeException(ex); // Re-throw other exceptions
        });

        System.out.println("Main: Future with timeout result: " + futureWithTimeout.join()); // Block for demo

        // Shut down the custom executor
        customExecutor.shutdown();
        customExecutor.awaitTermination(5, TimeUnit.SECONDS);
        System.out.println("Main: Program End.");
    }
}
```

**Explanation of Advanced Features**: <br>
1. `handle(BiFunction<T, Throwable, R> fn)`: A more general error handling mechanism than `exceptionally()`. The `BiFunction` receives both the result (`T`) and the exception (`Throwable`) (one of them will be `null`). This allows you to handle success and failure in the same callback, making it versatile for transforming results or error values.
2. `CompletableFuture.anyOf(CompletableFuture<?>... cfs)`: Returns a new `CompletableFuture<Object>` that is completed when any of the given `CompletableFuture`s complete (either successfully or exceptionally). This is useful for "race" conditions, e.g., contacting multiple identical services and taking the response from the fastest one.
3. **Timeout**: `CompletableFuture` itself doesn't have a built-in timeout method for its result. A common pattern to implement a timeout is using `applyToEither`. You create a "timeout future" that completes after a certain delay with an exception. Then, `applyToEither` will complete with the result of whichever future finishes first. If the original task is slower than the timeout future, the timeout future's exception will propagate.

### Common Pitfalls with `CompleteableFuture`
- **Blocking Operations (misuse of** `join()`/`get()`): The primary advantage of `CompletableFuture` is non-blocking. Over-reliance on `join()` or `get()` defeats this purpose and turns your async code back into blocking code. Use them only when absolutely necessary (e.g., at the very end of your application's main flow, or for testing).
  **Default** `ForkJoinPool.commonPool()` **overuse**: If you use `supplyAsync` or `runAsync` without specifying an `Executor`, the `ForkJoinPool.commonPool()` is used. This pool is shared by the entire JVM. If your tasks block heavily (e.g., I/O operations), they can starve the common pool and impact other asynchronous operations in your application or even other libraries using the same pool. Always consider using a custom `ExecutorService` for I/O-bound or long-running tasks.
  **Ignoring Exceptions**: If you don't use `exceptionally()`, `handle()`, or explicitly call `join()`/`get()` on a `CompletableFuture` that completes exceptionally, the exception might be swallowed or only logged by the default `UncaughtExceptionHandler` of the thread pool, leading to silent failures.
  Lifecycle Management: CompletableFutures don't automatically shut down their backing ExecutorService. You are responsible for shutting down any custom executors you create.
- Asynchronous vs. Synchronous Methods (Async vs. non-Async):
  Methods like thenApply run in the same thread that completed the previous stage, or on the main thread if the previous stage was already completed.
  Methods like thenApplyAsync explicitly run their callback in an asynchronous manner, typically using the default ForkJoinPool.commonPool() or a provided Executor. Misunderstanding this can lead to unexpected thread usage or performance issues.
  Deadlocks: While CompletableFuture itself is non-blocking, if a task within the CompletableFuture chain performs a blocking operation (like waiting for a Lock) that can lead to a deadlock with other tasks in the same limited-size thread pool, it can still cause problems.