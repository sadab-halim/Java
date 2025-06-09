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
