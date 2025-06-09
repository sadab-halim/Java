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


---




