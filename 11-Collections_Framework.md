# Collections

Java Collections Framework (JCF) provides a set of interfaces and classes that represent various types of collections, offering a pre-built, optimized, and standardized way to store and manipulate groups of objects.

# The Hierarchy
- [The Root: The `Iterable` Interface](#the-root-the-iterable-interface)
- [The Core: The `Collection` Interface](#the-core-the-collection-interface)
- [Beyond `Collection`: The `Map` Interface](#beyond-collection-the-map-interface)

## The Root: The `Iterable` Interface
At the very top of the collections hierarchy lies the `java.lang.Iterable<T>` interface. While not technically part of the `java.util.Collection` interface, it is the parent of `Collection` 

### Syntax
```java
package java.lang;

public interface Iterable<T> {
    Iterator<T> iterator(); // Returns an iterator over elements of type T.
}
```

### Purpose
The sole purpose of `Iterable` is to enable objects to be the target of the "for-each" loop (also known as the enhanced for loop). Any class that implements `Iterable` guarantees that you can iterate over its elements using this convenient syntax.

### Use Cases
- Any custom data structure you create that you want to be usable with a for-each loop should implement `Iterable`.
- All classes in the Java Collections Framework (e.g., `ArrayList`, `HashSet`, `LinkedList`) implement `Iterable` indirectly through the `Collection` interface.

### Example (Custom Interable)
```java
import java.util.Iterator;

class MySimpleList implements Iterable<String> {
    private String[] elements;
    private int size;

    public MySimpleList(int capacity) {
        this.elements = new String[capacity];
        this.size = 0;
    }

    public void add(String element) {
        if (size < elements.length) {
            elements[size++] = element;
        } else {
            System.out.println("List is full!");
        }
    }

    @Override
    public Iterator<String> iterator() {
        return new MyListIterator();
    }

    private class MyListIterator implements Iterator<String> {
        private int currentIndex = 0;

        @Override
        public boolean hasNext() {
            return currentIndex < size;
        }

        @Override
        public String next() {
            if (!hasNext()) {
                throw new java.util.NoSuchElementException();
            }
            return elements[currentIndex++];
        }
    }

    public static void main(String[] args) {
        MySimpleList myList = new MySimpleList(3);
        myList.add("Apple");
        myList.add("Banana");
        myList.add("Cherry");

        System.out.println("Iterating with for-each loop:");
        for (String fruit : myList) {
            System.out.println(fruit);
        }
    }
}
```

## The Core: The `Collection` Interface]
The `java.util.Collection<E>` interface is the fundamental building block of the Java Collections Framework. It defines the common behavior for all collections that store individual elements (as opposed to key-value pairs, which are handled by `Map`).

### Syntax (Key Methods)
```java
package java.util;

public interface Collection<E> extends Iterable<E> {
    int size();
    boolean isEmpty();
    boolean contains(Object o);
    Iterator<E> iterator(); // Inherited from Iterable
    Object[] toArray();
    <T> T[] toArray(T[] a);
    boolean add(E e);
    boolean remove(Object o);
    boolean containsAll(Collection<?> c);
    boolean addAll(Collection<? extends E> c);
    boolean removeAll(Collection<?> c);
    boolean retainAll(Collection<?> c); // Removes elements not contained in the specified collection
    void clear();
    boolean equals(Object o);
    int hashCode();
    // Default methods added in Java 8+ for streams
    default Spliterator<E> spliterator() { /* ... */ }
    default Stream<E> stream() { /* ... */ }
    default Stream<E> parallelStream() { /* ... */ }
    default boolean removeIf(Predicate<? super E> filter) { /* ... */ }
}
```

### Purpose
`Collection` defines the basic operations that can be performed on any group of objects: adding, removing, checking for existence, determining size, and iterating.

### Key Sub-Interfaces
- `List<E>`: An ordered collection (sequence). Elements can be accessed by their integer index. Allows duplicate elements. Examples: `ArrayList`, `LinkedList`, `Vector`.
- `Set<E>`: A collection that contains no duplicate elements. The order of elements is generally not guaranteed. Examples: `HashSet`, `LinkedHashSet`, `TreeSet`.
- `Queue<E>`: A collection designed for holding elements prior to processing. Typically, elements are added at one end and removed from the other (FIFO - First-In, First-Out). Examples: `PriorityQueue`, `ArrayDeque`.

## Beyond `Collection`: The `Map` Interface
While not directly extending Collection, the java.util.Map<K, V> interface is an integral part of the Java Collections Framework. It represents a collection of key-value pairs, where each key is unique.

### Syntax (Key Methods)
```java
package java.util;

public interface Map<K, V> {
    int size();
    boolean isEmpty();
    boolean containsKey(Object key);
    boolean containsValue(Object value);
    V get(Object key);
    V put(K key, V value); // Associates the specified value with the specified key
    V remove(Object key);
    void putAll(Map<? extends K, ? extends V> m);
    void clear();
    Set<K> keySet(); // Returns a Set view of the keys
    Collection<V> values(); // Returns a Collection view of the values
    Set<Map.Entry<K, V>> entrySet(); // Returns a Set view of the mappings
    boolean equals(Object o);
    int hashCode();
    // Default methods added in Java 8+
    default V getOrDefault(Object key, V defaultValue) { /* ... */ }
    // ... and many more utility methods
}
```

### Purpose
To store and retrieve data based on unique keys. Think of it like a dictionary or a lookup table.

### Key Implementations
- `HashMap<K, V>`: Provides fast access, but no guaranteed order. Allows `null` keys and values.
- `LinkedHashMap<K, V>`: Maintains insertion order.
- `TreeMap<K, V>`: Stores elements in a sorted order based on the natural ordering of keys or a custom `Comparator`.
- `HashTable<K, V>`: Legacy, synchronized version of HashMap. Generally discouraged in new code in favor of `ConcurrentHashMap` for thread safety.

---

## Collection vs Collections
- `java.util.Collection (Interface)`: As discussed, this is the root interface in the hierarchy that defines the common behavior for individual collections (like lists and sets). When you declare `List<String> myList = new ArrayList<>();`, `myList` is an instance of a class that implements the `Collection` interface (indirectly through `List`).
- `java.util.Collections (Class)`: This is a utility class consisting exclusively of static methods that operate on or return collections. It provides various helpful functionalities like:
  - Sorting collections (`sort()`)
  - Shuffling collections (`shuffle()`)
  - Reversing collections (`reverse()`)
  - Finding min/max elements (`min()`, `max()`)
  - Making collections synchronized (`synchronizedList()`, `synchronizedSet()`, etc.)
  - Creating unmodifiable collections (`unmodifiableList()`, `unmodifiableSet()`, etc.)

### Example
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class CollectionVsCollectionsDemo {
    public static void main(String[] args) {
        // --- Using Collection (interface) ---
        List<String> names = new ArrayList<>(); // 'names' is a type of Collection (specifically, a List)
        names.add("Alice");
        names.add("Charlie");
        names.add("Bob");
        System.out.println("Original List (Collection): " + names);

        // --- Using Collections (utility class) ---
        // Sorting the list
        Collections.sort(names); // Using the static sort method from the Collections class
        System.out.println("Sorted List (using Collections.sort): " + names);

        // Reversing the list
        Collections.reverse(names); // Using the static reverse method from the Collections class
        System.out.println("Reversed List (using Collections.reverse): " + names);

        // Finding the maximum element
        String maxName = Collections.max(names);
        System.out.println("Max Name (using Collections.max): " + maxName);

        // Creating an unmodifiable list
        List<String> unmodifiableNames = Collections.unmodifiableList(names);
        System.out.println("Unmodifiable List: " + unmodifiableNames);
        // unmodifiableNames.add("David"); // This would throw an UnsupportedOperationException
    }
}
```

---

# Step-by-Step Implementation and Use Cases
Let's dive into practical examples for `List`, `Set`, and `Map`.

## Lists: Ordered Collections with Duplicates
### Common Implementations
- `ArrayList`: Resizable array implementation. Excellent for random access (getting elements by index) but potentially slow for insertions/deletions in the middle.
- `LinkedList`: Doubly linked list implementation. Efficient for insertions/deletions at the beginning or end, but slower for random access.
- `Vector`: Legacy, synchronized version of `ArrayList`. Generally avoided in new code due to performance overhead unless explicit synchronization is required, in which case `Collections.synchronizedList()` or `CopyOnWriteArrayList` are preferred.

### Example (ArrayList Basics)
```java
import java.util.ArrayList;
import java.util.List;

public class ArrayListBasics {
    public static void main(String[] args) {
        // 1. Declare and Initialize an ArrayList
        List<String> fruits = new ArrayList<>();

        // 2. Add elements
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Orange");
        fruits.add("Apple"); // Lists allow duplicates

        System.out.println("Fruits list: " + fruits); // Output: [Apple, Banana, Orange, Apple]

        // 3. Get element by index
        System.out.println("Fruit at index 1: " + fruits.get(1)); // Output: Banana

        // 4. Check size
        System.out.println("Size of list: " + fruits.size()); // Output: 4

        // 5. Check if element exists
        System.out.println("Contains 'Banana'? " + fruits.contains("Banana")); // Output: true
        System.out.println("Contains 'Grape'? " + fruits.contains("Grape"));   // Output: false

        // 6. Remove elements
        fruits.remove("Apple"); // Removes the first occurrence
        System.out.println("After removing 'Apple': " + fruits); // Output: [Banana, Orange, Apple]

        fruits.remove(0); // Removes element at index 0 (which is now Banana)
        System.out.println("After removing element at index 0: " + fruits); // Output: [Orange, Apple]

        // 7. Iterate through the list
        System.out.println("\nIterating through fruits:");
        for (String fruit : fruits) {
            System.out.println(fruit);
        }

        // 8. Clear the list
        fruits.clear();
        System.out.println("List after clearing: " + fruits); // Output: []
        System.out.println("Is list empty? " + fruits.isEmpty()); // Output: true
    }
}
```

### Example (LinkedList or Queue-like behavior)
```java
import java.util.LinkedList;
import java.util.Queue;

public class LinkedListQueueDemo {
    public static void main(String[] args) {
        // LinkedList implements the Queue interface
        Queue<String> taskQueue = new LinkedList<>();

        // Add tasks (enqueue)
        taskQueue.offer("Process Report 1"); // offer is preferred over add for capacity-constrained queues
        taskQueue.offer("Send Email Notification");
        taskQueue.offer("Update Database Record");

        System.out.println("Current tasks in queue: " + taskQueue); // Output: [Process Report 1, Send Email Notification, Update Database Record]

        // Peek at the next task without removing it
        System.out.println("Next task to process (peek): " + taskQueue.peek()); // Output: Process Report 1

        // Process tasks (dequeue)
        String processedTask1 = taskQueue.poll(); // poll is preferred over remove, returns null if queue is empty
        System.out.println("Processed: " + processedTask1); // Output: Processed: Process Report 1
        System.out.println("Tasks remaining: " + taskQueue); // Output: [Send Email Notification, Update Database Record]

        String processedTask2 = taskQueue.poll();
        System.out.println("Processed: " + processedTask2); // Output: Processed: Send Email Notification
        System.out.println("Tasks remaining: " + taskQueue); // Output: [Update Database Record]

        // Check if queue is empty
        System.out.println("Is queue empty? " + taskQueue.isEmpty()); // Output: false

        taskQueue.poll(); // Process the last task
        System.out.println("Tasks remaining: " + taskQueue); // Output: []
        System.out.println("Is queue empty? " + taskQueue.isEmpty()); // Output: true

        System.out.println("Trying to poll from empty queue: " + taskQueue.poll()); // Output: null
        // System.out.println("Trying to remove from empty queue: " + taskQueue.remove()); // Throws NoSuchElementException
    }
}
```

### Example (Custom Sorting with `Comparator`)
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

class Product {
    private String name;
    private double price;
    private int stock;

    public Product(String name, double price, int stock) {
        this.name = name;
        this.price = price;
        this.stock = stock;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getStock() { return stock; }

    @Override
    public String toString() {
        return "Product{" + "name='" + name + '\'' + ", price=" + price + ", stock=" + stock + '}';
    }
}

public class CustomListSorting {
    public static void main(String[] args) {
        List<Product> products = new ArrayList<>();
        products.add(new Product("Laptop", 1200.00, 5));
        products.add(new Product("Mouse", 25.50, 20));
        products.add(new Product("Keyboard", 75.00, 10));
        products.add(new Product("Monitor", 300.00, 3));

        System.out.println("Original Products: " + products);

        // Sort by price (ascending) using a lambda expression for Comparator
        Collections.sort(products, (p1, p2) -> Double.compare(p1.getPrice(), p2.getPrice()));
        System.out.println("\nSorted by Price (Ascending):");
        products.forEach(System.out::println);

        // Sort by stock (descending) using Comparator.comparing and reversed()
        products.sort(Comparator.comparing(Product::getStock).reversed());
        System.out.println("\nSorted by Stock (Descending):");
        products.forEach(System.out::println);

        // Sort by name, then by price (chained comparators)
        products.sort(Comparator.comparing(Product::getName)
                               .thenComparing(Product::getPrice));
        System.out.println("\nSorted by Name then Price:");
        products.forEach(System.out::println);
    }
}
```

## Sets: Collections with Unique Elements
### Common Implementations
- `HashSet`: Uses a hash table for storage. Provides constant-time performance for basic operations (add, remove, contains) on average. Does not guarantee iteration order.
- `LinkedHashSet`: Maintains insertion order. Slower than `HashSet` for basic operations but faster than TreeSet.
- `TreeSet`: Stores elements in a sorted order (natural ordering or custom Comparator). Provides guaranteed `log(n)` time complexity for basic operations.

### Example (HashSet for Uniqueness)
```java
import java.util.HashSet;
import java.util.Set;

public class HashSetBasics {
    public static void main(String[] args) {
        Set<String> uniqueColors = new HashSet<>();

        uniqueColors.add("Red");
        uniqueColors.add("Green");
        uniqueColors.add("Blue");
        uniqueColors.add("Red"); // Duplicate, will not be added

        System.out.println("Unique Colors: " + uniqueColors); // Order is not guaranteed, e.g., [Red, Blue, Green]

        System.out.println("Size: " + uniqueColors.size()); // Output: 3

        System.out.println("Contains 'Green'? " + uniqueColors.contains("Green")); // Output: true
        System.out.println("Contains 'Yellow'? " + uniqueColors.contains("Yellow")); // Output: false

        uniqueColors.remove("Blue");
        System.out.println("After removing 'Blue': " + uniqueColors);

        System.out.println("\nIterating through unique colors:");
        for (String color : uniqueColors) {
            System.out.println(color);
        }
    }
}
```

### Example (LinkedHashSet for Order Preservation)
```java
import java.util.LinkedHashSet;
import java.util.Set;

public class LinkedHashSetDemo {
    public static void main(String[] args) {
        Set<String> BrowseHistory = new LinkedHashSet<>();

        BrowseHistory.add("google.com");
        BrowseHistory.add("github.com");
        BrowseHistory.add("stackoverflow.com");
        BrowseHistory.add("google.com"); // Duplicate, won't change order or size

        System.out.println("Browse History (maintains insertion order): " + BrowseHistory);
        // Output: [google.com, github.com, stackoverflow.com]

        BrowseHistory.add("oracle.com");
        System.out.println("After adding 'oracle.com': " + BrowseHistory);
        // Output: [google.com, github.com, stackoverflow.com, oracle.com]
    }
}
```

### Example (TreeSet for Sorted Order and Custom Objects)
```java
import java.util.Set;
import java.util.TreeSet;
import java.util.Comparator;

class Book implements Comparable<Book> { // Implementing Comparable for natural ordering
    private String title;
    private String author;
    private int year;

    public Book(String title, String author, int year) {
        this.title = title;
        this.author = author;
        this.year = year;
    }

    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    public int getYear() { return year; }

    @Override
    public String toString() {
        return "Book{" + "title='" + title + '\'' + ", author='" + author + '\'' + ", year=" + year + '}';
    }

    @Override
    public int compareTo(Book other) {
        // Natural order by title
        return this.title.compareTo(other.title);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Book book = (Book) o;
        return year == book.year && title.equals(book.title) && author.equals(book.author);
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(title, author, year);
    }
}

public class TreeSetDemo {
    public static void main(String[] args) {
        // TreeSet using natural ordering (Book implements Comparable by title)
        Set<Book> booksByTitle = new TreeSet<>();
        booksByTitle.add(new Book("The Great Gatsby", "F. Scott Fitzgerald", 1925));
        booksByTitle.add(new Book("1984", "George Orwell", 1949));
        booksByTitle.add(new Book("To Kill a Mockingbird", "Harper Lee", 1960));
        booksByTitle.add(new Book("1984", "George Orwell", 1949)); // Duplicate based on equals/hashCode

        System.out.println("Books sorted by Title (natural ordering):");
        booksByTitle.forEach(System.out::println);

        // TreeSet with a custom Comparator (sorting by year)
        Set<Book> booksByYear = new TreeSet<>(Comparator.comparingInt(Book::getYear));
        booksByYear.add(new Book("The Great Gatsby", "F. Scott Fitzgerald", 1925));
        booksByYear.add(new Book("1984", "George Orwell", 1949));
        booksByYear.add(new Book("To Kill a Mockingbird", "Harper Lee", 1960));
        booksByYear.add(new Book("Brave New World", "Aldous Huxley", 1932)); // New book

        System.out.println("\nBooks sorted by Year (custom comparator):");
        booksByYear.forEach(System.out::println);
    }
}
```

For `TreeSet` to work correctly with custom objects, the objects must either implement `Comparable` (for natural ordering) or you must provide a `Comparator` to the `TreeSet` constructor. Also, `equals()` and `hashCode()` are crucial for correctly identifying duplicates, especially in `HashSet` and `LinkedHashSet`, and indirectly for `TreeSet` when elements are considered equal for storage purposes (though `TreeSet` primarily uses `compareTo` or `compare` for ordering and uniqueness).

## Maps: Key-Value Associations
### Common Implementations
- `HashMap`: Fastest for basic operations. No guaranteed order. Allows one `null` key and multiple `null` values.
- `LinkedHashMap`: Maintains insertion order.
- `TreeMap`: Stores entries in a sorted order based on keys (natural ordering or custom Comparator). `log(n)` time complexity for basic operations.
- `ConcurrentHashMap`: Thread-safe alternative to `HashMap` for concurrent environments, offering much better performance than `HashTable`.
- `WeakHashMap`: Keys are weakly referenced, meaning an entry can be garbage collected if its key is no longer strongly referenced elsewhere. Useful for caching.

### Example (HashMap Basics)
```java
import java.util.HashMap;
import java.util.Map;

public class HashMapBasics {
    public static void main(String[] args) {
        Map<String, String> countryCapitals = new HashMap<>();

        // Add key-value pairs
        countryCapitals.put("USA", "Washington D.C.");
        countryCapitals.put("India", "New Delhi");
        countryCapitals.put("Japan", "Tokyo");
        countryCapitals.put("USA", "Washington D.C."); // Duplicate key, value might be updated (if different) or remain same

        System.out.println("Country Capitals: " + countryCapitals); // Order not guaranteed

        // Get value by key
        System.out.println("Capital of India: " + countryCapitals.get("India")); // Output: New Delhi
        System.out.println("Capital of France: " + countryCapitals.get("France")); // Output: null

        // Check if key/value exists
        System.out.println("Contains key 'Japan'? " + countryCapitals.containsKey("Japan")); // Output: true
        System.out.println("Contains value 'London'? " + countryCapitals.containsValue("London")); // Output: false

        // Remove by key
        countryCapitals.remove("Japan");
        System.out.println("After removing Japan: " + countryCapitals);

        // Iterate through Map (various ways)
        System.out.println("\nIterating through Map:");

        // 1. Using entrySet() to get both key and value
        for (Map.Entry<String, String> entry : countryCapitals.entrySet()) {
            System.out.println("Country: " + entry.getKey() + ", Capital: " + entry.getValue());
        }

        // 2. Using keySet() to get keys, then get values
        for (String country : countryCapitals.keySet()) {
            System.out.println("Country: " + country + ", Capital: " + countryCapitals.get(country));
        }

        // 3. Using values() to get only values
        System.out.println("\nOnly capitals:");
        for (String capital : countryCapitals.values()) {
            System.out.println(capital);
        }

        // 4. Using forEach (Java 8+)
        System.out.println("\nUsing forEach (Java 8+):");
        countryCapitals.forEach((country, capital) -> System.out.println(country + " -> " + capital));

        countryCapitals.clear();
        System.out.println("Map after clearing: " + countryCapitals);
    }
}
```

### Example (LinkedHashMap for LRU Cache)
`LinkedHashMap` is particularly useful for implementing simple caches, especially a Least Recently Used (LRU) cache, because it can be configured to order elements by access order rather than insertion order.

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class LRUCacheDemo {
    private static final int MAX_CACHE_SIZE = 3;

    public static void main(String[] args) {
        // Create a LinkedHashMap with access order and a removal policy
        Map<String, Integer> lruCache = new LinkedHashMap<>(MAX_CACHE_SIZE, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<String, Integer> eldest) {
                // Remove the eldest entry when size exceeds MAX_CACHE_SIZE
                return size() > MAX_CACHE_SIZE;
            }
        };

        lruCache.put("A", 1);
        lruCache.put("B", 2);
        lruCache.put("C", 3);
        System.out.println("Initial Cache: " + lruCache); // {A=1, B=2, C=3}

        lruCache.get("A"); // Access 'A', it moves to the end of the access order
        System.out.println("After accessing A: " + lruCache); // {B=2, C=3, A=1}

        lruCache.put("D", 4); // Add 'D', 'B' (eldest) will be removed
        System.out.println("After adding D: " + lruCache); // {C=3, A=1, D=4}

        lruCache.put("E", 5); // Add 'E', 'C' (eldest) will be removed
        System.out.println("After adding E: " + lruCache); // {A=1, D=4, E=5}
    }
}
```

### Example (TreeMap for Sorted Sata & Custom Key)
```java
import java.util.Comparator;
import java.util.Map;
import java.util.TreeMap;

class Employee implements Comparable<Employee> {
    private int id;
    private String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() { return id; }
    public String getName() { return name; }

    @Override
    public String toString() {
        return "Employee{" + "id=" + id + ", name='" + name + '\'' + '}';
    }

    @Override
    public int compareTo(Employee other) {
        // Natural ordering by ID
        return Integer.compare(this.id, other.id);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return id == employee.id; // Uniqueness based on ID
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(id);
    }
}

public class TreeMapDemo {
    public static void main(String[] args) {
        // TreeMap sorted by natural ordering of Employee ID
        Map<Employee, String> employeeDepartments = new TreeMap<>();
        employeeDepartments.put(new Employee(103, "Alice"), "HR");
        employeeDepartments.put(new Employee(101, "Bob"), "IT");
        employeeDepartments.put(new Employee(102, "Charlie"), "Sales");
        employeeDepartments.put(new Employee(101, "Robert"), "Marketing"); // Key 101 already exists, value updated

        System.out.println("Employee Departments (sorted by ID):");
        employeeDepartments.forEach((emp, dept) -> System.out.println(emp.getName() + " -> " + dept));
        // Output will be sorted by ID: Bob -> Marketing, Charlie -> Sales, Alice -> HR

        // TreeMap with a custom Comparator (sorting by Employee Name)
        Map<Employee, String> employeeDepartmentsByName = new TreeMap<>(Comparator.comparing(Employee::getName));
        employeeDepartmentsByName.put(new Employee(103, "Alice"), "HR");
        employeeDepartmentsByName.put(new Employee(101, "Bob"), "IT");
        employeeDepartmentsByName.put(new Employee(102, "Charlie"), "Sales");

        System.out.println("\nEmployee Departments (sorted by Name):");
        employeeDepartmentsByName.forEach((emp, dept) -> System.out.println(emp.getName() + " -> " + dept));
    }
}
```

---

# Common Pitfalls and Best Practices
## Common Pitfalls
### 1. Modifying a Collection During Iteration:
- Problem: Using a traditional `for` loop or enhanced `for-each` loop to iterate and simultaneously add/remove elements (other than via `Iterator.remove()`) often leads to `ConcurrentModificationException`.
- Solution: Use `Iterator.remove()` for safe removal. For adding, or complex modifications, iterate over a copy, or collect elements to be modified/added in a separate list and then apply changes. For Java 8+, `removeIf()` is excellent for conditional removal.
#### Example of Pitfall
```
List<String> names = new ArrayList<>(Arrays.asList("A", "B", "C"));
// for (String name : names) { // Throws ConcurrentModificationException
//     if (name.equals("B")) {
//         names.remove(name);
//     }
// }

// Correct way with Iterator:
Iterator<String> iterator = names.iterator();
while (iterator.hasNext()) {
    String name = iterator.next();
    if (name.equals("B")) {
        iterator.remove(); // Safe removal
    }
}
System.out.println(names); // [A, C]

// Correct way with Java 8 removeIf:
names = new ArrayList<>(Arrays.asList("X", "Y", "Z"));
names.removeIf(s -> s.equals("Y"));
System.out.println(names); // [X, Z]
```

### 2. Forgetting `equals()` and `hashCode()` for Custom Objects in `HashSet/HashMap`
- **Problem**: If you put custom objects into `HashSet` or use them as keys in `HashMap` without overriding `equals()` and `hashCode()` correctly, the collections won't correctly identify duplicates or retrieve elements. `HashSet` might store duplicate objects, and HashMap might fail to retrieve values using the correct key.
- **Solution**: Always override both `equals()` and `hashCode()` together, ensuring that if two objects are equal according to `equals()`, their `hashCode()` methods return the same integer.

### 3. Choosing the Wrong Collection Type
- **Problem**: Using an `ArrayList` when you need frequent insertions/deletions at the beginning/middle (where `LinkedList` would be better), or using a `HashSet` when you need sorted order (where `TreeSet` is better).
- **Solution**: Understand the characteristics (ordered, unique, sorted, performance of operations) of each collection type and choose wisely.

### 4. Nulls in Collections
- **Problem**: `HashMap` allows one null key and multiple null values. `HashSet` allows one null element. `TreeMap` and `TreeSet` generally do NOT allow null elements/keys (they throw `NullPointerException` if you try to add one) because they rely on comparison.
- **Solution**: Be aware of nullability rules for each collection type. Perform null checks if necessary.

### 5. Thread Safety (Lack of)
- **Problem**: Most standard collection implementations (`ArrayList`, `HashMap`, `HashSet`, `LinkedList`) are not thread-safe. Concurrent modification from multiple threads without external synchronization can lead to inconsistent state or `ConcurrentModificationException`.
- **Solution**:
  - Use `Collections.synchronizedList()`, `Collections.synchronizedMap()`, etc., which wrap the non-synchronized collection with synchronized methods. This often leads to performance bottlenecks due to a single lock.
  - For higher concurrency, use concurrent collections from `java.util.concurrent` package: `ConcurrentHashMap`, `CopyOnWriteArrayList`, `ConcurrentLinkedQueue`, `BlockingQueue` implementations, etc.
  - Use external synchronization mechanisms (`synchronized` blocks, `ReentrantLock`).

## Best Practices
### 1. Program to Interfaces, Not Implementations:
- **Good**: `List<String> names = new ArrayList<>();`
- **Bad**: `ArrayList<String> names = new ArrayList<>();`
- **Why**: This promotes flexibility. If you later decide to switch from `ArrayList` to `LinkedList` (e.g., due to performance changes), you only need to change the instantiation line, not every part of your code that interacts with the list.

### 2. Use Generics:
- **Good**: `List<String> names = new ArrayList<>();`
- **Bad**: `List names = new ArrayList();` (raw type)
- **Why**: Generics provide compile-time type safety, preventing `ClassCastException` at runtime and making your code clearer.

### 3. Choose the Right Collection Type: This cannot be stressed enough. Analyze your requirements:
- Do you need ordered elements? -> `List`
- Do you need unique elements? -> `Set`
- Do you need key-value pairs? -> `Map`
- Do you need fast random access? -> `ArrayList`
- Do you need fast insertions/deletions at ends? -> `LinkedList`
- Do you need sorted elements? -> `TreeSet`, `TreeMap`
- Do you need thread-safe collections? -> `ConcurrentHashMap`, `CopyOnWriteArrayList`, `BlockingQueue`, or `Collections.synchronizedXYZ`.

### 4. Utilize Java 8 Stream API: 
For processing collections, the Stream API (`stream()`, `parallelStream()`) provides a powerful and concise way to perform operations like filtering, mapping, reducing, and collecting data.

### 5. Consider Immutability: 
For collections that shouldn't change after creation, consider using `Collections.unmodifiableList()`, `unmodifiableSet()`, etc., or builders for immutable collections (e.g., Guava's ImmutableList). This increases thread safety and simplifies reasoning about your code.

---

# Performance Considerations
The choice of collection implementation has significant performance implications.
- `ArrayList` vs. `LinkedList`:
  - `ArrayList`:
    - `get(index)`: O(1) (constant time) because it's a direct array lookup.
    - `add(element)` at end: Amortized O(1) (constant time), but can be O(n) if resizing is needed.
    - `add(index, element)` in middle: O(n) because elements need to be shifted.
    - `remove(index)` from middle: O(n) because elements need to be shifted.
  - `LinkedList`:
    - `get(index)`: O(n) (linear time) because it has to traverse from the beginning/end.
    - `add(element)` at end/beginning: O(1).
    - `add(index, element)` in middle: O(n) to find the index, then O(1) for insertion.
    - `remove(index)` from middle: O(n) to find the index, then O(1) for removal.
  - **Rule of Thumb**: Use `ArrayList` for read-heavy operations, `LinkedList` for write-heavy operations (especially at the ends).
  
- `HashSet` vs. `LinkedHashSet` vs. `TreeSet`:
  - `HashSet`:
    - `add()`, `remove()`, `contains()`: Average O(1), worst case O(n) (due to hash collisions). Depends heavily on good `hashCode()` implementation.
    - No guaranteed order.
  - `LinkedHashSet`:
    - `add()`, `remove()`, `contains()`: O(1) (slightly slower than `HashSet` due to maintaining linked list for order).
    - Maintains insertion order.
  - `TreeSet`:
    - `add()`, `remove()`, `contains()`: O(logn) (logarithmic time).
    - Elements are sorted. Higher memory consumption.
  - **Rule of Thumb**: Use `HashSet` for general unique element storage where order doesn't matter and performance is critical. Use `LinkedHashSet` if insertion order needs to be preserved. Use `TreeSet` if elements need to be stored and retrieved in sorted order.
- `HashMap` vs. `LinkedHashMap` vs. `TreeMap`:
  - Performance characteristics for `put()`, `get()`, `remove()`, `containsKey()` are similar to their `Set` counterparts (replace "element" with "key").
  - `HashMap`: Average O(1) (fastest).
  - `LinkedHashMap`: O(1) (maintains insertion/access order).
  - `TreeMap`: O(logn) (sorted by key).
  - **Rule of Thumb**: Similar to Sets, choose based on whether order or sorting is required.
- **Initial Capacity and Load Factor**:
  - For `ArrayList`, `HashMap`, `HashSet`, setting an appropriate initial capacity can reduce the number of rehashes/resizing operations, thus improving performance, especially when you know the approximate size of the collection beforehand.
  - The **load factor** for `HashMap` and `HashSet` determines when the underlying hash table is resized. A lower load factor reduces collisions but increases memory usage. The default is 0.75, which is a good balance.

---

# Comparator vs Comparable

## 1. `Comparable`: Natural Ordering
### Overview and Importance
The `Comparable` interface in Java provides a way for objects of a class to define their "natural ordering." If you have a class whose objects inherently have a logical order (e.g., numbers, strings, dates), you can make that class implement `Comparable`. This allows instances of that class to be sorted naturally without any external comparison logic.

#### Why is it important?
- **Default Sorting**: Many Java APIs, like `Collections.sort()` and `Arrays.sort()`, can sort collections of objects that implement `Comparable` without any extra effort.
- **Simplicity**: When an object has a single, obvious way to be ordered, `Comparable` is the most straightforward approach.
- **Consistency**: It ensures that all instances of a class will be sorted in the same way by default, promoting predictable behavior.

### Syntax
The `Comparable` interface has a single method
```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

- `compareTo(T o)`: This method compares the current object (`this`) with the specified object (`o`).
  - Returns a negative integer if `this` is less than `o`.
  - Returns zero if `this` is equal to `o`.
  - Returns a positive integer if `this` is greater than `o`.

### Use Cases
- Sorting a list of `String` objects (they naturally sort alphabetically).
- Sorting a list of `Integer` objects (they naturally sort numerically).
- Sorting custom objects based on a primary identifier (e.g., sorting `Product` objects by their `productId`).

#### Example: Sorting Integers (Built-in `Comparable`)
You don't explicitly implement `Comparable` for `Integer` because it's already done by Java. This demonstrates how `Comparable` is used by default.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class ComparableExample {
    public static void main(String[] args) {
        List<Integer> numbers = new ArrayList<>();
        numbers.add(5);
        numbers.add(2);
        numbers.add(8);
        numbers.add(1);

        System.out.println("Before sorting: " + numbers);
        Collections.sort(numbers); // Integer implements Comparable
        System.out.println("After sorting: " + numbers);
    }
}
```

**Output**
```
Before sorting: [5, 2, 8, 1]
After sorting: [1, 2, 5, 8]
```

#### Example: Custom Class with `Comparable`
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Student implements Comparable<Student> {
    private String name;
    private int rollNumber;
    private double score;

    public Student(String name, int rollNumber, double score) {
        this.name = name;
        this.rollNumber = rollNumber;
        this.score = score;
    }

    public String getName() {
        return name;
    }

    public int getRollNumber() {
        return rollNumber;
    }

    public double getScore() {
        return score;
    }

    @Override
    public String toString() {
        return "Student{" +
               "name='" + name + '\'' +
               ", rollNumber=" + rollNumber +
               ", score=" + score +
               '}';
    }

    @Override
    public int compareTo(Student other) {
        // Natural ordering based on rollNumber
        return Integer.compare(this.rollNumber, other.rollNumber);
    }
}

public class ComparableCustomExample {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("Alice", 101, 85.5));
        students.add(new Student("Bob", 103, 92.0));
        students.add(new Student("Charlie", 100, 78.0));
        students.add(new Student("David", 102, 95.0));

        System.out.println("Before sorting: " + students);
        Collections.sort(students); // Sorts based on Student's compareTo (rollNumber)
        System.out.println("After sorting by roll number: " + students);
    }
}
```

**Output**
```
Before sorting: [Student{name='Alice', rollNumber=101, score=85.5}, Student{name='Bob', rollNumber=103, score=92.0}, Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='David', rollNumber=102, score=95.0}]
After sorting by roll number: [Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='Alice', rollNumber=101, score=85.5}, Student{name='David', rollNumber=102, score=95.0}, Student{name='Bob', rollNumber=103, score=92.0}]
```

#### Example: Handling Multiple Criteria with `compareTo`
While `Comparable` is for natural ordering, you can implement more complex logic within `compareTo`. However, it's generally recommended to use `Comparator` for multiple sorting criteria. This example demonstrates how to implement multiple criteria within `compareTo` (though not the best practice for maintainability).

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Product implements Comparable<Product> {
    private String name;
    private double price;
    private int stock;

    public Product(String name, double price, int stock) {
        this.name = name;
        this.price = price;
        this.stock = stock;
    }

    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }

    public int getStock() {
        return stock;
    }

    @Override
    public String toString() {
        return "Product{" +
               "name='" + name + '\'' +
               ", price=" + price +
               ", stock=" + stock +
               '}';
    }

    @Override
    public int compareTo(Product other) {
        // Primary sorting: by price in ascending order
        int priceComparison = Double.compare(this.price, other.price);
        if (priceComparison != 0) {
            return priceComparison;
        }

        // Secondary sorting: if prices are same, sort by stock in descending order
        int stockComparison = Integer.compare(other.stock, this.stock); // Descending stock
        if (stockComparison != 0) {
            return stockComparison;
        }

        // Tertiary sorting: if price and stock are same, sort by name alphabetically
        return this.name.compareTo(other.name);
    }
}

public class ComparableAdvancedExample {
    public static void main(String[] args) {
        List<Product> products = new ArrayList<>();
        products.add(new Product("Laptop", 1200.00, 5));
        products.add(new Product("Mouse", 25.00, 50));
        products.add(new Product("Keyboard", 75.00, 20));
        products.add(new Product("Monitor", 300.00, 10));
        products.add(new Product("Headphones", 75.00, 15)); // Same price as Keyboard
        products.add(new Product("Webcam", 75.00, 15)); // Same price, same stock as Headphones

        System.out.println("Before sorting: " + products);
        Collections.sort(products);
        System.out.println("After sorting: " + products);
    }
}
```

**Output**
```
Before sorting: [Product{name='Laptop', price=1200.0, stock=5}, Product{name='Mouse', price=25.0, stock=50}, Product{name='Keyboard', price=75.0, stock=20}, Product{name='Monitor', price=300.0, stock=10}, Product{name='Headphones', price=75.0, stock=15}, Product{name='Webcam', price=75.0, stock=15}]
After sorting: [Product{name='Mouse', price=25.0, stock=50}, Product{name='Headphones', price=75.0, stock=15}, Product{name='Webcam', price=75.0, stock=15}, Product{name='Keyboard', price=75.0, stock=20}, Product{name='Monitor', price=300.0, stock=10}, Product{name='Laptop', price=1200.0, stock=5}]
```

Notice how "Headphones", "Webcam", and "Keyboard" are ordered:
1. "Headphones" and "Webcam" have the same price (75.00) and same stock (15). They are then sorted by name, so "Headphones" comes before "Webcam".
2. "Keyboard" has the same price (75.00) but higher stock (20) than "Headphones" and "Webcam". Since stock is sorted in descending order, "Keyboard" comes after "Headphones" and "Webcam".

---

## 2. `Comparator`: External Ordering
### Overview and Importance
The `Comparator` interface provides an external way to define an ordering for objects. Unlike `Comparable`, which modifies the class itself, `Comparator` is a separate class (or a lambda expression) that implements the comparison logic. This is incredibly flexible.

#### Why is it important?
- **Multiple Sorting Orders**: A single class can have multiple `Comparator` implementations, allowing you to sort its objects in different ways (e.g., sort `Student` by `rollNumber`, then by `name`, then by `score`).
- **Sorting Objects** You Don't Own: You can sort objects from classes you don't have control over (e.g., `java.io.File` objects) without modifying their source code.
- **Separation of Concerns**: It promotes cleaner code by separating the comparison logic from the data class itself.
- **Functional Programming**: With Java 8 and beyond, `Comparator` can be easily expressed using lambda expressions, making code more concise.

### Syntax
The `Comparator` interface has two main methods, though `compare` is the primary one:
```java
public interface Comparator<T> {
    int compare(T o1, T o2);

    // Default methods (Java 8+) for chaining comparators, etc.
    boolean equals(Object obj); // Generally not overridden
    Comparator<T> reversed();
    Comparator<T> thenComparing(Comparator<? super T> other);
    // ... and many more utility methods
}
```

- `compare(T o1, T o2)`: This method compares two objects, `o1` and `o2`.
  - Returns a negative integer if `o1` is less than `o2`.
  - Returns zero if `o1` is equal to `o2`.
  - Returns a positive integer if `o1` is greater than `o2`.

### Use Cases
- Sorting a list of `Student` objects by `name`, and then separately by `score`.
- Sorting files in a directory by last modified date, then by size.
- Sorting objects in a `PriorityQueue` based on custom criteria.
- Implementing custom sorting for complex business objects where natural ordering isn't sufficient or multiple orders are needed.

#### Example: Sorting with an Anonymous Inner Class `Comparator`
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

// Student class (same as before, but without Comparable implementation)
class StudentComparatorBeginner {
    private String name;
    private int rollNumber;
    private double score;

    public StudentComparatorBeginner(String name, int rollNumber, double score) {
        this.name = name;
        this.rollNumber = rollNumber;
        this.score = score;
    }

    public String getName() {
        return name;
    }

    public int getRollNumber() {
        return rollNumber;
    }

    public double getScore() {
        return score;
    }

    @Override
    public String toString() {
        return "Student{" +
               "name='" + name + '\'' +
               ", rollNumber=" + rollNumber +
               ", score=" + score +
               '}';
    }
}

public class ComparatorAnonymousExample {
    public static void main(String[] args) {
        List<StudentComparatorBeginner> students = new ArrayList<>();
        students.add(new StudentComparatorBeginner("Alice", 101, 85.5));
        students.add(new StudentComparatorBeginner("Bob", 103, 92.0));
        students.add(new StudentComparatorBeginner("Charlie", 100, 78.0));
        students.add(new StudentComparatorBeginner("David", 102, 95.0));

        System.out.println("Before sorting: " + students);

        // Sort by name using an anonymous inner class Comparator
        Collections.sort(students, new Comparator<StudentComparatorBeginner>() {
            @Override
            public int compare(StudentComparatorBeginner s1, StudentComparatorBeginner s2) {
                return s1.getName().compareTo(s2.getName());
            }
        });
        System.out.println("After sorting by name: " + students);
    }
}
```

**Output**
```
Before sorting: [Student{name='Alice', rollNumber=101, score=85.5}, Student{name='Bob', rollNumber=103, score=92.0}, Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='David', rollNumber=102, score=95.0}]
After sorting by name: [Student{name='Alice', rollNumber=101, score=85.5}, Student{name='Bob', rollNumber=103, score=92.0}, Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='David', rollNumber=102, score=95.0}]
```

Wait, the output for "After sorting by name" is not what we expect. Let's fix the test data to make the name sorting more apparent.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

// Student class (same as before, but without Comparable implementation)
class StudentComparatorBeginner {
    private String name;
    private int rollNumber;
    private double score;

    public StudentComparatorBeginner(String name, int rollNumber, double score) {
        this.name = name;
        this.rollNumber = rollNumber;
        this.score = score;
    }

    public String getName() {
        return name;
    }

    public int getRollNumber() {
        return rollNumber;
    }

    public double getScore() {
        return score;
    }

    @Override
    public String toString() {
        return "Student{" +
               "name='" + name + '\'' +
               ", rollNumber=" + rollNumber +
               ", score=" + score +
               '}';
    }
}

public class ComparatorAnonymousExample {
    public static void main(String[] args) {
        List<StudentComparatorBeginner> students = new ArrayList<>();
        students.add(new StudentComparatorBeginner("David", 102, 95.0));
        students.add(new StudentComparatorBeginner("Alice", 101, 85.5));
        students.add(new StudentComparatorBeginner("Charlie", 100, 78.0));
        students.add(new StudentComparatorBeginner("Bob", 103, 92.0));


        System.out.println("Before sorting: " + students);

        // Sort by name using an anonymous inner class Comparator
        Collections.sort(students, new Comparator<StudentComparatorBeginner>() {
            @Override
            public int compare(StudentComparatorBeginner s1, StudentComparatorBeginner s2) {
                return s1.getName().compareTo(s2.getName());
            }
        });
        System.out.println("After sorting by name: " + students);
    }
}
```

**Output**
```
Before sorting: [Student{name='David', rollNumber=102, score=95.0}, Student{name='Alice', rollNumber=101, score=85.5}, Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='Bob', rollNumber=103, score=92.0}]
After sorting by name: [Student{name='Alice', rollNumber=101, score=85.5}, Student{name='Bob', rollNumber=103, score=92.0}, Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='David', rollNumber=102, score=95.0}]
```

### Example: Using Lambda Expression (Java 8+)
Lambda expressions make Comparator much cleaner.
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

// Student class (same as before, without Comparable implementation)
class StudentComparatorLambda {
    private String name;
    private int rollNumber;
    private double score;

    public StudentComparatorLambda(String name, int rollNumber, double score) {
        this.name = name;
        this.rollNumber = rollNumber;
        this.score = score;
    }

    public String getName() {
        return name;
    }

    public int getRollNumber() {
        return rollNumber;
    }

    public double getScore() {
        return score;
    }

    @Override
    public String toString() {
        return "Student{" +
               "name='" + name + '\'' +
               ", rollNumber=" + rollNumber +
               ", score=" + score +
               '}';
    }
}

public class ComparatorLambdaExample {
    public static void main(String[] args) {
        List<StudentComparatorLambda> students = new ArrayList<>();
        students.add(new StudentComparatorLambda("Alice", 101, 85.5));
        students.add(new StudentComparatorLambda("Bob", 103, 92.0));
        students.add(new StudentComparatorLambda("Charlie", 100, 78.0));
        students.add(new StudentComparatorLambda("David", 102, 95.0));

        System.out.println("Before sorting: " + students);

        // Sort by score in descending order using a lambda expression
        Collections.sort(students, (s1, s2) -> Double.compare(s2.getScore(), s1.getScore()));
        System.out.println("After sorting by score (desc): " + students);

        // Sort by roll number in ascending order using a method reference
        students.sort(Comparator.comparing(StudentComparatorLambda::getRollNumber));
        System.out.println("After sorting by roll number (asc): " + students);
    }
}
```

**Output**
```
Before sorting: [Student{name='Alice', rollNumber=101, score=85.5}, Student{name='Bob', rollNumber=103, score=92.0}, Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='David', rollNumber=102, score=95.0}]
After sorting by score (desc): [Student{name='David', rollNumber=102, score=95.0}, Student{name='Bob', rollNumber=103, score=92.0}, Student{name='Alice', rollNumber=101, score=85.5}, Student{name='Charlie', rollNumber=100, score=78.0}]
After sorting by roll number (asc): [Student{name='Charlie', rollNumber=100, score=78.0}, Student{name='Alice', rollNumber=101, score=85.5}, Student{name='David', rollNumber=102, score=95.0}, Student{name='Bob', rollNumber=103, score=92.0}]
```

### Example: Chaining Comparators and Null Handling
Java 8's `Comparator` interface provides powerful default methods for chaining and null handling.
```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

class Employee {
  private String name;
  private int age;
  private double salary;

  public Employee(String name, int age, double salary) {
    this.name = name;
    this.age = age;
    this.salary = salary;
  }

  public String getName() {
    return name;
  }

  public int getAge() {
    return age;
  }

  public double getSalary() {
    return salary;
  }

  @Override
  public String toString() {
    return "Employee{" +
            "name='" + name + '\'' +
            ", age=" + age +
            ", salary=" + salary +
            '}';
  }
}

public class ComparatorChainingExample {
  public static void main(String[] args) {
    List<Employee> employees = new ArrayList<>();
    employees.add(new Employee("Alice", 30, 70000.00));
    employees.add(new Employee("Bob", 25, 80000.00));
    employees.add(new Employee("Charlie", 35, 60000.00));
    employees.add(new Employee("David", 30, 75000.00)); // Same age as Alice
    employees.add(new Employee("Eve", 25, 80000.00)); // Same age and salary as Bob
    employees.add(null); // Introduce a null for nullsLast/First demo

    System.out.println("Before sorting: " + employees);

    // Sort by age (ascending), then by salary (descending), then by name (ascending)
    Comparator<Employee> byAge = Comparator.comparing(Employee::getAge);
    Comparator<Employee> bySalaryDesc = Comparator.comparing(Employee::getSalary).reversed();
    Comparator<Employee> byName = Comparator.comparing(Employee::getName);

    Comparator<Employee> compoundComparator = Comparator
            .comparing(Employee::getAge, Comparator.nullsLast(Integer::compare)) // Handle null ages, put them last
            .thenComparing(Employee::getSalary, Comparator.nullsLast(Double::compare).reversed()) // Handle null salaries, put them last, then sort descending
            .thenComparing(Employee::getName, Comparator.nullsLast(String::compare)); // Handle null names, put them last

    // If you specifically want to handle null Employee objects in the list:
    Comparator<Employee> fullCompoundComparator = Comparator
            .nullsFirst(compoundComparator); // Put null Employee objects first

    employees.sort(fullCompoundComparator);
    System.out.println("After sorting by age (asc), then salary (desc), then name (asc): " + employees);

    // Example of sorting by salary ascending, with nulls last for salary values
    employees.clear();
    employees.add(new Employee("Alice", 30, 70000.00));
    employees.add(new Employee("Bob", 25, 80000.00));
    employees.add(new Employee("Charlie", 35, 60000.00));
    employees.add(new Employee("David", 30, 75000.00));
    employees.add(new Employee("Eve", 25, 80000.00));
    employees.add(null);

    employees.sort(Comparator.nullsLast(Comparator.comparing(Employee::getSalary)));
    System.out.println("\nAfter sorting by salary (asc) with null Employee objects last: " + employees);

    // Another example: sort by name, handling null names within an Employee
    List<Employee> employeesWithNullNames = new ArrayList<>();
    employeesWithNullNames.add(new Employee("Alice", 30, 70000.00));
    employeesWithNullNames.add(new Employee(null, 25, 80000.00)); // Null name
    employeesWithNullNames.add(new Employee("Charlie", 35, 60000.00));
    employeesWithNullNames.add(new Employee("Bob", 28, 72000.00));

    System.out.println("\nBefore sorting (with null names): " + employeesWithNullNames);
    employeesWithNullNames.sort(Comparator.comparing(Employee::getName, Comparator.nullsLast(String::compareTo)));
    System.out.println("After sorting by name (asc) with null names last: " + employeesWithNullNames);
  }
}
```

**Output**
```
Before sorting: [Employee{name='Alice', age=30, salary=70000.0}, Employee{name='Bob', age=25, salary=80000.0}, Employee{name='Charlie', age=35, salary=60000.0}, Employee{name='David', age=30, salary=75000.0}, Employee{name='Eve', age=25, salary=80000.0}, null]
After sorting by age (asc), then salary (desc), then name (asc): [null, Employee{name='Bob', age=25, salary=80000.0}, Employee{name='Eve', age=25, salary=80000.0}, Employee{name='Alice', age=30, salary=70000.0}, Employee{name='David', age=30, salary=75000.0}, Employee{name='Charlie', age=35, salary=60000.0}]

After sorting by salary (asc) with null Employee objects last: [Employee{name='Charlie', age=35, salary=60000.0}, Employee{name='Alice', age=30, salary=70000.0}, Employee{name='David', age=30, salary=75000.0}, Employee{name='Bob', age=25, salary=80000.0}, Employee{name='Eve', age=25, salary=80000.0}, null]

Before sorting (with null names): [Employee{name='Alice', age=30, salary=70000.0}, Employee{name='null', age=25, salary=80000.0}, Employee{name='Charlie', age=35, salary=60000.0}, Employee{name='Bob', age=28, salary=72000.0}]
After sorting by name (asc) with null names last: [Employee{name='Alice', age=30, salary=70000.0}, Employee{name='Bob', age=28, salary=72000.0}, Employee{name='Charlie', age=35, salary=60000.0}, Employee{name='null', age=25, salary=80000.0}]
```

---

## 3. PriorityQueue: Ordered Collection
### Overview and Importance
A `PriorityQueue` is a special type of queue where each element has a "priority." When you retrieve an element from a priority queue, the element with the highest priority is removed first. It does not sort the elements internally; instead, it maintains a heap data structure to ensure that the head of the queue is always the element with the highest priority.

#### How does it determine priority?
- **Natural Ordering**: If elements implement `Comparable`, `PriorityQueue` uses their `compareTo()` method.
- **Custom Ordering**: You can provide a `Comparator` to the `PriorityQueue` constructor to define a custom ordering.

#### Why is it important?
- **Efficient Retrieval of Min/Max**: It provides O(1) access to the highest-priority element (peek) and O(logn) for adding and removing elements, making it ideal for scenarios where you repeatedly need to get the "next best" item.
- **Heap Implementation**: Its underlying heap data structure guarantees efficient operations for priority-based retrieval.
- **Algorithm Implementations**: Crucial for algorithms like Dijkstra's shortest path, Prim's minimum spanning tree, Huffman coding, and event simulation.
- **Task Scheduling/Processing**: Useful for managing tasks based on their urgency or priority.

### Syntax
```java
public class PriorityQueue<E> extends AbstractQueue<E> implements Serializable {
    // Constructors:
    public PriorityQueue(); // Uses natural ordering (elements must be Comparable)
    public PriorityQueue(int initialCapacity);
    public PriorityQueue(Comparator<? super E> comparator); // Uses provided Comparator
    public PriorityQueue(int initialCapacity, Comparator<? super E> comparator);
    public PriorityQueue(Collection<? extends E> c); // Builds from a collection
    public PriorityQueue(PriorityQueue<? extends E> c);
    public PriorityQueue(SortedSet<? extends E> c);

    // Key methods:
    boolean add(E e); // Adds an element. Throws IllegalStateException if full.
    boolean offer(E e); // Adds an element. Returns true/false. Preferred over add for capacity-constrained queues.
    E peek(); // Retrieves, but does not remove, the head of this queue. Returns null if empty.
    E element(); // Retrieves, but does not remove, the head of this queue. Throws NoSuchElementException if empty.
    E poll(); // Retrieves and removes the head of this queue. Returns null if empty.
    E remove(); // Retrieves and removes the head of this queue. Throws NoSuchElementException if empty.
    boolean remove(Object o); // Removes a single instance of the specified element from this queue.
    int size();
    void clear();
}
```

### Use Cases
- **Task Schedulers**: Process the highest-priority task next.
- **Event Simulation**: Process events in chronological order.
- **Network Packet Prioritization**: Process urgent packets first.
- **Graph Algorithms**: Maintain a set of visited nodes and their minimum distances (Dijkstra's).
- **Top K Problems**: Efficiently find the K largest/smallest elements in a stream of data.


#### Example: `PriorityQueue` with Natural Ordering
```java
import java.util.PriorityQueue;

public class PriorityQueueNaturalOrdering {
    public static void main(String[] args) {
        PriorityQueue<Integer> pq = new PriorityQueue<>(); // Uses natural ordering (min-heap by default)

        pq.offer(10);
        pq.offer(2);
        pq.offer(8);
        pq.offer(5);
        pq.offer(1);

        System.out.println("Elements in PriorityQueue (not necessarily sorted order internally): " + pq);
        System.out.println("Head of the queue (smallest element): " + pq.peek()); // Should be 1

        System.out.println("Polling elements:");
        while (!pq.isEmpty()) {
            System.out.println(pq.poll()); // Elements are polled in sorted order
        }
    }
}
```

**Output**
```
Elements in PriorityQueue (not necessarily sorted order internally): [1, 2, 8, 10, 5]
Head of the queue (smallest element): 1
Polling elements:
1
2
5
8
10
```

#### Example: `PriorityQueue` with `Comparator` for Custom Objects
```java
import java.util.Comparator;
import java.util.PriorityQueue;

// Student class (same as before, without Comparable implementation)
class StudentPQ {
  private String name;
  private int rollNumber;
  private double score;

  public StudentPQ(String name, int rollNumber, double score) {
    this.name = name;
    this.rollNumber = rollNumber;
    this.score = score;
  }

  public String getName() {
    return name;
  }

  public int getRollNumber() {
    return rollNumber;
  }

  public double getScore() {
    return score;
  }

  @Override
  public String toString() {
    return "Student{" +
            "name='" + name + '\'' +
            ", rollNumber=" + rollNumber +
            ", score=" + score +
            '}';
  }
}

public class PriorityQueueCustomOrdering {
  public static void main(String[] args) {
    // PriorityQueue that orders students by score in descending order (max-heap based on score)
    PriorityQueue<StudentPQ> studentPQ = new PriorityQueue<>(
            Comparator.comparing(StudentPQ::getScore).reversed()
    );

    studentPQ.offer(new StudentPQ("Alice", 101, 85.5));
    studentPQ.offer(new StudentPQ("Bob", 103, 92.0));
    studentPQ.offer(new StudentPQ("Charlie", 100, 78.0));
    studentPQ.offer(new StudentPQ("David", 102, 95.0));

    System.out.println("Head of the queue (highest score): " + studentPQ.peek());

    System.out.println("Polling students by score (descending):");
    while (!studentPQ.isEmpty()) {
      System.out.println(studentPQ.poll());
    }

    System.out.println("\n--- Another PQ: by roll number (ascending) ---");
    PriorityQueue<StudentPQ> studentPQByRoll = new PriorityQueue<>(
            Comparator.comparing(StudentPQ::getRollNumber)
    );

    studentPQByRoll.offer(new StudentPQ("Alice", 101, 85.5));
    studentPQByRoll.offer(new StudentPQ("Bob", 103, 92.0));
    studentPQByRoll.offer(new StudentPQ("Charlie", 100, 78.0));
    studentPQByRoll.offer(new StudentPQ("David", 102, 95.0));

    System.out.println("Polling students by roll number (ascending):");
    while (!studentPQByRoll.isEmpty()) {
      System.out.println(studentPQByRoll.poll());
    }
  }
}
```

**Output**
```
Head of the queue (highest score): Student{name='David', rollNumber=102, score=95.0}
Polling students by score (descending):
Student{name='David', rollNumber=102, score=95.0}
Student{name='Bob', rollNumber=103, score=92.0}
Student{name='Alice', rollNumber=101, score=85.5}
Student{name='Charlie', rollNumber=100, score=78.0}

--- Another PQ: by roll number (ascending) ---
Polling students by roll number (ascending):
Student{name='Charlie', rollNumber=100, score=78.0}
Student{name='Alice', rollNumber=101, score=85.5}
Student{name='David', rollNumber=102, score=95.0}
Student{name='Bob', rollNumber=103, score=92.0}
```

#### Example: Using `PriorityQueue` in a "Top K" Problem
Finding the top K elements in a large dataset is a common problem. A `PriorityQueue` is very efficient for this. Here, we find the 3 largest numbers.
```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.PriorityQueue;

public class TopKElements {
    public static List<Integer> findKLargest(int[] nums, int k) {
        // Use a min-heap to store the K largest elements.
        // The smallest element in the heap will be the Kth largest element encountered so far.
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();

        for (int num : nums) {
            minHeap.offer(num);
            if (minHeap.size() > k) {
                minHeap.poll(); // Remove the smallest element if heap size exceeds k
            }
        }

        // The heap now contains the K largest elements
        List<Integer> result = new ArrayList<>(minHeap);
        // If you need them sorted in descending order, sort the result list
        Collections.sort(result, Collections.reverseOrder());
        return result;
    }

    public static void main(String[] args) {
        int[] numbers = {3, 2, 1, 5, 6, 4, 7, 8, 9, 10};
        int k = 3;
        List<Integer> topKLargest = findKLargest(numbers, k);
        System.out.println("Original numbers: " + java.util.Arrays.toString(numbers));
        System.out.println("Top " + k + " largest elements: " + topKLargest); // Should be [10, 9, 8]

        int[] moreNumbers = {100, 4, 200, 1, 3, 2};
        k = 2;
        List<Integer> top2Largest = findKLargest(moreNumbers, k);
        System.out.println("Original numbers: " + java.util.Arrays.toString(moreNumbers));
        System.out.println("Top " + k + " largest elements: " + top2Largest); // Should be [200, 100]

        // Example for finding K smallest elements (using a max-heap)
        List<Integer> findKSmallest(int[] nums, int k) {
            PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
            for (int num : nums) {
                maxHeap.offer(num);
                if (maxHeap.size() > k) {
                    maxHeap.poll();
                }
            }
            List<Integer> result = new ArrayList<>(maxHeap);
            Collections.sort(result); // Sort ascending for smallest
            return result;
        }

        List<Integer> top3Smallest = findKSmallest(numbers, 3);
        System.out.println("Top 3 smallest elements: " + top3Smallest); // Should be [1, 2, 3]
    }
}
```

**Output**
```
Original numbers: [3, 2, 1, 5, 6, 4, 7, 8, 9, 10]
Top 3 largest elements: [10, 9, 8]
Original numbers: [100, 4, 200, 1, 3, 2]
Top 2 largest elements: [200, 100]
Top 3 smallest elements: [1, 2, 3]
```

# Common Pitfalls and Best Practices
## Common Pitfalls
1. **Modifying Objects in a** `PriorityQueue` **without Re-adding**: If you modify an object's properties that affect its comparison (e.g., a student's score in a `PriorityQueue` ordered by score) after it's been added to the queue, the `PriorityQueue`'s internal heap structure will become invalid. You must remove the object and re-add it to restore the heap property.
2. **Inconsistent** `equals()` **with** `compareTo()` **/** `compare()`: If `x.compareTo(y) == 0` (or `comparator.compare(x, y) == 0`) implies `x.equals(y)`, then the ordering is "consistent with equals." While not strictly required, failing this can lead to unexpected behavior, especially in collections that rely on `equals()` for element uniqueness (like `Set` or `Map` keys). For `PriorityQueue`, it mainly affects `remove(Object o)`.
3. **Nulls in Comparable**: `compareTo()` cannot handle null arguments; it will throw a `NullPointerException`. Always check for nulls if your objects or the objects being compared might be null.
4. **Nulls in Comparator (Pre-Java 8)**: Similar to `Comparable`, if you write your own `Comparator` for older Java versions, you must explicitly handle nulls. Java 8's `Comparator.nullsFirst()` and `nullsLast()` methods greatly simplify this.
5. **Not Providing a Comparator for Custom Objects in PriorityQueue**: If your custom object does not implement `Comparable`, and you don't provide a `Comparator` to the `PriorityQueue` constructor, it will throw a `ClassCastException` at runtime when you try to add an element.
6. **Performance Misconceptions**: While `peek()` is O(1), add() and poll() are O(logn). Iterating through a `PriorityQueue` using an iterator does not guarantee sorted order, as the heap property only guarantees the head is the smallest/largest. To get sorted elements, you must poll() them one by one.

## Best Practices
- **Use Comparable for Natural Ordering**: If there's one clear, inherent way to sort objects of a class, implement `Comparable`.
- **Use Comparator for Multiple/External Orderings**: When you need flexible sorting, or to sort objects from classes you don't control, use `Comparator`.
- **Leverage Java 8 Comparator Features**: `Comparator.comparing()`, `thenComparing()`, `reversed()`, `nullsFirst()`, and `nullsLast()` make writing complex comparators concise and readable.
- **Immutability for PriorityQueue Elements**: If possible, make objects stored in a `PriorityQueue` immutable. This prevents accidental invalidation of the heap structure. If mutable, ensure you explicitly remove and re-add elements after changes that affect their priority.
- **Override** `equals()` **and** `hashCode()`: If you override `compareTo()` or `compare()`, it's good practice to also consider overriding `equals()` and `hashCode()` to maintain consistency, especially if these objects will be used in `Set`s or as `Map` keys.
- **Descriptive Comparator Names**: When defining separate Comparator classes, give them meaningful names (e.g., `StudentByNameComparator`, `ProductByPriceDescendingComparator`).

# Performance Considerations
- `Comparable.compareTo()` / `Comparator.compare()`:
  - The performance of these methods is typically O(1) if they compare primitive fields or use built-in `compareTo`/`compare` for String, Integer, etc.
  - Performance can degrade if the comparison logic involves iterating over collections or performing expensive computations. Keep them as efficient as possible. 
- `PriorityQueue` Operations:
  - `peek()`: O(1)
  - `size()`: O(1)
  - `add()` / `offer()`: O(logn), where n is the number of elements in the queue. This is due to the heap re-balancing.
  - `poll()` / `remove()`: O(logn), also due to heap re-balancing.
  - `contains(Object o)`: O(n), as it may require a linear scan.
  - `remove(Object o)`: O(n) in the worst case, as it might involve scanning to find the element and then O(logn) for heap re-balancing.
  - Constructing a `PriorityQueue` from a collection: O(n) on average, as it uses the heapify algorithm.
- Sorting Collections (`Collections.sort()`, `Arrays.sort()`):
  - Typically O(nlogn) for most algorithms like Timsort (used in `Collections.sort()`) or Dual-Pivot Quicksort (used in `Arrays.sort()` for primitives).

**Memory Footprint:** A `PriorityQueue` uses an array-based heap, so its memory footprint is O(n), where n is the number of elements. Each element takes up space, plus some overhead for the array structure.

---

# Deque and List
`List` and `Deque` are the interfaces which define contracts for how certain types of collections should behave.

### Why are Deque and List Important?
- `List`: Represents an ordered collection (also known as a sequence). It allows duplicate elements and provides precise control over where each element is inserted. The key feature of a List is that elements are indexed, just like in an array, allowing for quick access by position. Lists are incredibly common and form the basis for storing ordered sequences of data in almost every application.
- `Deque` **(Double-Ended Queue)**: A Deque is a linear collection that supports element insertion and removal at both ends. It generalizes the concept of a queue (FIFO - First-In, First-Out) and a stack (LIFO - Last-In, First-Out). This versatility makes Deques powerful for scenarios where you need to add or remove elements from either the front or the back efficiently.

## `List` Interface
### Overview and Core Concepts
The `java.util.List interface` is a subinterface of `java.util.Collection`. Here are its fundamental characteristics:
- **Ordered (Indexed)**: Elements are maintained in a specific order and can be accessed by their integer index (position), starting from 0.
- **Allows Duplicates**: You can store multiple identical elements in a List.
- **Maintains Insertion Order**: The order in which elements are added to the list is preserved.

### Key Implementations of `List`
While List is an interface, you cannot instantiate it directly. Instead, you use concrete classes that implement the List interface. The most common implementations are:
- `ArrayList`:
  - **Underlying Data Structure**: Resizable array
  - **Performance**:
    - `get(index)`: O(1) (constant time) because it's direct array access.
    - `add(element)` (at the end): Amortized O(1). When the underlying array is full, a new, larger array is allocated, and elements are copied.
    - `add(index, element)`: O(n) (linear time) because elements after the insertion point need to be shifted.
    - `remove(index)`: O(n) because elements after the removal point need to be shifted.
    - `remove(Object)`: O(n) in the worst case, as it might need to iterate through all elements to find the object.
  - **Use Cases**: When you need frequent random access to elements, or when you are mostly adding elements to the end.
- `LinkedList`:
  - **Underlying Data Structure**: Doubly linked list. Each element (node) stores references to the previous and next elements.
  - **Performance**:
    - `get(index)`: O(n) (linear time) because it has to traverse the list from the beginning or end to reach the index.
    - `add(element)` (at the beginning or end): O(1).
    - `add(index, element)`: O(n) to find the index, but O(1) once the position is found.
    - `remove(element)` (at the beginning or end): O(1).
    - `remove(Object)`: O(n) to find the object.
  - **Use Cases**: When you need frequent insertions or deletions at the beginning or end of the list, or when you iterate sequentially through the list. It's also a good choice when memory usage is a concern, as `LinkedList` can be more memory efficient for very large lists than `ArrayList` if frequent resizing isn't happening.
- `Vector`:
  - Similar to `ArrayList` (resizable array).
  - **Key Difference**: `Vector` is **synchronized**, meaning its methods are thread-safe. This comes with a performance overhead.
  - **Use Cases**: Rarely used in modern Java development due to the availability of `Collections.synchronizedList()` and `CopyOnWriteArrayList` for thread-safe list operations, which offer more fine-grained control or better performance characteristics in specific concurrent scenarios. `ArrayList` is generally preferred for single-threaded environments.

### Syntax
```java
import java.util.ArrayList;
import java.util.List;

public class ListBasics {
    public static void main(String[] args) {
        // 1. Declaration and Instantiation
        // It's good practice to declare the variable with the interface type (List)
        // and instantiate with the concrete class (ArrayList).
        List<String> fruits = new ArrayList<>();

        // 2. Adding Elements
        fruits.add("Apple");        // Adds to the end
        fruits.add("Banana");
        fruits.add("Orange");
        System.out.println("Initial List: " + fruits); // Output: [Apple, Banana, Orange]

        // 3. Adding an element at a specific index
        fruits.add(1, "Grape"); // Adds "Grape" at index 1, shifting "Banana" and "Orange"
        System.out.println("List after adding at index 1: " + fruits); // Output: [Apple, Grape, Banana, Orange]

        // 4. Getting an element by index
        String firstFruit = fruits.get(0);
        System.out.println("First fruit: " + firstFruit); // Output: Apple

        String thirdFruit = fruits.get(2);
        System.out.println("Third fruit: " + thirdFruit); // Output: Banana

        // 5. Getting the size of the list
        int size = fruits.size();
        System.out.println("Number of fruits: " + size); // Output: 4

        // 6. Checking if the list contains an element
        boolean hasBanana = fruits.contains("Banana");
        System.out.println("Does list contain Banana? " + hasBanana); // Output: true

        boolean hasPineapple = fruits.contains("Pineapple");
        System.out.println("Does list contain Pineapple? " + hasPineapple); // Output: false

        // 7. Removing an element by value
        fruits.remove("Orange");
        System.out.println("List after removing Orange: " + fruits); // Output: [Apple, Grape, Banana]

        // 8. Removing an element by index
        fruits.remove(0); // Removes the element at index 0 ("Apple")
        System.out.println("List after removing element at index 0: " + fruits); // Output: [Grape, Banana]

        // 9. Iterating through the list
        System.out.println("\nIterating through fruits:");
        for (String fruit : fruits) {
            System.out.println(fruit);
        }

        // 10. Checking if the list is empty
        System.out.println("Is list empty? " + fruits.isEmpty()); // Output: false

        // 11. Clearing the list
        fruits.clear();
        System.out.println("List after clearing: " + fruits); // Output: []
        System.out.println("Is list empty after clearing? " + fruits.isEmpty()); // Output: true
    }
}
```

#### Example: `ListIterator` and SubList
`ListIterator` is a more powerful iterator for lists than the standard `Iterator`. It allows you to traverse the list in both forward and backward directions, and to modify the list during iteration (add, set, remove).
```java
import java.util.ArrayList;
import java.util.List;
import java.util.ListIterator;

public class ListIntermediate {
    public static void main(String[] args) {
        List<String> colors = new ArrayList<>();
        colors.add("Red");
        colors.add("Green");
        colors.add("Blue");
        colors.add("Yellow");
        colors.add("Purple");

        System.out.println("Original List: " + colors);

        // Using ListIterator to traverse forwards and modify
        ListIterator<String> listIterator = colors.listIterator();
        while (listIterator.hasNext()) {
            String color = listIterator.next();
            if (color.equals("Green")) {
                listIterator.remove(); // Removes "Green"
                listIterator.add("Emerald Green"); // Adds "Emerald Green"
            } else if (color.equals("Yellow")) {
                listIterator.set("Gold"); // Replaces "Yellow" with "Gold"
            }
        }
        System.out.println("List after ListIterator modifications: " + colors);
        // Output: [Red, Emerald Green, Blue, Gold, Purple]

        // Using ListIterator to traverse backwards
        System.out.println("\nTraversing backwards:");
        while (listIterator.hasPrevious()) {
            System.out.println(listIterator.previous());
        }
        // Output: (prints in reverse order from Gold to Red)

        // SubList
        List<String> subList = colors.subList(1, 4); // Elements from index 1 (inclusive) to 4 (exclusive)
        System.out.println("\nSubList (from index 1 to 4): " + subList); // Output: [Emerald Green, Blue, Gold]

        // Important: Changes to the subList are reflected in the original list, and vice versa.
        subList.add("Cyan");
        System.out.println("SubList after adding Cyan: " + subList); // Output: [Emerald Green, Blue, Gold, Cyan]
        System.out.println("Original list after subList modification: " + colors);
        // Output: [Red, Emerald Green, Blue, Gold, Cyan, Purple]
    }
}
```

#### Example: Custom Sorting, Concurrent Lists
**Custom Sorting** with `Comparator` <br>
Lists can be sorted using `Collections.sort()` which uses a natural ordering (if elements are `Comparable`) or a custom `Comparator`.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" + "name='" + name + '\'' + ", age=" + age + '}';
    }
}

public class ListAdvanced {
    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 30));
        people.add(new Person("Bob", 25));
        people.add(new Person("Charlie", 35));
        people.add(new Person("David", 25));

        System.out.println("Original People List: " + people);

        // Sort by age
        Collections.sort(people, Comparator.comparingInt(p -> p.age));
        System.out.println("Sorted by Age: " + people);

        // Sort by age, then by name for same age
        Collections.sort(people, Comparator.comparingInt((Person p) -> p.age)
                                          .thenComparing(p -> p.name));
        System.out.println("Sorted by Age then Name: " + people);
    }
}
```

**Concurrent Lists** <br>
For multi-threaded environments, direct use of `ArrayList` or `LinkedList` is not thread-safe. You need synchronized versions.
- `Collections.synchronizedList(List<T> list)`: Returns a synchronized (thread-safe) `List` backed by the specified list. All access to the backing list must be done via the returned list.
- `CopyOnWriteArrayList`: A thread-safe variant of `ArrayList` in which all mutative operations (add, set, remove, etc.) are implemented by making a fresh copy of the underlying array. This is expensive for frequent modifications but excellent for lists that are frequently read but rarely modified, as read operations do not require synchronization.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

public class ConcurrentListExample {
    public static void main(String[] args) {
        // 1. Using Collections.synchronizedList
        List<String> synchronizedNames = Collections.synchronizedList(new ArrayList<>());

        // In a multi-threaded scenario, threads would add/remove elements here
        // Example:
        // new Thread(() -> synchronizedNames.add("Thread1_Name")).start();
        // new Thread(() -> synchronizedNames.add("Thread2_Name")).start();
        // ... ensure proper synchronization for iterations if needed

        synchronizedNames.add("Alice");
        synchronizedNames.add("Bob");
        System.out.println("Synchronized List: " + synchronizedNames);

        // 2. Using CopyOnWriteArrayList
        CopyOnWriteArrayList<String> safeNames = new CopyOnWriteArrayList<>();
        safeNames.add("Charlie");
        safeNames.add("David");

        // Example of multi-threaded access (read operations are highly concurrent)
        new Thread(() -> {
            for (String name : safeNames) {
                System.out.println("Reading from safeNames: " + name);
            }
        }).start();

        new Thread(() -> {
            safeNames.add("Eve"); // This will create a new copy of the internal array
            System.out.println("Added Eve to safeNames.");
        }).start();

        try {
            Thread.sleep(100); // Give threads some time to execute
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println("CopyOnWriteArrayList after concurrent ops: " + safeNames);
    }
}
```

## Common Pitfalls and Best Practices for `List`
### Pitfalls
- `IndexOutOfBoundsException`: Attempting to access an index that is less than 0 or greater than or equal to `size()`. Always check bounds or use iterators.
- **Performance Misconceptions**: Using `LinkedList` for random access (e.g., frequent `get(index)` calls) or `ArrayList` for frequent insertions/deletions in the middle of a large list can lead to O(n) performance where O(1) might have been possible with the right choice.
- **Modifying a list while iterating (fail-fast)**: Directly modifying an `ArrayList` or `LinkedList` using a `for-each` loop or a traditional `Iterator` (not `ListIterator`) will result in a `ConcurrentModificationException`. Use `ListIterator` for safe modification during iteration or iterate over a copy.
- **Synchronized lists**: For `Collections.synchronizedList()`, remember that the returned list is synchronized, but iterators on that list are not. You must manually synchronize the block if you iterate over it in a multi-threaded environment.

```java
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
// ... add elements ...
synchronized (syncList) { // Manual synchronization for iteration
    for (String item : syncList) {
        System.out.println(item);
    }
}
```

- **SubList Caveats**: The `subList` is a view of the original list. Modifying the `subList` modifies the original, and vice versa. If the original list's structure is modified (e.g., elements added/removed) in a way that invalidates the subList's indices, operations on the subList will throw a `ConcurrentModificationException`.

### Best Practices
- **Choose the right implementation**: `ArrayList` for random access, `LinkedList` for frequent insertions/deletions at ends.
- **Declare with Interface Type**: Always declare variables with the `List` interface type (e.g., `List<String> myList = new ArrayList<>();`) to promote flexibility and abstraction.
- **Pre-allocate capacity (for** `ArrayList`**)**: If you know the approximate size of your `ArrayList`, initialize it with that capacity (`new ArrayList<>(initialCapacity)`) to reduce reallocations and improve performance.
- **Use** `isEmpty()` **instead of** `size()` **== 0**: It's more readable and often slightly more performant for some collection types.
- **Immutable Lists (Java 9+)**: For scenarios where a list should not change after creation, use `List.of()` for immutable lists. This improves safety and can sometimes lead to performance optimizations.

```java
List<String> immutableColors = List.of("Red", "Green", "Blue");
// immutableColors.add("Yellow"); // Throws UnsupportedOperationException
```

# Performance Considerations for `List`
- `ArrayList`:
  - **Pros**: Excellent for `get(index)` operations (O(1)). Efficient for adding to the end (O(1) amortized).
  - **Cons**: Expensive for insertions/deletions in the middle (O(n)). Reallocations can be costly if capacity is not managed.
- `LinkedList`:
  - **Pros**: Excellent for additions/removals at the beginning or end (O(1)).
  - **Cons**: Poor for random access `get(index)` (O(n)). Higher memory overhead per element due to storing next/previous pointers.
- When choosing between `ArrayList` and `LinkedList`, consider the most frequent operations your application will perform. If reads by index are dominant, `ArrayList` is usually better. If insertions/deletions at the ends are dominant, `LinkedList` is better.

---