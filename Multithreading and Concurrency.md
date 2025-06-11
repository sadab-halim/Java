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