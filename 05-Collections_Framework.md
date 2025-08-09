# Collections Framework

## 1. Introduction to Collections Framework
### Hierarchy and Core Interfaces: Collection, List, Set, Queue, Map
The JCF is built upon a hierarchy of interfaces and classes. The root interface for all collections is `java.util.Collection`.
#### Diagram: Core Collections Framework Hierarchy
```
                                    Iterable
                                        |
                                    Collection<E>
                                     /    |    \
                                    /     |     \
                                 List<E>  Set<E>  Queue<E>
                                   |       |       |
                               ArrayList   HashSet  PriorityQueue
                               LinkedList  LinkedHashSet
                               Vector      TreeSet
                               Stack
                               Deque
                               ArrayDeque

                                    Map<K, V>
                                     /    \
                                    /      \
                                HashMap   TreeMap
                                LinkedHashMap
                                Hashtable
                                EnumMap
                                WeakHashMap
                                IdentityHashMap
                                ConcurrentHashMap
```

- `Iterable<T>`: The top of the iteration hierarchy. Any class implementing this interface can be iterated over using the enhanced for-loop (for-each loop). The `Collection` interface extends `Iterable`.
- `Collection<E>`: The root interface of the collection hierarchy. It defines common operations that all collections should support, such as `add()`, `remove()`, `size()`, `isEmpty()`, `contains()`, and `iterator()`. It does **not** directly provide implementations.
- `List<E>`: An **ordered collection** (also known as a sequence). Elements in a List have a specific order, and **duplicate elements are allowed**. It supports index-based access.
  - **Implementations**: `ArrayList`, `LinkedList`, `Vector`, `Stack`.
- `Set<E>`: A collection that **does not allow duplicate elements**. It models the mathematical set abstraction. The **order** of elements is generally **not guaranteed** (except for `LinkedHashSet`).
  - **Implementations**: `HashSet`, `LinkedHashSet`, `TreeSet`.
- `Queue<E>`: A collection designed for holding elements prior to processing. Besides basic Collection operations, queues provide additional insertion, extraction, and inspection operations. They typically (but not necessarily) order elements in a **FIFO** (first-in, first-out) manner.
  - **Implementations**: `LinkedList` (implements `Deque` which extends `Queue`), `PriorityQueue`, `ArrayDeque`.
- `Map<K, V>`: Not a true `Collection`. `Map` maps keys to values. **Keys must be unique, but values can be duplicated**. It provides efficient lookup of values based on their keys.
  - **Implementations**: `HashMap`, `LinkedHashMap`, `TreeMap`, `Hashtable`, `EnumMap`, `WeakHashMap`, `IdentityHashMap`, `ConcurrentHashMap`.

### Legacy vs. Modern Collections
| Feature         | Legacy Collections (e.g., `Vector`, `Hashtable`) | Modern Collections (e.g., `ArrayList`, `HashMap`)        |
|-----------------|--------------------------------------------------|----------------------------------------------------------|
| Synchronization | Thread-safe (`synchronized` methods)             | Not thread-safe by default (faster for single-threaded)  |
| Performance     | Slower due to inherent synchronization           | Faster due to lack of synchronization overhead           |
| API             | Older, less consistent APIs (e.g., `elements()`) | Consistent, richer APIs (e.g., `iterator()`)             |
| Iterator        | Uses `Enumeration`                               | Uses `Iterator` and `ListIterator`                       |
| Generics        | Not generic (pre-Java 5), operate on `Object`    | Generic (type-safe)                                      |
| Null Elements   | `Hashtable` does not allow null keys/values      | `HashMap` allows null keys/values (with caveats)         |

### Difference between Collection and Collections
- `java.util.Collection` **(Interface)**: The root interface of the collections hierarchy, as described above. It defines the common behavior for groups of objects. You implement `Collection` or its sub-interfaces (`List`, `Set`, `Queue`).
  ```java
  // Example: Collection as an interface
  List<String> names = new ArrayList<>(); // ArrayList implements List, which extends Collection
  Collection<String> collectionOfNames = names; // Polymorphism: a List is a Collection
  collectionOfNames.add("Alice");  
  ```
- `java.util.Collections` **(Utility Class)**: A utility class (all static methods) that provides various static methods for operating on or returning collections. These methods often provide functionalities like sorting, searching, making collections unmodifiable, synchronizing collections, etc. You use methods from `Collections`.
  ```java
  // Example: Collections as a utility class
  List<String> numbers = new ArrayList<>(List.of("One", "Two", "Three"));
  Collections.sort(numbers); // Using the static sort method from Collections class
  System.out.println(numbers); // Output: [One, Three, Two]
  List<String> unmodifiableList = Collections.unmodifiableList(numbers);
  // unmodifiableList.add("Four"); // Throws UnsupportedOperationException
  ```

---

## 2. Comparable vs. Comparator
These two interfaces define strategies for sorting objects in Java.
### Interface Definitions and Differences
- `Comparable<T>`:
  - **Definition**: `public interface Comparable<T> { int compareTo(T o); }`
  - **Purpose**: Defines the "natural ordering" of objects of a class. If a class implements `Comparable`, its instances can be sorted automatically (e.g., by `Collections.sort()` or `Arrays.sort()`) based on this natural ordering.
  - **Location**: Implemented by the class whose objects are being compared.
  - **Usage**: For sorting objects based on their intrinsic properties (e.g., `String`s are naturally sorted alphabetically, `Integer`s numerically).
  - **Impact**: Modifies the class itself. A class can implement only one `Comparable` interface.
- `Comparator<T>`:
  - **Definition**: `public interface Comparator<T> { int compare(T o1, T o2); boolean equals(Object obj); }` (The `equals` method is inherited from `Object` and rarely overridden).
  - **Purpose**: Defines an "external ordering" for objects. It provides a way to sort objects of a class based on criteria other than their natural ordering, or if the class doesn't implement `Comparable`.
  - **Location**: Implemented by a separate class (or provided as a lambda/method reference) that acts as a sorting strategy.
  - **Usage**: For sorting objects based on multiple criteria, custom rules, or when you don't have control over the class definition to implement `Comparable`.
  - **Impact**: Does not modify the class being compared. A class can be sorted by multiple `Comparator`s.

### Comparison Table: Comparable vs. Comparator
| Feature          | `Comparable`                          | `Comparator`                                             |
|------------------|---------------------------------------|----------------------------------------------------------|
| Interface        | `java.lang.Comparable<T>`             | `java.util.Comparator<T>`                                |
| Method           | `int compareTo(T o)`                  | `int compare(T o1, T o2)`                                |
| Location         | Implemented by the class itself       | Implemented by a separate class/lambda/method reference  |
| Natural Order    | Defines the "natural" sorting order   | Defines an "external" or "custom" sorting order          |
| Modification     | Modifies the class being sorted       | Does not modify the class being sorted                   |
| Number of Orders | Only one natural order                | Multiple custom orders possible                          |
| Package          | `java.lang`                           | `java.util`                                              |

### Sorting with `Collections.sort()` and `List.sort()`
- `Collections.sort(List<T> list)`: Sorts the elements of the specified list into ascending order, according to the natural ordering of its elements. All elements in the list must implement the Comparable interface.
   ```java
    import java.util.ArrayList;
    import java.util.Collections;
    import java.util.List;
    
    class Product implements Comparable<Product> {
        String name;
        double price;
    
        public Product(String name, double price) {
            this.name = name;
            this.price = price;
        }
    
        @Override
        public int compareTo(Product other) {
            // Natural ordering by name
            return this.name.compareTo(other.name);
        }
    
        @Override
        public String toString() {
            return "Product{" + "name='" + name + '\'' + ", price=" + price + '}';
        }
    }
    
    public class SortingExample {
        public static void main(String[] args) {
            List<Product> products = new ArrayList<>();
            products.add(new Product("Laptop", 1200.0));
            products.add(new Product("Mouse", 25.0));
            products.add(new Product("Keyboard", 75.0));
    
            System.out.println("Before sorting (natural order): " + products);
            Collections.sort(products); // Sorts by natural order (Product name)
            System.out.println("After sorting (natural order): " + products);
            // Output: After sorting (natural order): [Product{name='Keyboard', price=75.0}, Product{name='Laptop', price=1200.0}, Product{name='Mouse', price=25.0}]
        }
    }
    ```
  
- `Collections.sort(List<T> list, Comparator<? super T> c)`: Sorts the elements of the specified list according to the order induced by the specified comparator. This allows custom sorting logic.
   ```java
    // ... (Product class from above)
    
    // Using Comparator for sorting by price
    import java.util.Comparator;
    
    public class SortingWithComparator {
        public static void main(String[] args) {
            List<Product> products = new ArrayList<>();
            products.add(new Product("Laptop", 1200.0));
            products.add(new Product("Mouse", 25.0));
            products.add(new Product("Keyboard", 75.0));
    
            System.out.println("Before sorting: " + products);
    
            // Sort by price using an anonymous inner class Comparator (pre-Java 8 style)
            Collections.sort(products, new Comparator<Product>() {
                @Override
                public int compare(Product p1, Product p2) {
                    return Double.compare(p1.price, p2.price);
                }
            });
            System.out.println("After sorting by price: " + products);
            // Output: After sorting by price: [Product{name='Mouse', price=25.0}, Product{name='Keyboard', price=75.0}, Product{name='Laptop', price=1200.0}]
        }
    }
    ```
- `List.sort(Comparator<? super E> c)` **(Java 8+)**: This is a default method added to the `List` interface, allowing you to sort a list directly using a `Comparator`. This is generally preferred over `Collections.sort()` when you have a `List` instance.
   ```java
    // ... (Product class from above)
    
    public class ListSortExample {
        public static void main(String[] args) {
            List<Product> products = new ArrayList<>();
            products.add(new Product("Laptop", 1200.0));
            products.add(new Product("Mouse", 25.0));
            products.add(new Product("Keyboard", 75.0));
    
            System.out.println("Before sorting: " + products);
    
            // Sort by price using List.sort() with a lambda expression
            products.sort((p1, p2) -> Double.compare(p1.price, p2.price));
            System.out.println("After sorting by price (List.sort with lambda): " + products);
        }
    }
  ```

### Lambda, Method References, Chained Comparators
Java 8 introduced significant enhancements to Comparator with lambdas, method references, and default/static methods.
- **Lambda Expressions**: Concise way to implement functional interfaces like `Comparator`.
    ```java
    // Sort by name (descending)
    products.sort((p1, p2) -> p2.name.compareTo(p1.name));
    
    // Sort by price (ascending)
    products.sort((p1, p2) -> Double.compare(p1.price, p2.price));
    ```
- **Method References**: Even more concise for simple sorting criteria based on existing methods.
    ```java
    // Assuming Product has a getPrice() method
    // Sort by price (ascending) using method reference
    products.sort(Comparator.comparing(Product::getPrice));
    
    // Sort by name (descending) using method reference and reversed()
    products.sort(Comparator.comparing(Product::getName).reversed());
    ```
- **Chained Comparators**: Use `thenComparing()` to define secondary, tertiary, etc., sorting criteria.
    ```java
    // Sort by price (ascending), then by name (ascending) if prices are equal
    products.sort(Comparator.comparing(Product::getPrice)
                            .thenComparing(Product::getName));
    
    // Sort by price (descending), then by name (ascending)
    products.sort(Comparator.comparing(Product::getPrice).reversed()
                            .thenComparing(Product::getName));
    
    // Real-world example: Sorting a list of employees by department, then by salary, then by name
    class Employee {
        String name;
        String department;
        double salary;
    
        public Employee(String name, String department, double salary) {
            this.name = name;
            this.department = department;
            this.salary = salary;
        }
    
        public String getName() { return name; }
        public String getDepartment() { return department; }
        public double getSalary() { return salary; }
    
        @Override
        public String toString() {
            return "Employee{" + "name='" + name + '\'' + ", department='" + department + '\'' + ", salary=" + salary + '}';
        }
    }
    
    List<Employee> employees = new ArrayList<>();
    employees.add(new Employee("Alice", "HR", 60000));
    employees.add(new Employee("Bob", "IT", 75000));
    employees.add(new Employee("Charlie", "HR", 55000));
    employees.add(new Employee("David", "IT", 75000));
    
    employees.sort(Comparator.comparing(Employee::getDepartment)
                            .thenComparing(Employee::getSalary)
                            .thenComparing(Employee::getName));
    
    System.out.println("Sorted Employees: " + employees);
    /* Output:
    Sorted Employees: [
      Employee{name='Charlie', department='HR', salary=55000.0},
      Employee{name='Alice', department='HR', salary=60000.0},
      Employee{name='Bob', department='IT', salary=75000.0},
      Employee{name='David', department='IT', salary=75000.0}
    ]
    */
    ```
  
---

## 3. Lists and Deques
Lists are ordered collections allowing duplicates. Deques (Double-Ended Queues) allow elements to be added or removed from both ends.
### ArrayList, LinkedList, Vector, Stack
#### `ArrayList`
- **Internal Implementation**: Resizable array (dynamically growing array).
- **Characteristics**:
  - Random access (get/set by index) is O(1) because it's backed by an array.
  - Adding elements to the end is typically O(1) amortized time, but can be O(N) when resizing occurs (copying elements to a larger array).
  - Adding/removing elements from the middle is O(N) because subsequent elements need to be shifted.
  - Not thread-safe.
- **Use Cases**: When frequent random access is needed, and insertions/deletions are mostly at the end or rare.

#### LinkedList
- **Internal Implementation**: Doubly linked list. Each element (node) stores a reference to the previous and next elements.
- **Characteristics**:
  - Random access (get/set by index) is O(N) because it needs to traverse from the beginning or end.
  - Adding/removing elements from the beginning or end is O(1) because only pointers need to be updated.
  - Adding/removing elements from the middle is O(1) after finding the position (which itself is O(N) traversal).
  - Not thread-safe.
  - Also implements the `Deque` interface, making it suitable for queue and stack operations.
- **Use Cases**: When frequent insertions/deletions at either end are needed, or when iterating sequentially through the list.

#### Vector
- **Internal Implementation**: Resizable array, similar to ArrayList.
- **Characteristics**:
  - **Thread-safe**: All its public methods are synchronized. This adds overhead and can lead to performance degradation in single-threaded or low-concurrency scenarios.
  - Similar performance characteristics to ArrayList but slower due to synchronization.
  - Legacy class (pre-JCF).
- **Use Cases**: Rarely used in modern Java. If thread-safety is required, `CopyOnWriteArrayList` or `Collections.synchronizedList()` are generally preferred for better performance or more fine-grained control.

#### `Stack`
- **Internal Implementation**: Extends `Vector`, so it's backed by a resizable array and is thread-safe.
- **Characteristics**: Represents a Last-In, First-Out (LIFO) stack. Provides `push()`, `pop()`, `peek()`, `empty()`, `search()` methods.
- **Use Cases**: Simple LIFO operations. However, it's generally discouraged in favor of `Deque` implementations (`ArrayDeque` or `LinkedList`) for better performance and a more consistent API, especially since `Stack` is a legacy class and extending `Vector` introduces unnecessary overhead for a stack.

#### Deque, ArrayDeque, and their Usage Over Stack
- `Deque<E>` **(Double-Ended Queue)**:
  - **Interface**: Extends `Queue`.
  - **Purpose**: Supports element insertion and removal at both ends. It can be used as a queue (FIFO) or a stack (LIFO).
  - **Stack Operations (LIFO)**:
    - `push(e)`: Adds an element to the front (top of the stack).
    - `pop()`: Removes and returns the first element (top of the stack).
    - `peek()`: Retrieves, but does not remove, the first element.
  - **Queue Operations (FIFO)**:
    - `offer(e)`: Adds an element to the end.
    - `poll()`: Removes and returns the first element.
    - `peek()`: Retrieves, but does not remove, the first element.
- `ArrayDeque`:
  - **Internal Implementation**: Resizable array.
  - **Characteristics**:
    - **No capacity restrictions** (grows as needed).
    - Faster than `LinkedList` for stack and queue operations when adding/removing from ends.
    - Not thread-safe.
    - Does not support `null` elements.
  - **Use Cases**: Highly recommended for implementing both stacks and queues due to its efficiency.
- **Why** `ArrayDeque` **is preferred over** `Stack` **and** `LinkedList` **for stack/queue**:
  - `Stack`: `ArrayDeque` is faster because it's not synchronized and specifically optimized for deque operations. `Stack` extends `Vector`, inheriting unnecessary overhead.
  - `LinkedList`: While `LinkedList` can act as a `Deque`, `ArrayDeque` typically offers better performance for stack and queue operations (add/remove from ends) due to its array-based contiguous memory allocation, which benefits from CPU cache locality. `LinkedList` involves more object creation (nodes) and pointer manipulation.

#### Code Example: `ArrayDeque` as a Stack and Queue
```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.LinkedList; // Can also be used as a Deque

public class DequeExample {
    public static void main(String[] args) {
        // Using ArrayDeque as a Stack (LIFO)
        Deque<String> stack = new ArrayDeque<>();
        stack.push("First pushed");
        stack.push("Second pushed");
        stack.push("Third pushed");
        System.out.println("Stack (ArrayDeque): " + stack); // [Third pushed, Second pushed, First pushed]
        System.out.println("Pop: " + stack.pop());     // Third pushed
        System.out.println("Peek: " + stack.peek());    // Second pushed
        System.out.println("Stack after pop: " + stack); // [Second pushed, First pushed]

        System.out.println("\n---");

        // Using ArrayDeque as a Queue (FIFO)
        Deque<String> queue = new ArrayDeque<>();
        queue.offer("First in");
        queue.offer("Second in");
        queue.offer("Third in");
        System.out.println("Queue (ArrayDeque): " + queue); // [First in, Second in, Third in]
        System.out.println("Poll: " + queue.poll());     // First in
        System.out.println("Peek: " + queue.peek());    // Second in
        System.out.println("Queue after poll: " + queue); // [Second in, Third in]

        System.out.println("\n---");

        // LinkedList as Deque (can also be used, but generally ArrayDeque is preferred for performance)
        Deque<Integer> linkedListDeque = new LinkedList<>();
        linkedListDeque.addFirst(1); // Push to front (stack-like)
        linkedListDeque.addLast(2);  // Add to end (queue-like)
        System.out.println("LinkedList as Deque: " + linkedListDeque); // [1, 2]
    }
}
```

### Performance Comparison
| Operation                  | `ArrayList` (Array) | `LinkedList` (Doubly Linked List)   | `Vector` (Synchronized Array)      | `ArrayDeque` (Resizable Array) | `Stack` (Extends Vector) |
|----------------------------|---------------------|-------------------------------------|------------------------------------|--------------------------------|--------------------------|
| `get(index)`               | O(1)                | O(N)                                | O(1) (Synchronized)                | N/A (no direct indexed get)    | O(N) (search)            |
| `add(E) (end)`             | O(1) amortized      | O(1)                                | O(1) amortized (Synchronized)      | O(1) amortized (offerLast)     | O(1) amortized (push)    |
| `add(index, E)`            | O(N)                | O(N) (find pos), then O(1)          | O(N) (Synchronized)                | N/A                            | N/A                      |
| `remove(index)`            | O(N)                | O(N) (find pos), then O(1)          | O(N) (Synchronized)                | N/A                            | O(N) (remove element)    |
| `remove()` **(end/front)** | O(1) (remove last)  | O(1)                                | O(1) (remove last) (Synchronized)  | O(1) (pollFirst/pollLast)      | O(1) (pop)               |
| **Iteration**              | O(N)                | O(N)                                | O(N) (Synchronized)                | O(N)                           | O(N)                     |
| **Memory**                 | Contiguous          | Dispersed (more overhead per node)  | Contiguous                         | Contiguous                     | Contiguous               |
| **Thread-Safe**            | No                  | No                                  | Yes                                | No                             | Yes                      |

### Common Pitfalls (e.g., resizing in ArrayList)
- `ArrayList` **Resizing**:
  - When an `ArrayList`'s internal array runs out of capacity, it creates a new, larger array (typically 1.5 times the current size) and copies all existing elements to the new array.
  - **Pitfall**: Frequent additions to an `ArrayList` that is constantly resizing can lead to performance bottlenecks due to the O(N) copy operation.
  - **Best Practice**: If the approximate size of the list is known in advance, initialize the `ArrayList` with an appropriate initial capacity using `new ArrayList<>(initialCapacity)`. This can significantly reduce resizing overhead.
   ```java
    // Bad: frequent resizing
    List<Integer> numbers = new ArrayList<>();
    for (int i = 0; i < 100000; i++) {
        numbers.add(i); // May cause many resizes
    }
    
    // Good: pre-allocate capacity
    List<Integer> numbersOptimized = new ArrayList<>(100000);
    for (int i = 0; i < 100000; i++) {
        numbersOptimized.add(i); // Fewer or no resizes
    }
  ```

- `LinkedList` **Traversal for Index-based Operations**:
  - Accessing elements by index (e.g., `get(i)`, `set(i, E)`) in a `LinkedList` is inefficient (O(N)) because it requires traversing the list from the beginning or end.
  - **Pitfall**: Using `LinkedList` when frequent random access is needed.
  - **Best Practice**: Use `ArrayList` for random access. Use `LinkedList` when frequent additions/removals at the ends or efficient sequential iteration are primary requirements.
- **Modifying Collections During Iteration (Fail-Fast)**:
  - Most non-concurrent `Collection` implementations (like `ArrayList`, `HashMap`, `HashSet`) have "fail-fast" iterators. If the collection is structurally modified (elements added or removed, except by the iterator's own `remove()` method) while an iteration is in progress, a `ConcurrentModificationException` will be thrown.
  - **Pitfall**: Trying to add or remove elements from a list within a simple for-each loop.
  - **Best Practice**:
    - Use `Iterator.remove()` to remove the current element.
    - Iterate in reverse if removing elements by index in a `for` loop.
    - Create a new list to store elements to be removed/added, and then perform the modifications after iteration.
    - For concurrent modifications, use `CopyOnWriteArrayList` (for read-heavy scenarios) or `ConcurrentHashMap`.
   ```java
    List<String> items = new ArrayList<>(List.of("A", "B", "C", "D"));
    
    // Pitfall: Throws ConcurrentModificationException
    // for (String item : items) {
    //     if ("B".equals(item)) {
    //         items.remove(item);
    //     }
    // }
    
    // Best Practice: Using Iterator.remove()
    Iterator<String> iterator = items.iterator();
    while (iterator.hasNext()) {
        String item = iterator.next();
        if ("B".equals(item)) {
            iterator.remove(); // Safe removal
        }
    }
    System.out.println("Items after safe removal: " + items); // [A, C, D]
    ```
  
---

## 4. Sets
Sets are collections that store unique elements. They model the mathematical set abstraction.

### `HashSet`, `LinkedHashSet`, `TreeSet`
#### HashSet
- **Internal Implementation**: Backed by a `HashMap`. Elements are stored as keys in the internal `HashMap`, and a dummy `Object` (a static final `PRESENT` object) is used as the value.
- **Characteristics**:
  - **Unordered**: Does not maintain the insertion order of elements.
  - **No duplicates**: Ensures uniqueness using the `hashCode()` and `equals()` methods of the stored objects.
  - **Performance**: O(1) average time for `add()`, `remove()`, `contains()`, and `size()` operations, assuming good hash function distribution. Worst-case O(N) if many collisions occur.
  - Allows one `null` element.
  - Not thread-safe.
- **Use Cases**: When quick lookups for existence are needed, and order is not important. Good for eliminating duplicates.

#### `LinkedHashSet`
- **Internal Implementation**: Backed by a `LinkedHashMap`. It maintains a doubly-linked list running through its entries.
- **Characteristics**:
  - **Maintains insertion order**: Iterates in the order elements were inserted.
  - **No duplicates**: Ensures uniqueness using `hashCode()` and `equals()`.
  - **Performance**: Slightly slower than `HashSet` due to the overhead of maintaining the linked list, but still O(1) average for basic operations.
  - Allows one `null` element.
  - Not thread-safe.
- **Use Cases**: When you need a set that preserves the order of elements (e.g., remembering the order of unique visitors to a website).

#### `TreeSet`
- **Internal Implementation**: Backed by a `TreeMap`. Elements are stored as keys in the internal `TreeMap`, and a dummy `Object` is used as the value. The `TreeMap` uses a Red-Black Tree for storing elements.
- **Characteristics**:
  - **Sorted**: Stores elements in their natural order (if they implement `Comparable`) or according to a `Comparator` provided at construction.
  - **No duplicates**: Uniqueness is determined by the `compareTo()` method (or `compare()` of the `Comparator`).
  - **Performance**: O(log N) for `add()`, `remove()`, `contains()`, and `size()` operations due to tree traversal.
  - Does **not** allow `null` elements (throws `NullPointerException` if you try to add one).
  - Not thread-safe.
  - Implements `NavigableSet`.
- **Use Cases**: When you need a sorted set, or to perform range queries (e.g., finding all elements within a certain range).

#### Diagram: Set Implementations
```
        Set
       / | \
      /  |  \
 HashSet   LinkedHashSet   TreeSet
    (HashMap)  (LinkedHashMap) (TreeMap / Red-Black Tree)
    Unordered   Insertion Order   Sorted (Natural / Custom)
```

#### Importance of `hashCode()` and `equals()`
For `HashSet` and `LinkedHashSet`, correctly implementing `hashCode()` and `equals()` is absolutely critical for proper functionality.
- `hashCode()`: Returns an integer hash code value for the object.
- `equals(Object obj)`: Indicates whether some other object is "equal to" this one.

Contract between `hashCode()` and `equals()`:
- If two objects are equal according to the `equals(Object)` method, then calling the `hashCode()` method on each of the two objects must produce the same integer result.
- If two objects are unequal according to the `equals(Object)` method, it is not required that calling the `hashCode()` method on each of the two objects must produce different integer results. However, producing different integer results for unequal objects can improve the performance of hash tables.

#### Why it matters for `HashSet`:
- When you `add()` an object to a `HashSet`, it first calculates the object's `hashCode()`.
- It then uses this hash code to determine which "bucket" (or array index) the object should go into.
- If multiple objects hash to the same bucket (a collision), `HashSet` (via its internal `HashMap`) uses `equals()` to determine if the object already exists in that bucket.
- If `hashCode()` is not implemented correctly (e.g., returns a constant value, or values that don't match `equals` logic), all objects might end up in the same bucket, degrading performance to O(N).
- If `equals()` is not implemented correctly (e.g., two logically equal objects return false), `HashSet` might store duplicates.

#### Example of incorrect `hashCode`/`equals`:
```java
class BadPoint {
    int x, y;
    public BadPoint(int x, int y) { this.x = x; this.y = y; }
    // No equals() or hashCode() override (uses Object's default)
}

class GoodPoint {
    int x, y;
    public GoodPoint(int x, int y) { this.x = x; this.y = y; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        GoodPoint goodPoint = (GoodPoint) o;
        return x == goodPoint.x && y == goodPoint.y;
    }

    @Override
    public int hashCode() {
        // Simple hash calculation (can be improved with Objects.hash)
        int result = x;
        result = 31 * result + y;
        return result;
        // Or using Objects.hash: return Objects.hash(x, y);
    }

    @Override
    public String toString() { return "(" + x + "," + y + ")"; }
}

public class SetHashingDemo {
    public static void main(String[] args) {
        Set<BadPoint> badPoints = new HashSet<>();
        badPoints.add(new BadPoint(1, 2));
        badPoints.add(new BadPoint(1, 2)); // This will be added as a duplicate!
        System.out.println("BadPoint Set size: " + badPoints.size()); // Output: 2

        Set<GoodPoint> goodPoints = new HashSet<>();
        goodPoints.add(new GoodPoint(1, 2));
        goodPoints.add(new GoodPoint(1, 2)); // This will NOT be added as a duplicate!
        System.out.println("GoodPoint Set size: " + goodPoints.size()); // Output: 1
    }
}
```

**Recommendation**: Always override both `equals()` and `hashCode()` together, or neither. Use `Objects.equals()` and `Objects.hash()` (Java 7+) for robust and concise implementations. Most IDEs (IntelliJ, Eclipse) can auto-generate these methods.

### NavigableSet and Range Queries in TreeSet
`TreeSet` implements the `NavigableSet` interface, which extends `SortedSet`. `NavigableSet` provides methods for finding elements closest to a given value, and for creating subsets.
- `lower(e)`: Returns the greatest element in this set strictly less than `e`, or `null` if there is no such element.
- `floor(e)`: Returns the greatest element in this set less than or equal to `e`, or `null`.
- `ceiling(e)`: Returns the least element in this set greater than or equal to `e`, or `null`.
- `higher(e)`: Returns the least element in this set strictly greater than `e`, or `null`.
- `pollFirst()`: Retrieves and removes the first (lowest) element, or ``null`` if empty.
- `pollLast()`: Retrieves and removes the last (highest) element, or null if empty.
- `descendingSet()`: Returns a reverse order view of the elements contained in this set.
- `subSet(fromElement, fromInclusive, toElement, toInclusive)`: Returns a `NavigableSet` view of the portion of this set whose elements range from `fromElement` to `toElement`.
- `headSet(toElement, inclusive)`: Returns a `NavigableSet` view of the portion of this set whose elements are less than (or equal to, if `inclusive` is true) `toElement`.
- `tailSet(fromElement, inclusive)`: Returns a `NavigableSet` view of the portion of this set whose elements are greater than (or equal to, if `inclusive` is true) `fromElement`.

#### Example: Range Queries in `TreeSet`
```java
import java.util.NavigableSet;
import java.util.TreeSet;

public class TreeSetRangeQuery {
    public static void main(String[] args) {
        NavigableSet<Integer> numbers = new TreeSet<>();
        numbers.add(10);
        numbers.add(20);
        numbers.add(30);
        numbers.add(40);
        numbers.add(50);

        System.out.println("Original TreeSet: " + numbers); // [10, 20, 30, 40, 50]

        // Find elements strictly less than 30
        System.out.println("Elements < 30: " + numbers.lower(30)); // 20

        // Find elements <= 30
        System.out.println("Elements <= 30: " + numbers.floor(30)); // 30

        // Find elements >= 30
        System.out.println("Elements >= 30: " + numbers.ceiling(30)); // 30

        // Find elements strictly > 30
        System.out.println("Elements > 30: " + numbers.higher(30)); // 40

        // Sub-set: elements from 20 (inclusive) to 40 (exclusive)
        NavigableSet<Integer> subSet = numbers.subSet(20, true, 40, false);
        System.out.println("SubSet [20, 40): " + subSet); // [20, 30]

        // Head-set: elements < 30 (inclusive)
        NavigableSet<Integer> headSet = numbers.headSet(30, true);
        System.out.println("HeadSet <= 30: " + headSet); // [10, 20, 30]

        // Tail-set: elements >= 30 (inclusive)
        NavigableSet<Integer> tailSet = numbers.tailSet(30, true);
        System.out.println("TailSet >= 30: " + tailSet); // [30, 40, 50]

        System.out.println("First element: " + numbers.pollFirst()); // 10
        System.out.println("Last element: " + numbers.pollLast());   // 50
        System.out.println("TreeSet after poll: " + numbers); // [20, 30, 40]
    }
}
```

---

## 5. Maps
Maps are objects that map keys to values. They cannot contain duplicate keys; each key can map to at most one value.
### HashMap: Internal Implementation, Hashing, Collision Resolution (Chaining vs. Tree)
- **Internal Implementation**: An array of `Node` objects (or `Entry` objects in older Java versions). Each `Node` represents a key-value pair and often points to the next `Node` in a linked list in case of collisions. Since Java 8, if a bucket contains too many elements (default 8) that have the same hash code, the linked list is converted into a balanced tree (`TreeNode` - Red-Black tree) to improve performance from O(N) to O(log N) in the worst-case scenario. When the number of elements in a treeified bin falls below a threshold (default 6), it converts back to a linked list.
- **Hashing**: When you call `put(key, value)` or `get(key)`:
  - The `hashCode()` method of the key object is called.
  - This hash code is then used to determine the index in the internal array (bucket) where the entry should be stored. The formula typically involves hash & (capacity - 1).
- **Collision Resolution**:
  - **Chaining (pre-Java 8 and for small buckets in Java 8+)**: If two different keys hash to the same bucket (collision), the `Node`s are stored in a linked list at that array index. When searching, the linked list is traversed, and `equals()` is used to find the correct entry.
  - **Treeification (Java 8+)**: To mitigate worst-case O(N) performance for buckets with many collisions (e.g., due to a poor `hashCode()` implementation or adversarial inputs), `HashMap` converts the linked list in a bucket to a Red-Black tree if the number of elements in that bucket exceeds `TREEIFY_THRESHOLD` (default 8). This changes lookup/insertion/deletion within that bucket from O(N) to O(log N).
    - **Untreeify**: If elements are removed and the number of nodes in a treeified bin falls below `UNTREEIFY_THRESHOLD` (default 6), the tree converts back to a linked list.
    - **Resize Threshold for Treeification**: Treeification only happens if the `HashMap`'s overall capacity is above a certain `MIN_TREEIFY_CAPACITY` (default 64). Below this, the `HashMap` will prefer to resize (double its capacity) rather than treeify, as resizing spreads out elements and reduces collisions more generally.

#### Diagram: HashMap Internal Structure (Chaining and Treeified Bin)
```
[Array of Buckets]
     |
     V
   +---+
0: | O | --> Node1 (Key1, Value1) --> Node2 (Key2, Value2)  (Linked List - Chaining)
   +---+
1: | O |
   +---+
2: | O | --> TreeNodeA (KeyA, ValueA)
   +---+     /        \
3: | O |    /          \
   +---+   TreeNodeB   TreeNodeC        (Red-Black Tree - Treeified Bin)
4: | O |
   +---+
...
   +---+
N: | O |
   +---+
```

#### LinkedHashMap: Insertion and Access Order, LRU Cache Pattern
- **Internal Implementation**: Combines a hash table (like HashMap) with a doubly-linked list running through all its entries.
- **Characteristics**:
  - **Maintains insertion order by default**: Iterates in the order elements were inserted.
  - Can also be configured to maintain **access order** (elements are ordered from least-recently accessed to most-recently accessed).
  - **Performance**: O(1) average for `put()`, `get()`, `remove()`. Iteration is faster than HashMap (O(N) vs. O(capacity + N)).
  - Allows one `null` key and multiple `null` values.
  - Not thread-safe.
- **Use Cases**:
  - When you need a `Map` that preserves insertion order (e.g., building a history of operations).
  - Implementing an LRU (Least Recently Used) cache: By configuring it for access order and overriding `removeEldestEntry()`, `LinkedHashMap` can automatically remove the least recently accessed entry when the map size exceeds a certain limit.

#### Code Example: LRU Cache with `LinkedHashMap`
```java
import java.util.LinkedHashMap;
import java.util.Map;

class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    public LRUCache(int capacity) {
        // accessOrder = true ensures entries are ordered by access
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // Return true to remove the eldest entry when size exceeds capacity
        return size() > capacity;
    }

    public static void main(String[] args) {
        LRUCache<Integer, String> cache = new LRUCache<>(3); // Cache capacity of 3

        cache.put(1, "One");
        cache.put(2, "Two");
        cache.put(3, "Three");
        System.out.println("Initial cache: " + cache); // {1=One, 2=Two, 3=Three}

        cache.get(1); // Access 1, moves it to the end (most recently accessed)
        System.out.println("After access 1: " + cache); // {2=Two, 3=Three, 1=One}

        cache.put(4, "Four"); // Add 4, 2 (eldest) will be removed
        System.out.println("After adding 4: " + cache); // {3=Three, 1=One, 4=Four}

        cache.put(5, "Five"); // Add 5, 3 (eldest) will be removed
        System.out.println("After adding 5: " + cache); // {1=One, 4=Four, 5=Five}
    }
}
```

### `TreeMap`: Red-Black Tree, Sorting Keys
- **Internal Implementation**: A Red-Black tree. This is a self-balancing binary search tree.
- **Characteristics**:
  - **Sorted by keys**: Keys are stored in their natural order (if they implement `Comparable`) or according to a `Comparator` provided at construction.
  - **No duplicates**: Uniqueness of keys is determined by `compareTo()` (or `compare()` of the `Comparator`).
  - **Performance**: O(log N) for `put()`, `get()`, `remove()`, and `size()` operations due to tree traversal.
  - Does not allow `null` keys (throws `NullPointerException`). Allows multiple null values.
  - Not thread-safe.
  - Implements `NavigableMap`.
- **Use Cases**:
  - When you need a sorted map (e.g., storing a dictionary where words are sorted alphabetically).
  - Performing range queries on keys (e.g., finding all users whose IDs are between X and Y).

#### Other Specialized Maps: EnumMap, WeakHashMap, IdentityHashMap, ConcurrentHashMap
- `EnumMap<K extends Enum<K>, V>`:
  - **Purpose**: Specialized `Map` implementation for use with enum keys.
  - **Characteristics**: Internally backed by an array. Extremely efficient and high-performance because it knows the exact set of possible keys (the enum constants). Does not use `hashCode()`/``equals()``.
  - **Performance**: O(1) for all basic operations, extremely fast.
  - Does not allow `null` keys.
  - Not thread-safe.
  - **Use Cases**: When mapping enum constants to values, e.g., mapping HTTP status codes (enums) to their descriptions.
   ```java
   import java.util.EnumMap;
    import java.util.Map;

    enum Day {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
    }
    
    public class EnumMapExample {
        public static void main(String[] args) {
            EnumMap<Day, String> activityMap = new EnumMap<>(Day.class);
            activityMap.put(Day.MONDAY, "Work");
            activityMap.put(Day.SATURDAY, "Weekend Fun");
            System.out.println("Activities: " + activityMap);
        }
    }
  ```

- `WeakHashMap<K, V>`:
  - **Purpose**: A `HashMap` implementation where keys are stored as `WeakReferences`.
  - **Characteristics**: If a key is no longer strongly referenced by any other part of your program, it can be garbage collected, and its entry will be automatically removed from the `WeakHashMap`. This is useful for building caches where you don't want the cache itself to prevent objects from being garbage collected.
  - **Performance**: Similar to `HashMap` (O(1) average), but with additional overhead for weak reference management.
  - Not thread-safe.
  - **Use Cases**: Caches that shouldn't hold onto objects strongly, metadata maps for objects that can be garbage collected.
   ```java
   import java.lang.ref.WeakReference;
    import java.util.Map;
    import java.util.WeakHashMap;
    
    public class WeakHashMapExample {
        public static void main(String[] args) {
            Map<Object, String> weakMap = new WeakHashMap<>();
            Object key1 = new Object();
            Object key2 = new Object();
    
            weakMap.put(key1, "Value for Key1");
            weakMap.put(key2, "Value for Key2");
            System.out.println("Initial WeakHashMap: " + weakMap); // {java.lang.Object@...=Value for Key2, java.lang.Object@...=Value for Key1}
    
            key1 = null; // Remove strong reference to key1
            System.gc(); // Request garbage collection (not guaranteed to run immediately)
    
            // Wait a bit to allow GC to happen
            try { Thread.sleep(100); } catch (InterruptedException e) {}
    
            System.out.println("WeakHashMap after GC (key1 might be gone): " + weakMap);
            // Output might vary, but eventually key1's entry will be removed
            // e.g., {java.lang.Object@...=Value for Key2} or {}
        }
    }
  ```

- `IdentityHashMap<K, V>`:
  - **Purpose**: A `HashMap` implementation that uses reference equality (`==`) instead of object equality (`equals()`) when comparing keys.
  - **Characteristics**: Two keys `k1` and `k2` are considered equal if and only if `k1 == k2`. This means that even if `k1.equals(k2)` is true, they are treated as different keys if they are different object instances.
  - **Performance**: Similar to `HashMap` (O(1) average).
  - Not thread-safe.
  - **Use Cases**: Rarely needed. Used when you specifically need to distinguish between objects that are logically equal but are distinct instances. Often seen in serialization or object graph traversal where object identity is crucial.
   ```java
   import java.util.HashMap;
    import java.util.IdentityHashMap;
    import java.util.Map;
    
    public class IdentityHashMapExample {
        public static void main(String[] args) {
            String s1 = new String("hello");
            String s2 = new String("hello"); // s1.equals(s2) is true, but s1 != s2
    
            Map<String, Integer> hashMap = new HashMap<>();
            hashMap.put(s1, 1);
            hashMap.put(s2, 2);
            System.out.println("HashMap size: " + hashMap.size()); // Output: 1 (s1 and s2 are considered equal keys)
    
            Map<String, Integer> identityHashMap = new IdentityHashMap<>();
            identityHashMap.put(s1, 1);
            identityHashMap.put(s2, 2);
            System.out.println("IdentityHashMap size: " + identityHashMap.size()); // Output: 2 (s1 and s2 are considered different keys)
        }
    }
  ```

- `ConcurrentHashMap<K, V>`: (Discussed in detail under Thread-Safe & Concurrent Collections)
  - **Purpose**: A thread-safe, high-performance `HashMap` implementation designed for concurrent access.
  - **Characteristics**: Offers much better concurrency than `Hashtable` or `Collections.synchronizedMap()` by allowing multiple readers and a limited number of writers to access the map concurrently without blocking the entire map.
  - **Use Cases**: High-concurrency environments where multiple threads read from and write to a map.

---

## 6. Queues and PriorityQueues
Queues are collections designed for holding elements prior to processing.

### FIFO vs. LIFO Queues
- **FIFO (First-In, First-Out)**: Elements are processed in the order they were added. The first element added is the first one removed.
  - **Examples**: Waiting lines, print queues, message brokers.
  - **Implementations**: `LinkedList` (as a `Queue`), `ArrayDeque` (as a `Queue`), `PriorityQueue` (though it sorts by priority, not strict FIFO). `ConcurrentLinkedQueue`, `LinkedBlockingQueue`.
- **LIFO (Last-In, First-Out)**: Elements are processed in reverse order of addition. The last element added is the first one removed.
  - **Examples**: Function call stacks, undo mechanisms in software.
  - **Implementations**: `Stack` (legacy, discouraged), `ArrayDeque` (as a `Deque`/`Stack`), `LinkedList` (as a `Deque`/`Stack`).

### PriorityQueue (Min-Heap), Custom Comparators
- **Internal Implementation**: A min-heap (binary heap). The smallest element is always at the root.
- **Characteristics**:
  - Elements are ordered based on their natural ordering (if `Comparable`) or by a `Comparator` provided at construction.
  - The `poll()` and `peek()` methods retrieve the smallest element (highest priority) based on this ordering.
  - Not thread-safe.
  - Does not allow `null` elements.
  - The head of the queue is the least element according to the specified ordering. If multiple elements are tied for the least value, any one of them may be retrieved.
  - It does not provide O(1) random access. Iterating over a `PriorityQueue` does not guarantee elements are visited in sorted order.
- **Performance**:
  - `offer()` (insert): O(log N)
  - `poll()` (remove smallest): O(log N)
  - `peek()` (get smallest): O(1)
- **Use Cases**:
  - Task scheduling (e.g., always process the task with the highest priority first).
  - Implementing graph algorithms like Dijkstra's (finding shortest path) or Prim's (minimum spanning tree).
  - Finding the k-th smallest/largest element efficiently.

#### Code Example: `PriorityQueue` with Custom Comparator
```java
import java.util.PriorityQueue;
import java.util.Comparator;

class Task {
    String name;
    int priority; // Lower number means higher priority

    public Task(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }

    public int getPriority() { return priority; }

    @Override
    public String toString() {
        return "Task{" + "name='" + name + '\'' + ", priority=" + priority + '}';
    }
}

public class PriorityQueueExample {
    public static void main(String[] args) {
        // PriorityQueue ordered by Task priority (min-heap)
        PriorityQueue<Task> taskQueue = new PriorityQueue<>(Comparator.comparing(Task::getPriority));

        taskQueue.offer(new Task("Write Report", 3));
        taskQueue.offer(new Task("Fix Bug", 1));
        taskQueue.offer(new Task("Attend Meeting", 2));
        taskQueue.offer(new Task("Deploy App", 1));

        System.out.println("Tasks in priority order:");
        while (!taskQueue.isEmpty()) {
            System.out.println(taskQueue.poll()); // Removes and returns highest priority (lowest number)
        }
        /* Output:
        Tasks in priority order:
        Task{name='Fix Bug', priority=1}
        Task{name='Deploy App', priority=1}
        Task{name='Attend Meeting', priority=2}
        Task{name='Write Report', priority=3}
        */
        // Note: For tasks with same priority (Fix Bug, Deploy App), order is arbitrary.
    }
}
```

### Real-world Usage: Task Scheduling, Dijkstra’s Algorithm
- **Task Scheduling**: A common use case for `PriorityQueue`. Tasks with higher priority (e.g., lower numerical value) are processed first. This is crucial in operating systems, job queues, or real-time systems.
- **Dijkstra's Algorithm (Shortest Path)**: In graph algorithms, Dijkstra's algorithm uses a `PriorityQueue` to efficiently select the next node to visit. The priority of a node is its current shortest distance from the source. The `PriorityQueue` always returns the unvisited node with the smallest distance, ensuring the algorithm explores paths in an optimal order.

---

## 7. Utility Classes
The Java Collections Framework provides two main utility classes: `Collections` and `Arrays`. Both consist exclusively of static methods that operate on or return collections or arrays.

### `Collections` and `Arrays` Utility Methods

#### `java.util.Collections`
As mentioned earlier, `Collections` provides static methods for operating on or returning collections.
- **Sorting and Searching**:
  - `sort(List<T> list)`: Sorts a list in natural order.
  - `sort(List<T> list, Comparator<? super T> c)`: Sorts a list with a custom comparator.
  - `binarySearch(List<? extends Comparable<? super T>> list, T key)`: Searches a sorted list for the specified key using the binary search algorithm.
  - `binarySearch(List<? extends T> list, T key, Comparator<? super T> c)`: Searches with a custom comparator.
- **Manipulation**:
  - `reverse(List<?> list)`: Reverses the order of the elements in the specified list.
  - `shuffle(List<?> list)`: Randomly permutes the specified list.
  - `fill(List<? super T> list, T obj)`: Replaces all elements of the specified list with the specified element.
  - `copy(List<? super T> dest, List<? extends T> src)`: Copies all of the elements from one list into another.
  - `replaceAll(List<T> list, T oldVal, T newVal)`: Replaces all occurrences of one specified value in a list with another.
  - `rotate(List<?> list, int distance)`: Rotates the elements in the specified list by the specified distance.
- **Min/Max**:
  - `min(Collection<? extends T> coll)`: Returns the minimum element of the given collection, according to the natural ordering of its elements.
  - `max(Collection<? extends T> coll)`: Returns the maximum element.
- **Frequency/Disjoint**:
  - `frequency(Collection<?> c, Object o)`: Returns the number of elements in the specified collection equal to the specified object.
  - `disjoint(Collection<?> c1, Collection<?> c2)`: Returns true if the two specified collections have no elements in common.
- **Synchronized Wrappers**: (Discussed in Thread-Safe section)
  - `synchronizedList(List<T> list)`
  - `synchronizedSet(Set<T> s)`
  - `synchronizedMap(Map<K, V> m)`
- **Unmodifiable Wrappers**: (Discussed below)
  - `unmodifiableList(List<? extends T> list)`
  - `unmodifiableSet(Set<? extends T> s)`
  - `unmodifiableMap(Map<? extends K, ? extends V> m)`
- **Empty Collections**:
  - `emptyList()`, `emptySet()`, `emptyMap()`: Returns immutable empty collections (singleton instances).
- **Singleton Collections**:
  - `singletonList(T o)`, `singleton(T o)`, `singletonMap(K key, V value)`: Returns immutable collections containing only the specified element.

#### java.util.Arrays
Provides static methods for operating on arrays.
- **Sorting**:
  - `sort(int[] a)`, `sort(Object[] a)`, etc.: Overloaded methods to sort arrays of primitives and objects. Uses Dual-Pivot Quicksort for primitives and TimSort for objects.
- **Searching**:
  - `binarySearch(int[] a, int key)`, `binarySearch(Object[] a, Object key)`: Searches a sorted array.
- **Comparison**:
  - `equals(int[] a, int[] a2)`: Compares two arrays for equality.
  - `deepEquals(Object[] a1, Object[] a2)`: Compares two arrays for deep equality (for multi-dimensional arrays or arrays of arrays).
- **Copying**:
  - `copyOf(T[] original, int newLength)`: Copies the specified array, truncating or padding with default values (null for objects, 0 for numeric primitives, false for boolean) if necessary.
  - `copyOfRange(T[] original, int from, int to)`: Copies a range of the specified array.
- **Filling**:
  - `fill(int[] a, int val)`: Assigns the specified int value to each element of the specified array of ints.
- **Converting to List**:
   - `asList(T... a)`: Returns a fixed-size `List` backed by the specified array. Changes to the list write through to the array, and vice versa. It does not support structural modifications (add/remove) and will throw `UnsupportedOperationException`.
   ```java
    String[] array = {"apple", "banana"};
    List<String> listFromArray = Arrays.asList(array);
    // listFromArray.add("cherry"); // Throws UnsupportedOperationException
    array[0] = "orange";
    System.out.println(listFromArray); // Output: [orange, banana]
   ```
- **Stream API (Java 8+)**:
  - `stream(T[] array)`: Returns a sequential `Stream` with the specified array as its source.

### Deep vs. Shallow Copying
This concept applies to objects in general, but is particularly relevant when copying collections.
- **Shallow Copy**:
  - Creates a new collection, but the elements within the new collection are references to the same objects as in the original collection.
  - If the elements are mutable objects, changing an element in the copied collection will also affect the original collection (and vice versa) because they refer to the same underlying object.
  - **Examples**:
    - `ArrayList`'s copy constructor: new `ArrayList<>(originalList)`
    - `clone()` method of `ArrayList`/`HashMap`.
    - `System.arraycopy()`.
    - `Arrays.copyOf()`.
   ```java
    List<List<Integer>> original = new ArrayList<>();
    original.add(new ArrayList<>(List.of(1, 2)));
    original.add(new ArrayList<>(List.of(3, 4)));
    
    List<List<Integer>> shallowCopy = new ArrayList<>(original); // Shallow copy
    
    shallowCopy.get(0).add(5); // Modifying an inner list
    System.out.println("Original: " + original);     // Original: [[1, 2, 5], [3, 4]]
    System.out.println("Shallow Copy: " + shallowCopy); // Shallow Copy: [[1, 2, 5], [3, 4]]
    ```
- **Deep Copy**:
  - Creates a new collection and new copies of all the elements within it.
  - Changes to elements in the copied collection will not affect the original collection because they are entirely independent objects.
  - **How to achieve**:
    - Manually iterate and create new instances of each element.
    - Using serialization/deserialization (if objects are `Serializable`).
    - Using a copy constructor or factory method for each object if available.
    - Libraries like Apache Commons Lang's `SerializationUtils.clone()`.
   ```java
    List<List<Integer>> original = new ArrayList<>();
    original.add(new ArrayList<>(List.of(1, 2)));
    original.add(new ArrayList<>(List.of(3, 4)));
    
    List<List<Integer>> deepCopy = new ArrayList<>();
    for (List<Integer> innerList : original) {
        deepCopy.add(new ArrayList<>(innerList)); // Creating new inner list instances
    }
    
    deepCopy.get(0).add(5);
    System.out.println("Original: " + original);     // Original: [[1, 2], [3, 4]]
    System.out.println("Deep Copy: " + deepCopy);     // Deep Copy: [[1, 2, 5], [3, 4]]
    ```
  
### Making Unmodifiable Collections
The `Collections` utility class provides methods to create unmodifiable views of existing collections. These "wrapper" collections throw `UnsupportedOperationException` if any modification operation is attempted. The underlying collection can still be modified, and changes will be reflected in the unmodifiable view.
- `Collections.unmodifiableList(List<? extends T> list)`
- `Collections.unmodifiableSet(Set<? extends T> s)`
- `Collections.unmodifiableMap(Map<? extends K, ? extends V> m)`

**Purpose**: To provide read-only access to a collection, preventing accidental modification, or to share a collection without exposing its mutability.
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class UnmodifiableCollectionExample {
    public static void main(String[] args) {
        List<String> mutableList = new ArrayList<>();
        mutableList.add("Apple");
        mutableList.add("Banana");

        List<String> unmodifiableList = Collections.unmodifiableList(mutableList);
        System.out.println("Unmodifiable list: " + unmodifiableList); // [Apple, Banana]

        // Try to modify the unmodifiable view (will throw exception)
        try {
            unmodifiableList.add("Cherry");
        } catch (UnsupportedOperationException e) {
            System.out.println("Caught exception: " + e.getMessage());
        }

        // Modify the original list
        mutableList.add("Date");
        System.out.println("Original list after modification: " + mutableList); // [Apple, Banana, Date]
        System.out.println("Unmodifiable list (reflects original change): " + unmodifiableList); // [Apple, Banana, Date]
    }
}
```

**Note**: For truly immutable collections (where the content cannot change at all, even if the original is modified), use immutable collection factory methods introduced in Java 9+ (e.g., `List.of()`, `Set.of(),` `Map.of()`). These are discussed in "Immutable Collections Enhancements" section.

---

## 8. Thread-Safe & Concurrent Collections
In multi-threaded environments, care must be taken when multiple threads access and modify collections simultaneously. Unsynchronized collections can lead to `ConcurrentModificationException`, data corruption, or inconsistent states.
### `Vector`, `Hashtable` vs. Modern Alternatives
- `Vector` and `Hashtable`:
  - These are legacy classes (from Java 1.0) and are inherently thread-safe because all their public methods are `synchronized`.
  - **Pitfall**: This "all-or-nothing" synchronization provides thread-safety but at a significant performance cost, as only one thread can access the collection at any given time, even for read operations. This makes them highly inefficient in concurrent scenarios.
  - **Recommendation**: Avoid using `Vector` and `Hashtable` in new code unless you have specific compatibility requirements with older APIs.

- `Collections.synchronizedList()`
- **Purpose**: Provides a synchronized (thread-safe) wrapper for any unsynchronized `Collection`.
- **Mechanism**: It returns a new `Collection` (or `List`/`Set`/`Map`) that internally wraps the original collection. Every method call on the wrapper acquires an intrinsic lock (on the wrapper object itself) before delegating to the underlying collection.
- **Characteristics**:
  - Achieves thread-safety by synchronizing all access to the wrapped collection.
  - **Performance**: Better than `Vector`/`Hashtable` if the synchronization block is finer-grained (e.g., synchronizing on a specific object), but still introduces significant overhead for all operations, including reads.
  - **Important Caveat**: Iteration over a synchronized collection still requires manual external synchronization by the client code to prevent `ConcurrentModificationException`.
   ```java
    import java.util.ArrayList;
    import java.util.Collections;
    import java.util.List;
    import java.util.concurrent.CopyOnWriteArrayList;
    
    public class SynchronizedListExample {
        public static void main(String[] args) throws InterruptedException {
            List<String> list = Collections.synchronizedList(new ArrayList<>());
    
            Runnable addToList = () -> {
                for (int i = 0; i < 1000; i++) {
                    list.add("Item-" + i + "-" + Thread.currentThread().getName());
                }
            };
    
            Thread t1 = new Thread(addToList, "Thread-1");
            Thread t2 = new Thread(addToList, "Thread-2");
    
            t1.start();
            t2.start();
    
            t1.join();
            t2.join();
    
            System.out.println("Size of synchronized list: " + list.size()); // Will be 2000
    
            // Pitfall: Iterating without external synchronization can throw ConcurrentModificationException
            // for (String item : list) {
            //     System.out.println(item);
            // }
    
            // Correct way to iterate over Collections.synchronizedList:
            synchronized (list) { // Synchronize on the list itself
                for (String item : list) {
                    // System.out.println(item); // Process items
                }
                System.out.println("Iteration complete (synchronized)");
            }
        }
    }
    ```

### `ConcurrentHashMap`, `CopyOnWriteArrayList`
These are part of the `java.util.concurrent` package, designed for high-performance concurrent access.
#### `ConcurrentHashMap`
- Internal Implementation: Similar to `HashMap` (array of bins/nodes), but uses a different locking strategy. It partitions the map into segments (or directly uses fine-grained locking on bins in Java 8+), allowing multiple threads to modify different parts of the map concurrently. It uses `volatile` fields and `CAS` (Compare-And-Swap) operations for non-blocking reads and some updates.
- **Characteristics**:
  - **Highly concurrent**: Provides excellent performance in multi-threaded environments, especially with many readers and fewer writers.
  - **Thread-safe**: All operations are thread-safe without requiring external synchronization.
  - **Weakly Consistent Iterators**: Its iterators are "weakly consistent," meaning they reflect the state of the map at the time the iterator was created, plus any modifications that have occurred since, but they are not guaranteed to reflect all modifications. They will not throw `ConcurrentModificationException`.
  - Does not allow `null` keys or `null` values.
- **Use Cases**: The primary choice for thread-safe maps in modern Java applications. Caching, shared configuration, state management in concurrent systems.
    ```java
    import java.util.concurrent.ConcurrentHashMap;
    import java.util.concurrent.atomic.AtomicInteger;
    
    public class ConcurrentHashMapExample {
        public static void main(String[] args) throws InterruptedException {
            ConcurrentHashMap<String, AtomicInteger> wordCounts = new ConcurrentHashMap<>();
    
            Runnable task = () -> {
                for (int i = 0; i < 1000; i++) {
                    String word = "word" + (i % 10);
                    wordCounts.computeIfAbsent(word, k -> new AtomicInteger(0)).incrementAndGet();
                }
            };
    
            Thread t1 = new Thread(task);
            Thread t2 = new Thread(task);
    
            t1.start();
            t2.start();
    
            t1.join();
            t2.join();
    
            System.out.println("Word counts: " + wordCounts.size()); // Should be 10 unique words
            System.out.println("Total count for 'word0': " + wordCounts.get("word0").get()); // Should be 200
        }
    }
  ```

#### `CopyOnWriteArrayList`
- **Internal Implementation**: When a modification operation (add, set, remove) is performed on a CopyOnWriteArrayList, it creates a new copy of the underlying array, performs the modification on the new array, and then atomically replaces the old array with the new one. Read operations still work on the old array until the new one is published.
- **Characteristics**:
  - **Thread-safe**: All operations are thread-safe.
  - High **Read Concurrency**: Multiple readers can access the list concurrently without any locking, as they operate on immutable snapshots of the array.
  - **Expensive Writes**: Write operations are very expensive due to the array copying (O(N) for each write).
  - **Weakly Consistent Iterators**: Iterators operate on a snapshot of the list at the time the iterator was created and will not reflect subsequent modifications. They will not throw `ConcurrentModificationException`.
- **Use Cases**: Scenarios where reads vastly outnumber writes (e.g., maintaining a list of listeners, broadcast lists, configuration lists that change infrequently). Avoid if writes are frequent.
    ```java
    import java.util.Iterator;
    import java.util.List;
    import java.util.concurrent.CopyOnWriteArrayList;
    
    public class CopyOnWriteArrayListExample {
        public static void main(String[] args) {
            List<String> list = new CopyOnWriteArrayList<>();
            list.add("Apple");
            list.add("Banana");
    
            // Thread 1: Iterates over the list
            Thread readerThread = new Thread(() -> {
                Iterator<String> it = list.iterator();
                while (it.hasNext()) {
                    String item = it.next();
                    System.out.println("Reader: " + item);
                    try {
                        Thread.sleep(50); // Simulate some processing
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                    }
                }
            });
    
            // Thread 2: Modifies the list
            Thread writerThread = new Thread(() -> {
                try {
                    Thread.sleep(10); // Give reader a head start
                    list.add("Cherry"); // This creates a new copy of the array
                    System.out.println("Writer: Added Cherry. List: " + list);
                    list.remove("Apple");
                    System.out.println("Writer: Removed Apple. List: " + list);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
    
            readerThread.start();
            writerThread.start();
    
            try {
                readerThread.join();
                writerThread.join();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            System.out.println("Final List: " + list);
            /* Possible Output:
            Reader: Apple
            Writer: Added Cherry. List: [Apple, Banana, Cherry]
            Reader: Banana
            Writer: Removed Apple. List: [Banana, Cherry]
            Final List: [Banana, Cherry]
            (Note: Reader thread does not see "Cherry" or removal of "Apple" because it operates on an old snapshot)
            */
        }
    }
  ```

### When to use what, thread-safety vs. performance
| Collection Type                             | Thread-Safety       | Performance                | Best Use Cases                                                   | Pitfalls                                                |
|---------------------------------------------|---------------------|----------------------------|------------------------------------------------------------------|---------------------------------------------------------|
| `ArrayList,` `HashMap`, `HashSet`           | No                  | High                       | Single-threaded or externally synchronized operations            | ConcurrentModificationException, data corruption        |
| `Vector`, `Hashtable`                       | Yes                 | Low                        | Legacy code, avoid in new designs                                | Global lock, poor scalability                           |
| `Collections.synchronizedX()`               | Yes                 | Moderate                   | Simple synchronization needs, manual external sync for iteration | Still global lock, external sync for iteration required |
| `ConcurrentHashMap`                         | Yes (Fine-grained)  | Very High                  | High-concurrency maps (many readers/writers)                     | No null keys/values, weakly consistent iterators        |
| `CopyOnWriteArrayList`                      | Yes (Copy-on-write) | High (reads), Low (writes) | Read-heavy lists, event listeners, broadcast lists               | High memory overhead on writes, stale iterators         |
| `LinkedBlockingQueue`, `ArrayBlockingQueue` | Yes (Blocking)      | High                       | Producer-consumer patterns, task queues                          | Can block threads                                       |

#### General Rule:
- For **single-threaded** environments, use the unsynchronized implementations (`ArrayList`, `HashMap`, `HashSet`, `ArrayDeque`). They offer the best performance.
- For **multi-threaded** environments:
  - If you need a map with high concurrency, use `ConcurrentHashMap`.
  - If you need a list with very frequent reads and infrequent writes, use `CopyOnWriteArrayList`.
  - If you need simple, all-encompassing thread-safety for an existing collection and can manage external synchronization for iteration, use `Collections.synchronizedX()`.
  - For producer-consumer patterns or bounded queues, use `java.util.concurrent.BlockingQueue` implementations.
  - Avoid `Vector` and `Hashtable` unless strictly necessary for compatibility.

---

## 9. Iterators and Fail-Fast Behavior
Iterators provide a standard way to traverse elements in a collection.
### Iterator, ListIterator, and Enumeration
- `Iterator<E>`:
  - **Purpose**: The universal interface for iterating over collections. It allows sequential access to elements.
  - **Methods**:
    - `boolean hasNext()`: Returns `true` if the iteration has more elements.
    - `E next()`: Returns the next element in the iteration.
    - `void remove()`: Removes the last element returned by `next()` from the underlying collection. This is the only safe way to modify a collection during iteration using a fail-fast iterator.
  - **Usage**: Can be used with `Collection`, `Set`, `List`, `Queue`.
  - Does not support adding elements or iterating backward.
- `ListIterator<E>`:
  - **Purpose**: Extends `Iterator` and is specifically designed for List implementations. It offers more powerful iteration capabilities.
  - **Methods (in addition to** `Iterator`**'s)**:
    - `boolean hasPrevious()`: Returns `true` if the list has more elements when traversing in reverse.
    - `E previous()`: Returns the previous element in the list.
    - `int nextIndex()`: Returns the index of the element that would be returned by a subsequent call to `next()`.
    - `int previousIndex()`: Returns the index of the element that would be returned by a subsequent call to `previous()`.
    - `void set(E e)`: Replaces the last element returned by `next()` or `previous()` with the specified element.
    - `void add(E e)`: Inserts the specified element into the list immediately before the element that would be returned by `next()`.
  - **Usage**: Only for `List` implementations.
  - Supports bidirectional traversal and modification (add, set, remove) of the list during iteration.
- `Enumeration<E>`:
  - **Purpose**: A legacy interface (from Java 1.0) for iterating over elements of a collection.
  - **Methods**:
    - `boolean hasMoreElements()`: Similar to `hasNext()`.
    - `E nextElement()`: Similar to `next()`.
  - **Usage**: Primarily used with legacy collections like `Vector` and `Hashtable`.
  - Does not support `remove()` or adding elements. It is equivalent to a read-only, forward-only iterator.
  - **Recommendation**: Prefer Iterator in modern code.

### Code Example: Iterators
```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;

public class IteratorExamples {
    public static void main(String[] args) {
        List<String> fruits = new ArrayList<>(List.of("Apple", "Banana", "Cherry", "Date"));

        // Using Iterator (forward, remove)
        Iterator<String> iterator = fruits.iterator();
        while (iterator.hasNext()) {
            String fruit = iterator.next();
            System.out.println("Iterator: " + fruit);
            if (fruit.equals("Banana")) {
                iterator.remove(); // Safe removal
            }
        }
        System.out.println("List after Iterator removal: " + fruits); // [Apple, Cherry, Date]

        System.out.println("\n---");

        // Using ListIterator (bidirectional, add, set)
        ListIterator<String> listIterator = fruits.listIterator();
        listIterator.next(); // Moves past "Apple"
        listIterator.add("Grape"); // Adds "Grape" before "Cherry"
        listIterator.next(); // Moves past "Cherry"
        listIterator.set("Pineapple"); // Replaces "Cherry" with "Pineapple"

        System.out.println("List after ListIterator modifications: " + fruits); // [Apple, Grape, Pineapple, Date]

        // Iterate backwards
        System.out.println("Iterating backwards with ListIterator:");
        while (listIterator.hasPrevious()) {
            System.out.println("Previous: " + listIterator.previous());
        }
        // Output:
        // Previous: Apple
        // Previous: Grape
        // Previous: Pineapple
        // Previous: Date (order might be tricky depending on last cursor position)
    }
}
```

### Fail-Fast vs. Fail-Safe
This describes how iterators behave when the underlying collection is modified concurrently.
- **Fail-Fast Iterators**:
  - **Behavior**: Throw a `ConcurrentModificationException` immediately (or as soon as possible) if the collection is structurally modified during iteration by any means other than the iterator's own remove() or add() methods.
  - **Purpose**: To quickly detect and report potential bugs related to concurrent modification. They are not designed for true concurrency but for single-threaded or externally synchronized access where unintended modifications should be caught.
  - Examples: Iterators returned by `ArrayList`, `HashMap`, `HashSet`, `LinkedList`, `TreeMap`, `TreeSet`, `Vector`, `Hashtable`.
  - **Implementation Detail**: They maintain a modCount (modification count) and check it at the beginning of each operation (next(), hasNext(), remove()). If modCount changes unexpectedly, an exception is thrown.
- **Fail-Safe Iterators**:
  - **Behavior**: Do not throw `ConcurrentModificationException` even if the underlying collection is structurally modified during iteration. They operate on a snapshot or a copy of the collection taken at the time the iterator was created.
  - **Purpose**: Designed for concurrent environments where modifications are expected during iteration. They trade off consistency for thread-safety during iteration.
  - **Examples**: Iterators returned by `CopyOnWriteArrayList`, `ConcurrentHashMap`.

## Comparison Table: Fail-Fast vs. Fail-Safe
| **Feature**    | **Fail-Fast**                                 | **Fail-Safe**                                         |
|----------------|-----------------------------------------------|-------------------------------------------------------|
| Exception      | Throws `ConcurrentModificationException`      | Does not throw `ConcurrentModificationException`      |
| Consistency    | Reflects current state, but fragile           | May operate on a stale snapshot                       |
| Thread-Safety  | Not thread-safe for concurrent modification   | Thread-safe during iteration                          |
| Cost of Write  | Low                                           | High (e.g., `CopyOnWriteArrayList` copies array)      |
| Examples       | `ArrayList`, `HashMap`, `HashSet` iterators   | `CopyOnWriteArrayList`, `ConcurrentHashMap` iterators |

### Removing Elements While Iterating
As discussed under "Common Pitfalls" for Lists:
- `Incorrect (for fail-fast collections)`:
   ```java
   List<String> list = new ArrayList<>(List.of("A", "B", "C"));
   // for (String s : list) {
   //     if (s.equals("B")) {
   //         list.remove(s); // ConcurrentModificationException
   //     }
   // }
   ```

- **Correct using** `Iterator.remove()`:
    ```java
    List<String> list = new ArrayList<>(List.of("A", "B", "C"));
    Iterator<String> it = list.iterator();
    while (it.hasNext()) {
       String s = it.next();
       if (s.equals("B")) {
          it.remove(); // Safely removes "B"
       }
    }
    System.out.println(list); // [A, C]
    ```

- **Correct using** `removeIf()` **(Java 8+)**:
   ```java
   List<String> list = new ArrayList<>(List.of("A", "B", "C"));
   list.removeIf(s -> s.equals("B")); // Safe and concise
   System.out.println(list); // [A, C]  
   ```


- **Correct for** `for` **loop (iterating backward)**:
    ```java
    List<String> list = new ArrayList<>(List.of("A", "B", "C"));
    for (int i = list.size() - 1; i >= 0; i--) {
        if (list.get(i).equals("B")) {
            list.remove(i); // Safe, as it doesn't affect indices not yet visited
        }
    }
    System.out.println(list); // [A, C]
    ```

---

## 10. Stream API vs. Collections
The Java 8 Stream API provides a functional approach to processing collections of objects. While collections are about data structures that store and manage data, streams are about data processing.

### Functional Operations on Collections
Streams enable you to perform operations like filtering, mapping, and reducing on collections in a declarative style, often with the benefit of potential parallelism.
- **Collections**: Primarily concerned with the storage and management of data. They are in-memory data structures that can be added to, removed from, and accessed directly.
- **Streams**: A sequence of elements supporting sequential and parallel aggregate operations. They do not store data themselves; they process data from a source (like a collection, array, I/O channel).
  - **Laziness**: Stream operations are generally lazy, meaning they are not executed until a terminal operation is invoked.
  - **Immutability**: Stream operations produce new streams or results, without modifying the source collection (non-interfering).
  - **Pipelines**: Operations can be chained together to form a pipeline.

#### Diagram: Stream Pipeline
```
Source -> Intermediate Operation 1 -> Intermediate Operation 2 -> ... -> Terminal Operation -> Result
 (Collection, Array, I/O)    (filter, map, sorted, distinct)        (forEach, collect, reduce, count)
```

### Mapping, Filtering, Reduction
- **Mapping (*`*map()`**)**: Transforms each element of the stream into another element.
    ```java
    List<String> names = List.of("Alice", "Bob", "Charlie");
    List<Integer> nameLengths = names.stream()
          .map(String::length) // Map String to Integer (length)
          .collect(Collectors.toList());
    System.out.println(nameLengths); // [5, 3, 7]
    ```

- **Filtering (**`filter()`**)**: Selects elements from the stream that satisfy a given predicate.
   ```java
   List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10); 
   List<Integer> evenNumbers = numbers.stream()
       .filter(n -> n % 2 == 0) // Filter for even numbers
       .collect(Collectors.toList());
   System.out.println(evenNumbers); // [2, 4, 6, 8, 10]
   ```

- **Reduction (**`reduce()`, `sum()`, `count()`, `collect()`**)**: Combines elements of a stream into a single result. `collect()` is a versatile reduction operation that accumulates elements into a `Collection`.
  ```java   
    List<Double> prices = List.of(10.5, 20.0, 5.25);
    double totalPrice = prices.stream()
                              .mapToDouble(Double::doubleValue) // Use double stream for sum
                              .sum(); // Reduction to a single double
    System.out.println("Total price: " + totalPrice); // 35.75
    
    long count = names.stream().count(); // Reduction to a count
    System.out.println("Number of names: " + count); // 3
    
    // Collect into a Map
    Map<String, Integer> nameToLengthMap = names.stream()
                                                 .collect(Collectors.toMap(
                                                     name -> name,
                                                     String::length
                                                 ));
    System.out.println(nameToLengthMap); // {Alice=5, Bob=3, Charlie=7}
  ```

### Benefits over Iterative Approach
- **Readability and Conciseness**: Expresses "what" to do rather than "how" to do it. Reduces boilerplate code.
- **Declarative Style**: Focus on the problem domain, not the loop mechanics.
- **Parallelism**: Streams can easily be converted to parallel streams (`.parallelStream()`) to leverage multiple CPU cores for large datasets, often with minimal code changes. This is a significant performance advantage for compute-intensive tasks.
- **Immutability**: Stream operations are non-mutating, promoting functional programming principles and reducing side effects.
- **Method Chaining**: Operations can be chained, creating expressive and fluent code.

#### Example: Iterative vs. Stream
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamVsIterative {
    static class Book {
        String title;
        String author;
        int pages;

        public Book(String title, String author, int pages) {
            this.title = title;
            this.author = author;
            this.pages = pages;
        }

        public String getTitle() { return title; }
        public String getAuthor() { return author; }
        public int getPages() { return pages; }

        @Override
        public String toString() {
            return "Book{" + "title='" + title + '\'' + ", author='" + author + '\'' + ", pages=" + pages + '}';
        }
    }

    public static void main(String[] args) {
        List<Book> books = Arrays.asList(
                new Book("The Lord of the Rings", "J.R.R. Tolkien", 1178),
                new Book("Pride and Prejudice", "Jane Austen", 279),
                new Book("1984", "George Orwell", 328),
                new Book("To Kill a Mockingbird", "Harper Lee", 281),
                new Book("The Hobbit", "J.R.R. Tolkien", 310)
        );

        // Iterative approach: Find titles of books with more than 300 pages by "J.R.R. Tolkien"
        List<String> iterativeTitles = new ArrayList<>();
        for (Book book : books) {
            if (book.getAuthor().equals("J.R.R. Tolkien") && book.getPages() > 300) {
                iterativeTitles.add(book.getTitle());
            }
        }
        System.out.println("Iterative Titles: " + iterativeTitles);

        // Stream API approach:
        List<String> streamTitles = books.stream()
                                         .filter(book -> book.getAuthor().equals("J.R.R. Tolkien"))
                                         .filter(book -> book.getPages() > 300)
                                         .map(Book::getTitle)
                                         .collect(Collectors.toList());
        System.out.println("Stream API Titles: " + streamTitles);

        // Parallel Stream (for very large collections/compute-intensive tasks)
        List<String> parallelStreamTitles = books.parallelStream() // Use parallelStream() for potential parallelism
                                                .filter(book -> book.getAuthor().equals("J.R.R. Tolkien"))
                                                .filter(book -> book.getPages() > 300)
                                                .map(Book::getTitle)
                                                .collect(Collectors.toList());
        System.out.println("Parallel Stream API Titles: " + parallelStreamTitles);
    }
}
```

## 11. Performance Analysis
Understanding the Big-O notation for various collection operations is crucial for choosing the right data structure for your application.

### Big-O for Insert, Delete, Lookup Across Structures
| Data Structure         | Get (Lookup) | Insert (Add)               | Delete (Remove)            | Notes                                                        |
|------------------------|--------------|----------------------------|----------------------------|--------------------------------------------------------------|
| `ArrayList`            | O(1)         | O(1) amortized             | O(N)                       | `add(index, E)` and `remove(index)` are O(N)                 |
| `LinkedList`           | O(N)         | O(1) (ends), O(N) (middle) | O(1) (ends), O(N) (middle) | `get(index)` requires traversal                              |
| `HashSet`              | O(1) average | O(1) average               | O(1) average               | Worst case O(N) if bad hash distribution or many collisions  |
| `LinkedHashSet`        | O(1) average | O(1) average               | O(1) average               | Slightly slower than `HashSet` due to linked list overhead   |
| `TreeSet`              | O(log N)     | O(log N)                   | O(log N)                   | Based on Red-Black Tree                                      |
| `HashMap`              | O(1) average | O(1) average               | O(1) average               | Worst case O(N) (pre-Java 8), O(log N) (Java 8+ treeified)   |
| `LinkedHashMap`        | O(1) average | O(1) average               | O(1) average               | Slightly slower than `HashMap` due to linked list overhead   |
| `TreeMap`              | O(log N)     | O(log N)                   | O(log N)                   | Based on Red-Black Tree                                      |
| `ArrayDeque`           | N/A          | O(1) amortized             | O(1)                       | `addFirst/Last`, `removeFirst/Last`                          |
| `PriorityQueue`        | O(1) (peek)  | O(log N) (offer)           | O(log N) (poll)            | `remove(Object)` is O(N) due to search                       |
| `ConcurrentHashMap`    | O(1) average | O(1) average               | O(1) average               | High concurrency, no null keys/values                        |
| `CopyOnWriteArrayList` | O(1) (get)   | O(N)                       | O(N)                       | Excellent for reads, expensive for writes (copies array)     |

### Memory Footprint
- `ArrayList`: Relatively memory-efficient due to contiguous memory allocation. Overhead is mainly the array itself and object references. Resizing can temporarily double memory usage.
- `LinkedList`: Higher memory footprint per element due to the overhead of each `Node` object (stores element, `next` reference, `prev` reference). Dispersed memory can also lead to poorer cache performance.
- `HashSet`/`HashMap`: Memory usage depends on `load factor` and `initial capacity`. Each `Node`/`Entry` object has overhead for key, value, hash, and `next` pointer. Treeified bins in Java 8+ also add overhead for `TreeNode` objects.
- `TreeSet`/`TreeMap`: Each node in the Red-Black tree has more overhead than a HashMap node (stores key, value, color, parent, left child, right child references).

### Tuning Parameters: Initial Capacity, Load Factor
These parameters are crucial for optimizing performance of hash-based collections (`HashMap`, `HashSet`, `LinkedHashMap`, `LinkedHashSet`).
- **Initial Capacity**:
  - **Definition**: The number of buckets (array slots) in the internal hash table when the collection is first created.
  - **Impact**:
    - **Too low**: Causes frequent rehashing (resizing and re-distributing all elements), which is an expensive O(N) operation.
    - **Too high**: Wastes memory.
  - **Best Practice**: Provide a reasonable initial capacity if you have an estimate of the number of elements the collection will hold. `HashMap`'s default initial capacity is 16. `ArrayList`'s is 10.
  - **Formula for** `HashMap` **(to avoid early resize)**: If you expect `N` elements, `initialCapacity` should be `(int) (N / loadFactor) + 1`.
- **Load Factor**:
  - **Definition**: A threshold value that determines when the hash table should be resized. It's `number of entries / current capacity`.
  - **Default Value**: `0.75f` (75%).
  - **Impact**:
    - **High Load Factor (e.g., 0.9)**: Reduces space overhead but increases the likelihood of collisions, leading to longer chain/tree traversals and degrading average O(1) performance towards O(N) or O(log N).
    - **Low Load Factor (e.g., 0.5)**: Reduces collision likelihood and improves time performance but increases space overhead due to more empty buckets.
  - **Best Practice**: The default of `0.75f` is a good balance for most use cases. Only change it if profiling shows performance bottlenecks related to hash collisions or memory usage, and you have specific knowledge of your data distribution.

#### Example: Tuning `HashMap`
```java
import java.util.HashMap;

public class HashMapTuning {
    public static void main(String[] args) {
        // Default HashMap: initial capacity 16, load factor 0.75
        HashMap<String, String> defaultMap = new HashMap<>();

        // HashMap with custom initial capacity
        // If we expect 1000 entries, and default load factor 0.75
        // (1000 / 0.75) + 1 = 1333 + 1 = 1334. Capacity must be a power of 2 >= 1334.
        // The nearest power of 2 is 2048. So passing 1334 will lead to an internal capacity of 2048.
        HashMap<String, String> optimizedMap = new HashMap<>(1334);

        // HashMap with custom initial capacity and load factor
        HashMap<String, String> customMap = new HashMap<>(128, 0.9f); // Higher load factor, more collisions, less memory
    }
}
```

---

## 12. Map and Set Internals
Deep dive into the underlying mechanisms of hash-based collections (`HashMap`, `HashSet`, `LinkedHashMap`, `LinkedHashSet`).

### Collision Handling
As described in the `HashMap` section (5. Maps), Java's hash-based collections use **chaining** to handle collisions. <br>
When `key.hashCode()` maps to an index `i` that already has elements, the new entry is appended to a linked list (or in Java 8+, potentially a tree) at that array index (bucket).

Retrieval then involves:
1. Calculating the hash of the key.
2. Finding the bucket using the hash.
3. Traversing the linked list/tree in that bucket, comparing keys using `equals()` until a match is found.

### Treeified Bins in HashMap (Java 8+)
- **Motivation**: In earlier Java versions, a poor hashCode() implementation or a malicious attack could lead to many keys hashing to the same bucket. This would degrade HashMap operations from O(1) to O(N) as it would effectively become a linked list traversal.
- **Solution (Java 8+)**: To mitigate this, HashMap (and LinkedHashMap) now transforms a linked list into a balanced Red-Black tree (TreeNode) when the number of elements in a single bucket reaches a certain TREEIFY_THRESHOLD (default 8).
- **Benefits**: This changes the worst-case time complexity for operations within that bucket from O(N) to O(log N).
- **Conditions for Treeification**:
  - The number of nodes in a bin must exceed `TREEIFY_THRESHOLD` (8).
  - The total capacity of the `HashMap` must be at least `MIN_TREEIFY_CAPACITY` (64). If the capacity is below 64, the `HashMap` will prefer to resize (double its capacity) instead of treeifying, as resizing generally distributes elements better.
- **Untreeification**: When elements are removed from a treeified bin and its size drops below `UNTREEIFY_THRESHOLD` (default 6), it reverts back to a linked list.

### Load Factor and Resize Strategy
- **Resize Threshold**: When the number of entries in the `HashMap` exceeds `capacity * loadFactor`, the `HashMap` is resized.
- **Resize Operation**:
  1. A new array (typically twice the size of the old array) is created.
  2. All existing entries from the old array are **rehashed** and redistributed into the new, larger array. This is an expensive O(N) operation.
- **Impact**: Frequent resizing degrades performance. A well-chosen initial capacity can minimize resizing.
- **Default Values**:
  - Default `initialCapacity`: 16
  - Default `loadFactor`: 0.75f

#### Insertion Order vs. Sort Order vs. Random Access
This refers to how elements are arranged and accessed in different collections.
- **Insertion Order**: Elements are stored and retrieved in the sequence they were added.
  - **Collections**: `LinkedHashSet`, `LinkedHashMap`, `ArrayList` (implicitly maintains insertion order by appending).
  - **Use Cases**: Maintaining a history, processing items in the order they were received.
- **Sort Order**: Elements are stored and retrieved according to a defined natural ordering (`Comparable`) or a custom order (`Comparator`).
  - **Collections**: `TreeSet`, `TreeMap`, `PriorityQueue`.
  - **Use Cases**: Sorted lists/maps, range queries, priority-based processing.
- **Random Access**: Elements can be accessed directly by an index (e.g., `get(i)`) in constant time, regardless of the size of the collection.
  - **Collections**: `ArrayList`, arrays.
  - **Use Cases**: Frequent lookups by index, scenarios where element position is meaningful.
- **Random/Unordered**: Elements have no guaranteed order. The order might change unpredictably (e.g., after a resize).
  - **Collections**: `HashSet`, `HashMap`.
  - **Use Cases**: When only uniqueness or key-value association matters, and order is irrelevant. Optimal for fast lookups.

---
  
## 13. Advanced Concepts
### NavigableMap/Set
- `NavigableMap<K, V>`:
  - Extends `SortedMap`. Provides methods for navigating the map based on its keys.
  - Methods: `lowerKey()`, `floorKey()`, `ceilingKey()`, `higherKey()`, `firstEntry()`, `lastEntry()`, `pollFirstEntry()`, `pollLastEntry()`, `descendingMap()`, and methods for creating `subMap`, `headMap`, `tailMap` with inclusive/exclusive bounds.
  - **Implementation**: `TreeMap`.
- `NavigableSet<E>`:
  - Extends `SortedSet`. Provides similar navigation methods for sets.
  - Methods: `lower()`, `floor()`, `ceiling()`, `higher()`, `pollFirst()`, `pollLast()`, `descendingSet()`, and methods for creating `subSet`, `headSet`, `tailSet` with inclusive/exclusive bounds.
  - **Implementation**: `TreeSet`.

These interfaces are powerful for working with ordered data, allowing efficient retrieval of elements based on their relative order.

### SubMap, HeadMap, TailMap
These methods, available in `NavigableMap` and `NavigableSet` (and their superinterfaces `SortedMap`/`SortedSet`), provide views of portions of the original map/set. Changes to the view are reflected in the original map/set, and vice versa.
- `subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive)`: Returns a view of the portion of this map/set whose keys range from fromKey to toKey. The inclusive booleans determine whether the endpoints are included.
- `headMap(K toKey, boolean inclusive)`: Returns a view of the portion of this map/set whose keys are less than (or equal to, if `inclusive` is true) toKey.
- `tailMap(K fromKey, boolean inclusive)`: Returns a view of the portion of this map/set whose keys are greater than (or equal to, if `inclusive` is true) `fromKey`.

**Real-world Example**: Finding all customers whose IDs are within a specific range from a large `TreeMap` of customer records.
```java
import java.util.NavigableMap;
import java.util.TreeMap;

public class NavigableMapExample {
    public static void main(String[] args) {
        NavigableMap<Integer, String> studentScores = new TreeMap<>();
        studentScores.put(101, "Alice");
        studentScores.put(105, "Bob");
        studentScores.put(110, "Charlie");
        studentScores.put(115, "David");
        studentScores.put(120, "Eve");

        System.out.println("Original Map: " + studentScores);

        // Get students with scores between 105 (inclusive) and 115 (exclusive)
        NavigableMap<Integer, String> rangeView = studentScores.subMap(105, true, 115, false);
        System.out.println("Students with scores [105, 115): " + rangeView); // {105=Bob, 110=Charlie}

        // Add a new student within the range via the view
        rangeView.put(108, "Frank");
        System.out.println("Original Map after view modification: " + studentScores); // {101=Alice, 105=Bob, 108=Frank, 110=Charlie, 115=David, 120=Eve}
        System.out.println("View after view modification: " + rangeView); // {105=Bob, 108=Frank, 110=Charlie}
    }
}
```

### Spliterator and Parallel Streams
- `Spliterator<T>`:
  - **Purpose**: A new kind of iterator introduced in Java 8, designed to support parallel iteration over data sources. It allows for parallel decomposition of the data.
  - **Methods**:
    - `tryAdvance(Consumer<? super T> action)`: Processes one element if available.
    - `trySplit()`: Tries to partition its elements into two. If successful, returns a new Spliterator covering about half the elements, and the original Spliterator covers the other half. This is key for parallel processing.
    - `estimateSize()`, `getExactSizeIfKnown()`: Provide estimates of remaining elements.
    - `characteristics()`: Returns a set of characteristics (e.g., `SIZED`, `SORTED`, `DISTINCT`, `IMMUTABLE`, `NONNULL`, `CONCURRENT`).
  - **How it enables parallelism**: The `Stream` API leverages `Spliterator` to break down a collection into multiple smaller chunks that can be processed concurrently by different threads in a parallel stream.
  - All `Collection` implementations provide a `spliterator()` method.
- **Parallel Streams**:
  - Achieved by calling `.parallelStream()` on a `Collection` (or `.parallel()` on a sequential stream).
  - When a `Stream` is parallel, the `Spliterator` splits the data, and the stream operations (filters, maps, reductions) are executed concurrently on different parts of the data.
  - **Benefits**: Can significantly speed up computations on large datasets, especially for CPU-bound tasks.
  - **Considerations**:
    - Not always faster: Overhead of parallelism (splitting, merging results) can negate benefits for small datasets or I/O-bound tasks.
    - Side effects: Avoid stateful lambdas or operations with side effects if not properly synchronized, as this can lead to unpredictable results in parallel streams.
    - Order: Operations like `forEachOrdered()` maintain order even in parallel streams, but might incur performance penalties.

### Bounded Queues (`BlockingQueue`)
- `BlockingQueue<E>`:
  - **Interface**: Extends `Queue`. Part of `java.util.concurrent`.
  - **Purpose**: Supports operations that block (wait) when the queue is empty or full.
  - **Key Operations**:
    - `put(E e)`: Inserts element at the tail, waiting if the queue is full.
    - `take()`: Retrieves and removes head, waiting if the queue is empty.
    - `offer(E e, long timeout, TimeUnit unit)`: Inserts with timeout.
    - `poll(long timeout, TimeUnit unit)`: Retrieves with timeout.
  - **Characteristics**:
    - **Thread-safe**: All methods are thread-safe and handle synchronization internally.
    - **Producer-Consumer pattern**: Essential for implementing robust producer-consumer designs where producers put tasks into a queue and consumers take them out.
    - **Bounded or Unbounded**: Can be created with a fixed capacity (bounded) or without (unbounded, defaults to `Integer.MAX_VALUE`).
  - **Implementations**:
    - `ArrayBlockingQueue`: Bounded, backed by an array. Fair (FIFO) by default.
    - `LinkedBlockingQueue`: Optionally bounded, backed by a linked list. Can be more flexible than ArrayBlockingQueue.
    - `PriorityBlockingQueue`: Unbounded, ordered by priority, blocking on empty.
    - `DelayQueue`: Stores elements that can only be retrieved after a given delay.
    - `SynchronousQueue`: A queue with zero capacity. Each insert must wait for a corresponding remove, and vice versa.

#### Code Example: Producer-Consumer with `BlockingQueue`
```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.TimeUnit;

public class BlockingQueueExample {
    public static void main(String[] args) throws InterruptedException {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5); // Bounded queue with capacity 5

        // Producer
        Runnable producer = () -> {
            try {
                for (int i = 0; i < 10; i++) {
                    System.out.println("Producer: Putting " + i);
                    queue.put(i); // Blocks if queue is full
                    Thread.sleep(50);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        // Consumer
        Runnable consumer = () -> {
            try {
                for (int i = 0; i < 10; i++) {
                    Integer item = queue.take(); // Blocks if queue is empty
                    System.out.println("Consumer: Took " + item);
                    Thread.sleep(150);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        Thread producerThread = new Thread(producer);
        Thread consumerThread = new Thread(consumer);

        producerThread.start();
        consumerThread.start();

        producerThread.join();
        consumerThread.join();
        System.out.println("Producer-Consumer example finished.");
    }
}
```

## 14. Custom Collection Implementations
While Java provides a rich set of built-in collections, there might be niche scenarios where you need a specialized collection with unique behavior or performance characteristics.
#### Writing a Custom List, Set, or Map
To implement a custom collection, you can either implement the core interfaces directly (`List`, `Set`, `Map`, `Queue`) or, more commonly, extend the abstract skeletal implementations provided by the JCF.
#### Extending Abstract Classes like `AbstractList`
The Java Collections Framework provides abstract skeletal implementations that handle much of the boilerplate code for you, allowing you to focus on the core storage mechanism and fundamental operations.
- `AbstractCollection`: Minimal implementation for `Collection`. You must implement iterator() and size().
- `AbstractList`: For list backed by a random-access data store (like an array). You must implement `get(index)` and `size()`. `set()`, `add(index, E)`, `remove(index)` typically need to be overridden for modifiable lists.
- `AbstractSequentialList`: For list backed by a sequential-access data store (like a linked list). You must implement `listIterator()` and `size()`.
- `AbstractSet`: For set. Similar to `AbstractCollection`.
- `AbstractQueue`: For queue. You must implement `offer()`, `peek()`, `poll()`, `size()`, and an `iterator()` that supports `remove()`.
- `AbstractMap`: For map. You must implement `entrySet()`. If the map is modifiable, you must also implement `put()`.

#### Example: Basic Custom `ArrayList`-like List (extending `AbstractList`)
```java
import java.util.AbstractList;
import java.util.Arrays;
import java.util.Collection;
import java.util.ConcurrentModificationException;
import java.util.Iterator;
import java.util.NoSuchElementException;

public class MySimpleArrayList<E> extends AbstractList<E> {
    private Object[] elementData;
    private int size;
    private static final int DEFAULT_CAPACITY = 10;

    public MySimpleArrayList() {
        this.elementData = new Object[DEFAULT_CAPACITY];
    }

    public MySimpleArrayList(int initialCapacity) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
        this.elementData = new Object[initialCapacity];
    }

    public MySimpleArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        size = elementData.length;
        // c.toArray might not return Object[] (e.g., if c is List<String>, it's String[])
        if (elementData.getClass() != Object[].class) {
            elementData = Arrays.copyOf(elementData, size, Object[].class);
        }
    }

    private void ensureCapacityInternal(int minCapacity) {
        if (minCapacity - elementData.length > 0) {
            grow(minCapacity);
        }
    }

    private void grow(int minCapacity) {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1); // Grow by 1.5 times
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - Integer.MAX_VALUE + 8 > 0) // Handle overflow
            newCapacity = Integer.MAX_VALUE - 8;
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    @Override
    public E get(int index) {
        rangeCheck(index);
        return (E) elementData[index];
    }

    @Override
    public int size() {
        return size;
    }

    @Override
    public boolean add(E e) {
        modCount++; // Increment modCount for fail-fast iterators
        ensureCapacityInternal(size + 1);
        elementData[size++] = e;
        return true;
    }

    @Override
    public E remove(int index) {
        modCount++;
        rangeCheck(index);
        E oldValue = (E) elementData[index];
        int numMoved = size - index - 1;
        if (numMoved > 0) {
            System.arraycopy(elementData, index + 1, elementData, index, numMoved);
        }
        elementData[--size] = null; // Clear to let GC do its work
        return oldValue;
    }

    private void rangeCheck(int index) {
        if (index >= size || index < 0) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
    }

    // Inherits iterator(), listIterator() implementations from AbstractList
    // but they rely on get(), size(), and remove() for structural modification,
    // which we've implemented here. AbstractList also takes care of modCount
    // checks in its default iterators.

    public static void main(String[] args) {
        MySimpleArrayList<String> myList = new MySimpleArrayList<>();
        myList.add("Hello");
        myList.add("World");
        myList.add("Java");
        System.out.println("MyList: " + myList); // [Hello, World, Java]

        System.out.println("Element at index 1: " + myList.get(1)); // World

        myList.remove(0);
        System.out.println("MyList after removal: " + myList); // [World, Java]

        // Test fail-fast behavior (using AbstractList's iterator which uses modCount)
        try {
            for (String s : myList) {
                if (s.equals("Java")) {
                    myList.add("Collections"); // This will cause ConcurrentModificationException
                }
            }
        } catch (ConcurrentModificationException e) {
            System.out.println("Caught ConcurrentModificationException as expected!");
        }
    }
}
```

### Importance of Contracts: `equals`/`hashCode`/`compareTo`
When implementing custom collections or defining custom objects to be used in standard collections, adhering to the contracts of `equals()`, `hashCode()`, and `compareTo()` is paramount.
- `equals()` and `hashCode()`:
  - **Contract**: As detailed in Section 4 ("Importance of `hashCode()` and `equals()`"), if `a.equals(b)` then `a.hashCode()` must be equal to `b.hashCode()`.
  - **Impact**: Incorrect implementation leads to `HashSet`/`HashMap` misbehaving (duplicates in Sets, inability to find elements in Maps).
- `compareTo()` **(for** `Comparable`) **and** `compare()` **(for** `Comparator`**)**:
  - **Contract (Transitivity, Symmetry, Consistency)**:
    - **Transitivity**: If `(x.compareTo(y) > 0)` and `(y.compareTo(z) > 0)`, then `(x.compareTo(z) > 0)`.
    - **Symmetry**: `sgn(x.compareTo(y)) == -sgn(y.compareTo(x))` for all `x` and `y`.
    - **Consistency**: If `x.equals(y)`, then `x.compareTo(y)` must return 0.
    - **Consistency with equals**: It is highly recommended (though not strictly required for compilation) that (`x.compareTo(y) == 0`) implies `x.equals(y)`. If this is violated (i.e., `compareTo` says they are equal but `equals` says they are not), then tree-based collections (`TreeSet`, `TreeMap`) will behave inconsistently with hash-based collections (`HashSet`, `HashMap`). `TreeSet` / `TreeMap` will only consider elements "equal" if `compareTo` returns 0, potentially storing duplicates that `equals` would identify as distinct.
  - **Impact**: Incorrect `compareTo`/`compare` implementation leads to `TreeSet`/`TreeMap` sorting incorrectly or failing to store elements correctly.

**Best Practice**: Always generate these methods using your IDE or use `java.util.Objects.equals()` and `java.util.Objects.hash()` for `equals` and `hashCode`, and `Comparator.comparing()` and `thenComparing()` for `compareTo` logic.

---

## 15. Best Practices & Common Pitfalls
### Choosing the Right Data Structure
This is often the most important decision and depends on the specific requirements of your application.

| Requirement                                             | Recommended Collection                        | Rationale                                                          |
|---------------------------------------------------------|-----------------------------------------------|--------------------------------------------------------------------|
| Ordered, indexed access, frequent appends/reads         | `ArrayList`                                   | O(1) random access, O(1) amortized add to end                      |
| Ordered, frequent insertions/deletions at ends          | `LinkedList`                                  | O(1) for add/remove at ends                                        |
| Unique elements, fast lookups, order irrelevant         | `HashSet`                                     | O(1) average for add/contains/remove                               |
| Unique elements, preserve insertion order, fast lookups | `LinkedHashSet`                               | Combines `HashSet` speed with `LinkedList` order                   |
| Unique elements, sorted order, range queries            | `TreeSet`                                     | O(log N) for all operations, provides `NavigableSet` functionality |
| Key-value pairs, fast lookups, order irrelevant         | `HashMap`                                     | O(1) average for put/get/remove                                    |
| Key-value pairs, preserve insertion order, fast lookups | `LinkedHashMap`                               | Combines `HashMap` speed with `LinkedList` order, good for LRU     |
| Key-value pairs, sorted by keys, range queries          | `TreeMap`                                     | O(log N) for all operations, provides NavigableMap functionality   |
| FIFO queue                                              | `ArrayDeque` or `LinkedList`                  | `ArrayDeque` generally faster, `LinkedList` more flexible          |
| LIFO stack                                              | `ArrayDeque`                                  | Efficient, preferred over `Stack` (legacy)                         |
| Priority-based queue                                    | `PriorityQueue`                               | O(log N) for add/poll, O(1) for peek                               |
| Thread-safe Map                                         | `ConcurrentHashMap`                           | High concurrency, good performance for reads/writes                |
| Thread-safe List (read-heavy)                           | `CopyOnWriteArrayList`                        | Excellent for reads, expensive for writes                          |
| Bounded, blocking queue                                 | `ArrayBlockingQueue`, `LinkedBlockingQueue`   | Essential for producer-consumer patterns                           |
| Small, fixed, immutable collections                     | `List.of()`, `Set.of()`, `Map.of()` (Java 9+) | Memory efficient, truly immutable, good for constants              |
| Enum keys                                               | `EnumMap`                                     | Extremely performant, type-safe for enum keys                      |

### Avoiding Unnecessary Boxing
- **Boxing/Unboxing**: Occurs when primitive types (e.g., `int`, `double`) are automatically converted to their corresponding wrapper objects (`Integer`, `Double`) and vice versa.
- **Pitfall**: Using collections of primitive types directly (e.g., `List<int>`) is not allowed in Java. You must use wrapper types (`List<Integer>`). This leads to autoboxing overhead (creating new `Integer` objects for each primitive, and garbage collection of those objects).
- **Performance Impact**: Can be significant for large collections or performance-critical loops due to increased memory allocation and GC pressure.
- **Best Practice**:
  - For performance-critical code with large numbers of primitives, consider using primitive arrays (`int[]`, `double[]`) instead of `ArrayList<Integer>`.
  - Leverage `IntStream`, `LongStream`, `DoubleStream` in the Stream API to avoid boxing for intermediate operations.
  - If using collections of primitives, be aware of the overhead and profile if performance is critical.
   ```java
  // High boxing overhead
  List<Integer> boxedNumbers = new ArrayList<>();
  for (int i = 0; i < 1_000_000; i++) {
     boxedNumbers.add(i); // Autoboxing int to Integer
  }

  // Better with Streams for intermediate ops (IntStream)
  long sum = IntStream.range(0, 1_000_000).sum(); // No boxing for sum
   ```

### Null Handling in Various Collections
Different collection implementations have different rules regarding null keys and values.

| Collection Type               | Null Keys                          | Null Values                        |
|-------------------------------|------------------------------------|------------------------------------|
| `ArrayList`, `LinkedList`     | N/A (elements)                     | Yes                                |
| `HashSet`, `LinkedHashSet`    | Yes (one null element)             | N/A (elements are keys)            |
| `TreeSet`                     | No (`NullPointerException` on add) | N/A (elements are keys)            |
| `HashMap`, `LinkedHashMap`    | Yes (one null key)                 | Yes                                |
| `TreeMap`                     | No (`NullPointerException` on put) | Yes                                |
| `Hashtable`                   | No (`NullPointerException` on put) | No (`NullPointerException` on put) |
| `EnumMap`                     | No (`NullPointerException` on put) | Yes                                |
| `ArrayDeque`, `PriorityQueue` | No (`NullPointerException` on add) | N/A                                |
| `ConcurrentHashMap`           | No (`NullPointerException` on put) | No (`NullPointerException` on put) |
| `CopyOnWriteArrayList`        | Yes                                | Yes                                |

- **Pitfall**: Assuming `null` handling is consistent across all collections. This can lead to `NullPointerException` at runtime.
- **Best Practice**: Always check the documentation for specific collection classes regarding `null` support. For keys in maps/sets, if `null` is a valid domain value, ensure your chosen implementation supports it or handle null externally.

### Immutable vs. Mutable Collections
- **Mutable Collections**:
  - Can be modified after creation (add, remove, change elements).
  - **Pros**: Flexible, efficient for in-place modifications.
  - **Cons**: Not thread-safe by default. Sharing mutable collections across threads without proper synchronization is a common source of bugs. Can lead to unexpected side effects if references are shared.
  - **Examples**: All standard `ArrayList`, `HashMap`, `HashSet`, etc., instances are mutable by default.
- **Immutable Collections**:
  - Cannot be modified after creation. Any "modification" operation returns a new collection.
  - **Pros**:
    - **Thread-safe**: Inherently thread-safe, no synchronization needed for concurrent reads.
    - **Easier Reasoning**: Simplifies concurrent programming and reduces bugs related to shared mutable state.
    - **Cachable**: Can be safely cached and reused.
    - **Predictable**: Their state never changes, making debugging easier.
  - **Cons**: Can be less memory-efficient and performance-intensive for frequent modifications (each modification creates a new object).
  - **Examples**:
    - `Collections.unmodifiableList()` (provides an unmodifiable view, underlying can still change).
    - `List.of()`, `Set.of()`, `Map.of()` (Java 9+ factory methods for truly immutable collections).
    - Third-party libraries like Guava's Immutable Collections.

#### Best Practice:
- Favor immutability by default, especially for data that needs to be shared across threads or throughout the application.
- Use mutable collections when frequent in-place modifications are necessary and manage concurrency explicitly (e.g., using `java.util.concurrent` collections or external synchronization).
- For method parameters, prefer interfaces (`List`, `Set`, `Map`) over concrete implementations. If a method receives a `List` and shouldn't modify it, consider wrapping it with `Collections.unmodifiableList()` before passing or using an immutable factory method.

---

## 16. Java 21+ New Features in Collections
Java 21 introduced Sequenced Collections, a significant enhancement that addresses a long-standing inconsistency in the Collections Framework regarding defined encounter order.

### Sequenced Collections (JEP 431)
This JEP introduces three new interfaces to bring unified behavior for collections that have a defined encounter order:
- `SequencedCollection<E>`:
  - **Purpose**: Unifies `List`, `Deque`, and `LinkedHashSet` by providing common methods for accessing elements at either end and for reversing the collection.
  - **Super-interfaces**: Extends `Collection`.
  - **Operations**:
    - `addFirst(E e)`: Adds an element to the beginning.
    - `addLast(E e)`: Adds an element to the end.
    - `getFirst()`: Returns the first element.
    - `getLast()`: Returns the last element.
    - `removeFirst()`: Removes and returns the first element.
    - `removeLast()`: Removes and returns the last element.
    - `reversed()`: Returns a `SequencedCollection` view with elements in reverse order.
  - **Implementations**: `List` (e.g., `ArrayList`, `LinkedList`), `Deque` (e.g., `ArrayDeque`), and `LinkedHashSet` all implicitly implement `SequencedCollection` due to default methods, or by implementing the new interface directly.
- `SequencedSet<E>`:
  - **Purpose**: Combines `SequencedCollection` and `Set`. For sets that maintain an encounter order.
  - **Super-interfaces**: Extends `SequencedCollection` and `Set`.
  - **Operations**: Inherits all methods from `SequencedCollection` and `Set`.
  - **Implementations**: `LinkedHashSet` now implements `SequencedSet`. `TreeSet` also implements it via default methods, as it inherently maintains a sorted order.
- `SequencedMap<K, V>`:
  - **Purpose**: Provides a consistent API for maps that maintain an encounter order (either insertion order or sorted order).
  - **Super-interfaces**: Extends Map.
  - **Operations**:
    - `reversed()`: Returns a SequencedMap view with keys in reverse order.
    - `firstEntry()`, `lastEntry()`, `pollFirstEntry()`, `pollLastEntry()`, `firstKey()`, `lastKey()`.
    - `putFirst(K k, V v)`: Inserts/replaces at the beginning.
    - `putLast(K k, V v)`: Inserts/replaces at the end.
    - `sequencedKeySet()`, `sequencedValues()`, `sequencedEntrySet()`: Views of keys, values, and entries in sequence.
  - **Implementations**: LinkedHashMap and TreeMap now implement SequencedMap.

#### Code Example: Sequenced Collections (Java 21+)
```java
import java.util.ArrayList;
import java.util.LinkedHashSet;
import java.util.LinkedHashMap;
import java.util.SequencedCollection;
import java.util.SequencedSet;
import java.util.SequencedMap;
import java.util.List;
import java.util.Set;
import java.util.Map;
import java.util.Collections; // For unmodifiable

public class SequencedCollectionsExample {
    public static void main(String[] args) {
        // SequencedCollection - using ArrayList
        SequencedCollection<String> sequencedList = new ArrayList<>(List.of("B", "C"));
        sequencedList.addFirst("A"); // New in SequencedCollection
        sequencedList.addLast("D");  // New in SequencedCollection
        System.out.println("Sequenced List: " + sequencedList); // [A, B, C, D]
        System.out.println("First: " + sequencedList.getFirst());   // A
        System.out.println("Last: " + sequencedList.getLast());    // D
        System.out.println("Reversed List: " + sequencedList.reversed()); // [D, C, B, A]

        System.out.println("\n---");

        // SequencedSet - using LinkedHashSet
        SequencedSet<String> sequencedSet = new LinkedHashSet<>(Set.of("Banana", "Apple", "Date"));
        System.out.println("Sequenced Set (initial): " + sequencedSet); // [Banana, Apple, Date] (insertion order)
        sequencedSet.addFirst("Cherry"); // Adds to front
        System.out.println("Sequenced Set (addFirst): " + sequencedSet); // [Cherry, Banana, Apple, Date]
        System.out.println("Reversed Set: " + sequencedSet.reversed());   // [Date, Apple, Banana, Cherry]

        System.out.println("\n---");

        // SequencedMap - using LinkedHashMap
        SequencedMap<String, Integer> sequencedMap = new LinkedHashMap<>();
        sequencedMap.put("B", 2);
        sequencedMap.put("C", 3);
        sequencedMap.putFirst("A", 1); // New in SequencedMap
        sequencedMap.putLast("D", 4);  // New in SequencedMap
        System.out.println("Sequenced Map: " + sequencedMap); // {A=1, B=2, C=3, D=4}
        System.out.println("First Entry: " + sequencedMap.firstEntry()); // A=1
        System.out.println("Last Key: " + sequencedMap.lastKey());       // D
        System.out.println("Reversed Map: " + sequencedMap.reversed());  // {D=4, C=3, B=2, A=1}

        // Using reversed() view for iteration
        System.out.println("Iterating reversed map entries:");
        sequencedMap.reversed().entrySet().forEach(System.out::println);
    }
}
```

### Benefits of Sequenced Collections:
- **Unified API**: Provides a consistent way to deal with ordered collections, reducing the need for type casting and fragmented code.
- **Enhanced Readability**: Clearer intent for operations on ordered collections.
- **New Functionality**: `addFirst`, `addLast`, `getFirst`, `getLast`, `removeFirst`, `removeLast` are now standard across ordered collections. `reversed()` provides an elegant way to iterate/process in reverse.
- **Improved Mutability Management**: The `reversed()` method returns a view that reflects changes in the original collection. This is important for mutation-safe data sharing.

### Immutable Collections Enhancements
While `List.of()`, `Set.of()`, `Map.of()` were introduced in Java 9 to create truly immutable collections, Java 21's Sequenced Collections inherently interact with these.
- **Factory Methods**:
  - `List.of(...)`, `Set.of(...)`, `Map.of(...)`, `Map.entry(...)`
  - These create small, compact, and truly immutable collections. Attempting to modify them results in `UnsupportedOperationException`.
  - They are highly optimized for memory footprint (e.g., for very small collections, they might not even use an array, but store elements directly in fields).
- **Interoperability with Sequenced Collections**:
  - The immutable collections returned by these factory methods also implement the new `SequencedCollection`, `SequencedSet`, `SequencedMap` interfaces where applicable.
  - This means you can call `getFirst()`, `getLast()`, `reversed()` etc., on these immutable collections, but the "modification" methods like `addFirst()` will still throw `UnsupportedOperationException`.
   ```java
    import java.util.List;
    import java.util.SequencedCollection;
    import java.util.SequencedMap;
    import java.util.Map;
    
    public class ImmutableSequencedCollections {
        public static void main(String[] args) {
            SequencedCollection<String> immutableList = List.of("One", "Two", "Three");
            System.out.println("Immutable List: " + immutableList);
            System.out.println("First element: " + immutableList.getFirst());
            System.out.println("Reversed view: " + immutableList.reversed());
    
            try {
                immutableList.addFirst("Zero"); // Throws UnsupportedOperationException
            } catch (UnsupportedOperationException e) {
                System.out.println("Caught expected exception: " + e.getMessage());
            }
    
            SequencedMap<Integer, String> immutableMap = Map.of(1, "A", 2, "B");
            System.out.println("Immutable Map: " + immutableMap);
            System.out.println("First Entry: " + immutableMap.firstEntry());
            System.out.println("Reversed Map: " + immutableMap.reversed());
        }
    }
  ```

- **Mutation-Safe Data Sharing and Caching**: The core benefit of these truly immutable collections is their safety in concurrent environments. They can be shared freely among threads or cached without fear of being modified unexpectedly, simplifying complex concurrency logic.

### Performance Improvements (Beyond JEP 431 Specifics)
JVM and JDK continuously undergo optimizations. While specific JEPs directly related to performance in collections beyond what's tied to JEP 431 are less prominent for Java 21, general improvements include:
- **JVM Optimizations for Small and Empty Collections**: The JVM often optimizes the internal representation of small and empty collections, especially those returned by `List.of()`, `Set.of()`, `Map.of()`. They might use specialized, more compact internal structures, reducing memory footprint and startup time for frequently created small collections.
- **Reduced Memory Usage with Immutable Types**: Because immutable collections do not need to manage mutable state or synchronization overhead, their internal implementations can be more compact, leading to reduced memory usage. This is particularly true for the factory methods (e.g., `List.of()`) which can use highly specialized internal arrays or even constant pools.
- **JIT Compiler Improvements**: Ongoing enhancements in the JIT (Just-In-Time) compiler continue to improve the performance of various Java constructs, including collection operations, by generating more efficient machine code.

### Preview Features (If Applicable)
As of Java 21, the primary collection-related preview feature has been Sequenced Collections, which moved from preview to standard in Java 21.
- **Pattern Matching for Switch (JEP 441, finalized in Java 21)**: While not directly a collection enhancement, pattern matching for `switch` statements can make working with heterogeneous collections (e.g., a `List<Object>`) more expressive and less error-prone when you need to act differently based on the type of element.
    ```java
    import java.util.List;
    
    public class PatternMatchingCollections {
        public static void processElement(Object o) {
            switch (o) {
                case Integer i -> System.out.println("It's an Integer: " + i);
                case String s when s.length() > 5 -> System.out.println("Long String: " + s);
                case String s -> System.out.println("Short String: " + s);
                case List<?> l -> System.out.println("It's a List with " + l.size() + " elements");
                default -> System.out.println("Unknown type: " + o.getClass().getName());
            }
        }
    
        public static void main(String[] args) {
            List<Object> mixedList = List.of(10, "Hello", List.of(1, 2), "WorldWideWeb");
            mixedList.forEach(PatternMatchingCollections::processElement);
        }
    }
  ```

- **Structured Concurrency (JEP 453, preview in Java 21, finalized in Java 22)**: While not specific to collections, structured concurrency provides a safer and more robust way to manage concurrent tasks. When dealing with complex operations on collections that involve multiple parallel sub-tasks (e.g., processing elements in parallel, where each element's processing itself involves further concurrent calls), structured concurrency can simplify error handling and cancellation, making it easier to reason about the lifecycle of concurrent operations on data.

These recent updates underscore Java's commitment to evolving the Collections Framework, making it more robust, performant, and developer-friendly.

---

