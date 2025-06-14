# Quick Revision

## Java Basics
### Explanation / Key Concepts
- **Data types**: Define the type of data a variable can hold.
  - **Primitive types**: `byte`, `short`, `int`, `long`, `float`, `double`, `boolean`, `char`. Directly store values.
  - **Non-primitive (Reference) types**: `String`, Arrays, Classes, Interfaces. Store references to objects.
- **Variables**: Named memory locations to store data. Must be declared with a data type.
- **Operators**: Symbols that perform operations on variables and values.
  - **Arithmetic**: `+`, `-`, `*`, `/`, `%`
  - **Assignment**: `=`, `+=`, `-=`, etc.
  - **Comparison**: `==`, `!=`, `<`, `>`, `<=`, `>=` (return boolean)
  - **Logical**: `&&` (AND), `||` (OR), `!` (NOT)
  - **Bitwise**: `&`, `|`, `^`, `~`, `<<`, `>>`, `>>>`
  - **Ternary**: `condition ? valueIfTrue : valueIfFalse`
- **Control Flow**: Dictates the order in which statements are executed.
  - **Conditional**: `if`, `else if`, `else`, `switch`
  - **Looping**: `for`, `while`, `do-while`, `for-each`
- **Input/Output**: Reading data from and writing data to various sources (console, files, network).
  - `System.out.println()` for output.
  - `Scanner` class for console input.
- **Type Casting**: Converting one data type to another.
  - **Widening (Implicit)**: Smaller type to larger type (e.g., `int` to `long`). Happens automatically.
  - **Narrowing (Explicit)**: Larger type to smaller type (e.g., `double` to `int`). Requires a cast operator and can lead to data loss.
- **Java Naming Conventions and Structure**:
  - **Classes**: `PascalCase` (e.g., `MyClass`).
  - **Methods/Variables**: `camelCase` (e.g., `myMethod`, `myVariable`).
  - **Constants**: `UPPER_SNAKE_CASE` (e.g., `MAX_VALUE`).
  - **Packages**: `lowercase.dotted.notation` (e.g., `com.example.app`).
  - Basic structure: `package` declaration, `import` statements, `class` definition, `main` method.

#### Example Code Snippet
```java
public class BasicConcepts {
    public static void main(String[] args) {
        // Data types and variables
        int age = 30;
        double salary = 75000.50;
        boolean isActive = true;
        String name = "Alice";

        // Operators
        int sum = age + 5; // Arithmetic
        boolean isAdult = age >= 18; // Comparison

        // Control Flow - if-else
        if (isAdult) {
            System.out.println(name + " is an adult.");
        } else {
            System.out.println(name + " is not an adult.");
        }

        // Control Flow - for loop
        for (int i = 0; i < 3; i++) {
            System.out.println("Loop iteration: " + i);
        }

        // Input (using Scanner)
        // Scanner scanner = new Scanner(System.in);
        // System.out.print("Enter your city: ");
        // String city = scanner.nextLine();
        // System.out.println("You live in: " + city);
        // scanner.close();

        // Type casting
        double myDouble = 9.78;
        int myInt = (int) myDouble; // Explicit narrowing cast
        System.out.println("Original double: " + myDouble + ", Cast to int: " + myInt);
    }
}
```

#### Common Mistakes / Tips
- **Forgetting** `break` in swi`tch: Leads to "fall-through" behavior.
- **Mixing** `==` with `.equals()` for object comparison: `==` compares references, .equals() compares content (if overridden).
- **Integer division**: `5 / 2` results in `2`, not `2.5`. Use `double` for precision.
- **Naming conventions**: Stick to them for readability and maintainability.
- **Resource management**: Close `Scanner` objects (or use try-with-resources later).

---

## Object-Oriented Programming
### Explanation / Key Concepts
OOP is a programming paradigm based on the concept of "objects," which can contain data and code.

- **Classes and Objects**:
  - **Class**: A blueprint or template for creating objects. Defines properties (fields/attributes) and behaviors (methods).
  - **Object**: An instance of a class. A real-world entity created from the class blueprint.
- **Four Pillars of OOP**:
  - **Encapsulation**: Bundling data (attributes) and methods that operate on the data within a single unit (class). Hiding internal implementation details and exposing only necessary interfaces. Achieved using access modifiers (private, public, etc.) and getter/setter methods.
  - **Inheritance**: A mechanism where one class (subclass/child class) acquires the properties and behaviors of another class (superclass/parent class). Promotes code reusability. extends keyword.
  - **Polymorphism**: "Many forms." The ability of an object to take on many forms.
    - **Compile-time Polymorphism (Method Overloading)**: Multiple methods in the same class with the same name but different parameters (number, type, or order).
    - **Runtime Polymorphism (Method Overriding)**: A subclass provides a specific implementation for a method that is already defined in its superclass. Achieved through inheritance and dynamic method dispatch.
  - **Abstraction**: Hiding complex implementation details and showing only the essential features of an object. Achieved using `abstract` classes and `interfaces`.
    - **Abstract Class**: Cannot be instantiated directly. Can have abstract (no body) and concrete methods. A class extending an abstract class must implement all abstract methods or be declared abstract itself.
    - **Interface**: A contract. Defines a set of abstract methods that a class must implement. Supports multiple inheritance of type. Since Java 8, interfaces can have `default` and `static` methods.
- **Access Modifiers**: Control the visibility and accessibility of classes, fields, constructors, and methods.
  - `public`: Accessible from anywhere.
  - `protected`: Accessible within the same package and by subclasses.
  - `default` (no keyword): Accessible only within the same package.
  - `private`: Accessible only within the same class.
- `this` **and** `super`:
  - `this`: Refers to the current object. Used to differentiate between instance variables and local variables (e.g., in constructors/setters) or to call another constructor of the same class (`this()`).
  - `super`: Refers to the immediate parent class object. Used to call the parent class's constructor (`super()`) or to access parent class's methods/fields (`super.method()`, `super.field`).
- **Method Overriding vs Overloading**:
  - **Overriding**: Same method signature (name and parameters) in parent and child class. Runtime polymorphism.
  - **Overloading**: Same method name but different parameter list in the same class. Compile-time polymorphism.

#### Example Code Snippet
```java
// Encapsulation
class BankAccount {
    private String accountNumber;
    private double balance;

    public BankAccount(String accountNumber, double initialBalance) {
        this.accountNumber = accountNumber;
        this.balance = initialBalance;
    }

    public String getAccountNumber() {
        return accountNumber;
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        if (amount > 0) {
            this.balance += amount;
            System.out.println("Deposited: " + amount + ", New balance: " + balance);
        }
    }

    public void withdraw(double amount) {
        if (amount > 0 && this.balance >= amount) {
            this.balance -= amount;
            System.out.println("Withdrew: " + amount + ", New balance: " + balance);
        } else {
            System.out.println("Insufficient balance or invalid amount.");
        }
    }
}

// Inheritance and Polymorphism (Method Overriding)
class Animal {
    void makeSound() {
        System.out.println("Animal makes a sound");
    }
}

class Dog extends Animal {
    @Override // Annotation for clarity and compiler check
    void makeSound() {
        System.out.println("Dog barks");
    }

    // Method Overloading
    void eat() {
        System.out.println("Dog eats food.");
    }

    void eat(String foodType) {
        System.out.println("Dog eats " + foodType + ".");
    }
}

// Abstraction (Interface)
interface Drawable {
    void draw(); // implicitly public abstract
    default void resize() { // Default method in interface (Java 8+)
        System.out.println("Resizing drawable object.");
    }
}

class Circle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing a circle.");
    }
}

public class OOPSConcepts {
    public static void main(String[] args) {
        // Encapsulation example
        BankAccount account = new BankAccount("12345", 1000);
        account.deposit(200);
        account.withdraw(500);
        System.out.println("Account Number: " + account.getAccountNumber());

        // Inheritance and Polymorphism example
        Animal myAnimal = new Animal();
        Animal myDog = new Dog(); // Runtime polymorphism

        myAnimal.makeSound(); // Output: Animal makes a sound
        myDog.makeSound();    // Output: Dog barks (Overridden method)

        Dog specificDog = (Dog) myDog; // Downcasting
        specificDog.eat();
        specificDog.eat("bones");

        // Abstraction example
        Drawable myCircle = new Circle();
        myCircle.draw();
        myCircle.resize();
    }
}
```

#### Common Mistakes / Tips
- **Not understanding the difference between** `interface` **and** `abstract class`: A common interview trap.
- **Confusing method overloading and overriding**: Remember "OVerloading - OVerdifferent parameters," "OVerRiding - OVerparent implementation."
- **Forgetting** `super()` **or** `this()` **call**: If you don't explicitly call `super()`, the no-arg constructor of the parent is implicitly called.
- **Violating encapsulation**: Directly exposing fields without getters/setters unless absolutely necessary.
- **Over-engineering with inheritance**: Favor composition over inheritance where appropriate to avoid "diamond problem" or rigid hierarchies

---

## Core Java APIs
### Explanation / Key Concepts
- `java.lang`: Fundamental classes, automatically imported.
  - `Object`: Root of the class hierarchy.
  - `String`: Immutable sequence of characters.
  - `System`: Provides standard input/output streams.
  - `Math`: Utility class for mathematical operations.
  - `Wrapper Classes`: (e.g., `Integer`, `Double`, `Boolean`) Objects that "wrap" primitive values.
- `java.util`: Utility classes, data structures, and algorithms
  - **Collections Framework**: `List`, `Set`, `Map`, `Queue` interfaces and their implementations
  - `Scanner`: For input
  - `Date`, `Calendar`: Old date/time API (mostly susperseded by `java.time`)
  - `Random`: For generating random numbers
- `java.time` **(Java 8+)**: Modern Date and Time API
  - `LocalDate`, `LocalTime`, `LocalDateTime`: Date, time, and combined date-time without time-zone.
  - `ZonedDateTime`: Date and time with time zone.
  - `Duration`, `Period`: For measuring time difference
  - `DateTimeFormatter`: For parsing and formatting dates/times.
- `java.math`:
  - `BigInteger`: For arbitrary-precision integer arithmetic
  - `BigDecimal`: For arbitrary-precision signed decimal numbers, ideal for financial calculations to avoid floating-point inaccuracies.
- **String Handling and Immutability**:
  - `String` objects are immutable: once created, their content cannot be changed. Any operation that appears to modify a `String` actually creates a new `String` object.
  - `StringBuffer`: Mutable, thread-safe.
  - `StringBuilder`: Mutable, not thread-safe (faster for single-threaded environments).
- **Wrapper Classes and Autoboxing/Unboxing**:
  - **Wrapper Classes**: Provide object representations for primitive data types. Needed for collections (which store objects) and for null values.
  - **Autoboxing**: Automatic conversion of a primitive type to its corresponding wrapper class object (e.g., `int` to `Integer`).
  - **Unboxing**: Automatic conversion of a wrapper class object to its corresponding primitive type (e.g., `Integer` to `int`).

#### Example Code Snippet
```java
import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class CoreAPIs {
    public static void main(String[] args) {
        // String Immutability
        String s1 = "Hello";
        String s2 = s1; // s2 points to the same object as s1
        s1 = s1 + " World"; // s1 now points to a new String object
        System.out.println("s1: " + s1); // Hello World
        System.out.println("s2: " + s2); // Hello (original object)

        // StringBuilder for mutability
        StringBuilder sb = new StringBuilder("Java");
        sb.append(" Programming");
        System.out.println("StringBuilder: " + sb); // Java Programming

        // Wrapper Classes, Autoboxing/Unboxing
        Integer num1 = 100; // Autoboxing: int to Integer
        int num2 = num1;    // Unboxing: Integer to int
        System.out.println("Autoboxing/Unboxing: " + (num1 + num2));

        // java.time API
        LocalDate today = LocalDate.now();
        System.out.println("Today's date: " + today);

        LocalDateTime now = LocalDateTime.now();
        System.out.println("Current date and time: " + now.format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));

        // BigDecimal for precision
        BigDecimal price = new BigDecimal("19.99");
        BigDecimal quantity = new BigDecimal("3");
        BigDecimal total = price.multiply(quantity);
        System.out.println("Total cost: " + total); // 59.97 (precise)
    }
}
```

#### Common Mistakes / Tips
- **Modifying** `String` **in loops**: Repeated string concatenations using `+` in loops create many intermediate String objects, leading to performance issues. Use `StringBuilder` or `StringBuffer` instead.
- **Using** `float`/`double` **for monetary calculations**: Leads to precision errors. Always use BigDecimal.
- **Ignoring null checks with Wrapper classes**: Unboxing a `null` wrapper can lead to NullPointerException.
- **Old Date/Time API**: Avoid `java.util.Date` and `java.util.Calendar` for new code. Stick to java.time.
- `String.intern()`: Useful to know about for optimizing string memory usage, though less common in daily coding

---

## Exception Handling
### Explanation / Key Concepts
Exception handling is a mechanism to deal with runtime errors, allowing the program to continue executing instead of crashing.
- `try-catch-finally`:
  - `try` **block**: Contains the code that might throw an exception
  - `catch` **block**: Catches and handles a specific type of exception thrown in the `try` block. A `try` block can have multiple `catch` blocks for different exception types
  - `finally` **block**: Contains code that will always execute, regardless of whether an exception occurred or was caught. Often used for resource cleanup (closing files, connections).
- `throw` vs `throws`:
  - `throw`: Used to explicitly throw an instance of an exception. Inside a method body.
  - `throws`: Used in a method signature to declare that a method might throw one or more specified checked exceptions. This forces the caller to either handle or re-declare the exception
- **Custom Exceptions**: You can create your own exception classes by extending `Exception` (for checked) or `RuntimeException` (for unchecked). Useful for specific business logic errors
- **Checked vs Unchecked Exceptions**:
  - **Checked Exceptions**: (e.g., `IOException`, `SQLException`) Subclasses of `Exception` (but not `RuntimeException`). The compiler forces you to handle them (either `try-catch` or `throws`). Typically represent external issues that your program might not be able to prevent but should handle
  - **Unchecked Exceptions**: (e.g., `NullPointerException`, `ArrayIndexOutOfBoundsException`, `ArithmeticException`) Subclasses of `RuntimeException`. The compiler does not force you to handle them. Usually indicate programming errors or logical flaws that should be fixed rather than caught
- **Best Practices**:
  - **Specific** `catch` **blocks first**: Catch more specific exceptions before more general ones.
  - **Don't swallow exceptions**: Don't just `catch (Exception e) {}` without logging or handling. This hides problems.
  - **Use** `finally` **for cleanup**: Ensure resources are closed.
  - **Try-with-resources (Java 7+)**: Automatically closes resources that implement `AutoCloseable`. Highly recommended.
  - **Log exceptions**: Use a logging framework (e.g., Log4j, SLF4j) to log exception details.
  - **Throw early, catch late**: Throw exceptions as soon as an error condition is detected. Catch them at a level where they can be meaningfully handled or escalated

#### Example Code Snippet
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

// Custom Checked Exception
class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
}

public class ExceptionHandling {

    // Method demonstrating throws and a checked exception
    public static void readFile(String filePath) throws IOException {
        try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } // No need for finally block here, try-with-resources handles closing
    }

    // Method demonstrating a custom checked exception
    public static void withdraw(double amount) throws InsufficientFundsException {
        double currentBalance = 500.0; // Simulate balance
        if (amount > currentBalance) {
            throw new InsufficientFundsException("Withdrawal amount " + amount + " exceeds current balance " + currentBalance);
        }
        currentBalance -= amount;
        System.out.println("Withdrawal successful. New balance: " + currentBalance);
    }

    public static void main(String[] args) {
        // Example 1: Handling a checked exception (IOException)
        try {
            readFile("nonExistentFile.txt");
        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
            // e.printStackTrace(); // For debugging, not for production logging
        } finally {
            System.out.println("File reading attempt finished.");
        }

        // Example 2: Handling a custom checked exception
        try {
            withdraw(600.0);
        } catch (InsufficientFundsException e) {
            System.err.println("Transaction failed: " + e.getMessage());
        }

        // Example 3: Unchecked exception (ArrayIndexOutOfBoundsException)
        int[] numbers = {1, 2, 3};
        try {
            System.out.println(numbers[5]); // This will throw ArrayIndexOutOfBoundsException
        } catch (ArrayIndexOutOfBoundsException e) {
            System.err.println("Array index out of bounds: " + e.getMessage());
        } finally {
            System.out.println("Demonstrating finally with unchecked exception.");
        }
    }
}
```

#### Common Mistakes / Tips
- **Catching** `Exception` **too broadly**: Hides specific issues. Catch specific exceptions first, then broader ones if needed.
- **Ignoring exceptions**: An empty `catch` block (`catch (Exception e) {}`) is a common anti-pattern. Always log or handle the exception.
- **Not closing resources**: Before `try-with-resources`, forgetting to close file streams or database connections was a major source of resource leaks.
- **Throwing** `RuntimeException` **unnecessarily**: If a problem is something the caller can reasonably recover from, use a checked exception. If it's a bug in your code, an unchecked exception (or fixing the bug) is appropriate.
- **Using** `printStackTrace()` **in production**: It prints to standard error, which might not be captured by logging systems. Use a proper logging framework.

---

## Collections Framework
### Explanation / Key Concepts
The Java Collections Framework provides a unified architecture for representing and manipulating collections of objects.
- **Interfaces**:
  - `Collection` **(root interface)**: Basic operations like `add()`, `remove()`, `size()`, `isEmpty()`.
  - `List`: Ordered collection (sequence) that allows duplicate elements. Maintains insertion order. Access by index
    - **Implementations**: `ArrayList` (dynamic array, good for random access), `LinkedList` (doubly linked list, good for insertions/deletions at ends), `Vector` (legacy, synchronized, rarely used)
  - `Set`: Unordered collection that does not allow duplicate elements.
    - **Implementations**: `HashSet` (uses a hash table, fastest for most operations, no guaranteed order), `LinkedHashSet` (maintains insertion order), `TreeSet` (stores elements in sorted order, uses a Red-Black tree).
  - `Map`: Stores key-value pairs. Keys must be unique, values can be duplicated.
    - **Implementations**: `HashMap` (uses a hash table, fastest for most operations, no guaranteed order), `LinkedHashMap` (maintains insertion order of keys), `TreeMap` (stores entries in sorted order of keys, uses a Red-Black tree), `HashTable` (legacy, synchronized, rarely used).
  - `Queue`: Designed for holding elements prior to processing. Follows FIFO (First-In, First-Out) principle
    - **Implementations**: `LinkedList` (can act as a Queue), `PriorityQueue` (elements ordered based on their natural ordering or a `Comparator`).
- **Sorting and Searching with Collection**:
  - `Collections.sort()`: Sorts `List` implementations. Requires elements to implement `Comparable` or provide a `Comparator`.
  - `Collections.binarySearch()`: Efficiently searches a sorted `List`
- `Comparable` **vs** `Comparator`:
  - `Comparable` (natural ordering): Interface with `compareTo()` method. Implemented by the class whose objects are to be sorted. Defines a single "natural" way of comparing objects of that class.
  - `Comparator` (custom ordering): Interface with `compare()` method. Implemented by a separate class. Provides alternative ways to sort objects without modifying the original class. Useful for defining multiple sorting criteria
- **Fail-fast vs Fail-safe Iterators**:
- **Fail-fast**: Iterators that throw a `ConcurrentModificationException` if a collection is structurally modified (add, remove) while an iteration is in progress, except through the iterator's own `remove()` method. Examples: `ArrayList`, `HashMap`.
- **Fail-safe**: Iterators that do not throw `ConcurrentModificationException` in the face of structural modification. They typically operate on a clone or snapshot of the collection at the time the iterator was created. Examples: `CopyOnWriteArrayList`, `ConcurrentHashMap` (iterators on these are fail-safe for some operations, but not all).

#### Example Code Snippet
```java
import java.util.*;

public class CollectionsDemo {
    public static void main(String[] args) {
        // List (ArrayList)
        List<String> names = new ArrayList<>();
        names.add("Alice");
        names.add("Bob");
        names.add("Charlie");
        names.add("Alice"); // Duplicates allowed
        System.out.println("ArrayList: " + names);
        System.out.println("Element at index 1: " + names.get(1));

        // Set (HashSet)
        Set<String> uniqueNames = new HashSet<>();
        uniqueNames.add("Alice");
        uniqueNames.add("Bob");
        uniqueNames.add("Charlie");
        uniqueNames.add("Alice"); // Duplicate ignored
        System.out.println("HashSet (no duplicates, no order): " + uniqueNames);

        // Map (HashMap)
        Map<Integer, String> studentMap = new HashMap<>();
        studentMap.put(101, "David");
        studentMap.put(102, "Eve");
        studentMap.put(101, "Frank"); // Overwrites value for key 101
        System.out.println("HashMap: " + studentMap);
        System.out.println("Student with ID 102: " + studentMap.get(102));

        // Sorting with Comparable (assuming Person implements Comparable)
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 30));
        people.add(new Person("Bob", 25));
        people.add(new Person("Charlie", 35));
        Collections.sort(people); // Sorts by age (natural ordering)
        System.out.println("Sorted People (by age): " + people);

        // Sorting with Comparator (by name length)
        Collections.sort(people, new Comparator<Person>() {
            @Override
            public int compare(Person p1, Person p2) {
                return Integer.compare(p1.getName().length(), p2.getName().length());
            }
        });
        System.out.println("Sorted People (by name length): " + people);

        // Fail-fast iterator example (will throw ConcurrentModificationException)
        /*
        List<String> fruits = new ArrayList<>(Arrays.asList("Apple", "Banana", "Orange"));
        for (String fruit : fruits) {
            if (fruit.equals("Banana")) {
                fruits.remove(fruit); // Throws ConcurrentModificationException
            }
        }
        */
        // Correct way to remove during iteration (using Iterator's remove)
        Iterator<String> it = names.iterator();
        while (it.hasNext()) {
            String name = it.next();
            if (name.equals("Alice")) {
                it.remove();
            }
        }
        System.out.println("Names after safe removal: " + names);
    }
}

class Person implements Comparable<Person> {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age); // Natural ordering by age
    }

    @Override
    public String toString() {
        return "Person{" + "name='" + name + '\'' + ", age=" + age + '}';
    }
}
```

#### Common Mistakes / Tips
- **Modifying collections while iterating**: Using a `for-each` loop and modifying the collection (add/remove) will lead to `ConcurrentModificationException` with fail-fast iterators. Use the `Iterator`'s `remove()` method or a new list for modifications.
- **Incorrect** `hashCode()` **and** `equals()` **implementation**: For custom objects to work correctly in `HashSet`, `HashMap`, etc., you must override both `equals()` and `hashCode()` methods consistently. If two objects are equal, their hash codes must be the same.
- **Performance implications**: Be aware of the Big O notation for different collection operations (e.g., `ArrayList` for random access is O(1), `LinkedList` for insertion/deletion at ends is O(1)).
- **Choosing the right collection**: Don't just default to `ArrayList` or `HashMap`. Understand the strengths and weaknesses of each to pick the most suitable one for the use case.
- **Thread safety**: Most collections (`ArrayList`, `HashMap`, `HashSet`) are not thread-safe. Use `Collections.synchronizedList()`/`synchronizedMap()` or concurrent collections (e.g., `ConcurrentHashMap`, `CopyOnWriteArrayList`) for multithreaded environments.

---

## Generics
### Explanation / Key Concepts
Generics enable you to write code that works with different types without compromising type safety at compile-time. They allow you to define classes, interfaces, and methods with type parameters.

- **Why Generics?**
  - **Type Safety**: Catches type errors at compile time, preventing ClassCastException at runtime.
  - **Code Reusability**: Write generic algorithms that work on collections of various types.
  - **Eliminate Casts**: Reduces the need for explicit type casting
- **Generic Classes**: A class that operates on a type parameter
  - `class Box<T> { private T content; public T getContent() { return content; } }`
- **Generic Methods**: A method that introduces its own type parameter.
  - `public static <T> void printArray(T[] array) { ... }`
- **Bounded Types**: Restrict the types that can be used as type arguments
  - `<T extends Comparable<T>>`: `T` must be `Comparable` or a subclass.
  - `<T extends Number>`: `T` must be a `Number` or a subclass
- **Wildcards**: `?`, `<? extends T>`, `<? super T>`: Used in generic method signatures to provide more flexibility.
  - `?` **(Unbounded Wildcard)**: Represents an unknown type. `List<?>` means a list of unknown type. Useful when you don't care about the specific type, only that it's a list. Can read from it, but cannot add (except `null`).
  - `<? extends T> ` **(Upper Bounded Wildcard)**: `List<? extends Number>` means a list of Number or any of its subclasses (e.g., `Integer`, `Double`). "Producer Extends." You can read `T` objects from this list, but you cannot add (except `null`) to ensure type safety.
  - `<? super T>` or any of its superclasses (e.g., `Number`, `Object`). "Consumer Super." You can add T objects (or its subtypes) to this list, but reading from it requires casting to `Object`. 

#### Example Code Snippet
```java
import java.util.ArrayList;
import java.util.List;

// Generic Class
class Box<T> {
    private T content;

    public Box(T content) {
        this.content = content;
    }

    public T getContent() {
        return content;
    }

    public void setContent(T content) {
        this.content = content;
    }
}

public class GenericsDemo {

    // Generic Method with Bounded Type
    public static <T extends Comparable<T>> T findMax(T x, T y) {
        return (x.compareTo(y) > 0) ? x : y;
    }

    // Producer Extends (Reading from a list of unknown Number types)
    public static void printNumbers(List<? extends Number> list) {
        for (Number n : list) {
            System.out.print(n + " ");
        }
        System.out.println();
        // list.add(new Integer(5)); // Compile-time error! Cannot add elements (except null)
    }

    // Consumer Super (Adding to a list that accepts Integers or its supertypes)
    public static void addIntegers(List<? super Integer> list) {
        list.add(10);
        list.add(20);
        list.add(30);
        // Integer i = list.get(0); // Compile-time error! Cannot read specific type without cast
    }

    public static void main(String[] args) {
        // Generic Class Usage
        Box<String> stringBox = new Box<>("Hello Generics");
        System.out.println("String Box: " + stringBox.getContent());

        Box<Integer> integerBox = new Box<>(123);
        System.out.println("Integer Box: " + integerBox.getContent());

        // Generic Method Usage
        Integer maxInt = findMax(5, 10);
        System.out.println("Max Integer: " + maxInt);

        String maxString = findMax("Apple", "Banana");
        System.out.println("Max String: " + maxString);

        // Wildcard Examples
        List<Integer> intList = new ArrayList<>();
        intList.add(1);
        intList.add(2);
        printNumbers(intList); // Works because Integer extends Number

        List<Double> doubleList = new ArrayList<>();
        doubleList.add(1.1);
        doubleList.add(2.2);
        printNumbers(doubleList); // Works because Double extends Number

        List<Number> numberList = new ArrayList<>();
        addIntegers(numberList); // Works because Integer is a subtype of Number
        System.out.println("Number List after adding integers: " + numberList);

        // Unbounded Wildcard
        List<?> unknownList = intList; // Can be assigned any type of list
        System.out.println("Unknown List: " + unknownList);
    }
}
```
#### Common Mistakes / Tips
- **Not understanding Type Erasure**: At runtime, generic type information is erased. This means you can't use type parameters for runtime checks (`instanceof T`) or to create new instances of `T` (`new T()`).
- **Misusing wildcards**: PECS (Producer Extends, Consumer Super) is crucial. If you only read from a generic collection, use `? extends T`. If you only add to it, use `? super T`. If you do both, don't use a wildcard.
- **Raw types**: Using `List` instead of `List<String>` (`raw type`) disables compile-time type checking and can lead to runtime `ClassCastException`. Always use parameterized types.
- **Arrays of generic types**: You cannot create arrays of parameterized types (e.g., `new List<String>[10]`). Due to type erasure, the JVM wouldn't know the component type at runtime. Workarounds involve using `ArrayList` or unchecked casts

---

## Multithreading & Concurrency
### Explanation / Key Concepts
Multithreading allows a program to execute multiple parts concurrently, improving responsiveness and throughput, especially on multi-core processors. Concurrency deals with managing access to shared resources by multiple threads
- **Thread Lifecycle and Basic Usage:**
  - **New**: Thread has been created but not yet started.
  - **Runnable**: Thread is ready to run (either running or waiting for CPU).
  - **Running**: Thread is currently executing.
  - **Blocked/Waiting**: Thread is temporarily inactive (e.g., waiting for I/O, a lock, or `notify()`).
  - **Terminated/Dead**: Thread has finished execution.
- **Creating Threads**:
  1. **Extend** `Thread` **class**: Override `run()` method.
  2. **Implement** `Runnable` **interface**: Implement `run()` method. (Preferred as it allows your class to extend other classes).
- **Starting a thread**: Call `threadObject.start()`. Calling `run()` directly executes it in the current thread.
- `Runnable` **vs** `Callable`:
  - `Runnable`: `run()` method has `void` return type and cannot throw checked exceptions. Suitable for tasks that don't return a result.
  - `Callable`: `call()` method returns a `V` type and can throw `Exception`. Used with `ExecutorService` and `Future` for tasks that return a result or throw exceptions.
- `synchronized` **keyword**: Used to achieve thread safety by allowing only one thread at a time to execute a block of code or a method.
  - **Synchronized method**: Locks on the object itself (`this`).
  - **Synchronized static method**: Locks on the class object (`ClassName.class`).
  - **Synchronized block**: Locks on a specified object. More granular control.
- `volatile` **keyword**: Ensures visibility of modifications to a variable across threads. Guarantees that reads of the `volatile` variable will always see the most recent write, and ensures that operations before a `volatile` write happen before the write, and operations after a `volatile` read happen after the read. Does not guarantee atomicity.
- `wait()`, `notify()`, `notifyAll()`: Methods of the Object class, used for inter-thread communication
  - Must be called from within a synchronized block/method.
  - `wait()`: Releases the lock on the object and puts the current thread into a waiting state until another thread calls `notify()` or `notifyAll()` on the same object.
  - `notify()`: Wakes up a single waiting thread.
  - `notifyAll()`: Wakes up all waiting threads.
- **Executors Framework, Thread Pools**:
  - `ExecutorService`: Interface for managing thread pools.
  - `Executors` utility class: Factory methods to create different types of thread pools (`newFixedThreadPool`, `newCachedThreadPool`, `newSingleThreadExecutor`).
  - **Thread Pool**: A collection of pre-initialized threads that can be reused for tasks. Reduces overhead of creating and destroying threads.
- **Locks (from** `java.util.concurrent.locks)`: More flexible and powerful alternatives to synchronized.
  - `ReentrantLock`: A mutual exclusion lock. Provides `lock()`, `unlock()`, `tryLock()`, `lockInterruptibly()`.
  - `ReadWriteLock`: Allows multiple readers or one writer at a time. Improves concurrency for read-heavy workloads.
- **Semaphores**: Controls access to a limited number of resources. A semaphore maintains a count of available permits. Threads acquire permits to access a resource and release them when done.
- `ConcurrentHashMap`: A thread-safe, high-performance `Map` implementation. Uses fine-grained locking (segment locking in older versions, node-level locking in Java 8+) to allow concurrent reads and writes. Much more efficient than `Collections.synchronizedMap(new HashMap())`.

#### Example Code Snippet
```java
import java.util.concurrent.*;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

// Example 1: Thread creation (Runnable)
class MyRunnable implements Runnable {
    private String taskName;

    public MyRunnable(String taskName) {
        this.taskName = taskName;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " - Starting " + taskName);
        try {
            Thread.sleep(100); // Simulate work
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println(Thread.currentThread().getName() + " - Finished " + taskName);
    }
}

// Example 2: Synchronized method
class Counter {
    private int count = 0;

    // Synchronized method
    public synchronized void increment() {
        count++;
        System.out.println(Thread.currentThread().getName() + " Increment: " + count);
    }

    public int getCount() {
        return count;
    }
}

// Example 3: Callable and Future with ExecutorService
class SumTask implements Callable<Integer> {
    private int from;
    private int to;

    public SumTask(int from, int to) {
        this.from = from;
        this.to = to;
    }

    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = from; i <= to; i++) {
            sum += i;
        }
        System.out.println(Thread.currentThread().getName() + " - Calculated sum from " + from + " to " + to + ": " + sum);
        return sum;
    }
}

// Example 4: ReentrantLock
class SharedResourceWithLock {
    private int data = 0;
    private final Lock lock = new ReentrantLock();

    public void increment() {
        lock.lock(); // Acquire the lock
        try {
            data++;
            System.out.println(Thread.currentThread().getName() + " - Data: " + data);
        } finally {
            lock.unlock(); // Release the lock in a finally block
        }
    }
}


public class ConcurrencyDemo {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        // --- 1. Basic Thread Creation ---
        System.out.println("--- Basic Thread Creation ---");
        Thread t1 = new Thread(new MyRunnable("Task A"));
        Thread t2 = new Thread(new MyRunnable("Task B"));
        t1.start();
        t2.start();
        t1.join(); // Wait for t1 to finish
        t2.join(); // Wait for t2 to finish
        System.out.println("All basic tasks finished.\n");


        // --- 2. Synchronized Method ---
        System.out.println("--- Synchronized Method ---");
        Counter counter = new Counter();
        Runnable incrementTask = () -> {
            for (int i = 0; i < 5; i++) {
                counter.increment();
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread s1 = new Thread(incrementTask, "Thread-S1");
        Thread s2 = new Thread(incrementTask, "Thread-S2");
        s1.start();
        s2.start();
        s1.join();
        s2.join();
        System.out.println("Final Counter value (synchronized): " + counter.getCount() + "\n");


        // --- 3. ExecutorService and Callable/Future ---
        System.out.println("--- ExecutorService and Callable/Future ---");
        ExecutorService executor = Executors.newFixedThreadPool(2);

        Future<Integer> future1 = executor.submit(new SumTask(1, 10));
        Future<Integer> future2 = executor.submit(new SumTask(11, 20));

        System.out.println("Sum 1-10: " + future1.get()); // .get() blocks until result is available
        System.out.println("Sum 11-20: " + future2.get());

        executor.shutdown(); // Initiates an orderly shutdown
        executor.awaitTermination(1, TimeUnit.MINUTES); // Wait for tasks to finish
        System.out.println("ExecutorService tasks finished.\n");

        // --- 4. ReentrantLock ---
        System.out.println("--- ReentrantLock ---");
        SharedResourceWithLock sharedResource = new SharedResourceWithLock();
        Runnable lockTask = () -> {
            for (int i = 0; i < 3; i++) {
                sharedResource.increment();
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        };

        Thread l1 = new Thread(lockTask, "Thread-L1");
        Thread l2 = new Thread(lockTask, "Thread-L2");
        l1.start();
        l2.start();
        l1.join();
        l2.join();
        System.out.println("ReentrantLock demo finished.\n");


        // --- 5. ConcurrentHashMap (brief demo) ---
        System.out.println("--- ConcurrentHashMap ---");
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        map.put("A", 1);
        map.put("B", 2);
        map.forEach((k, v) -> System.out.println("Key: " + k + ", Value: " + v));
        System.out.println("ConcurrentHashMap is thread-safe for these operations.\n");
    }
}
```

#### Common Mistakes / Tips
- **Calling** `run()` **instead of** `start()`: `run()` executes the task in the current thread, not a new one. `start()` creates and schedules a new thread.
- **Race Conditions**: When multiple threads try to access and modify shared data simultaneously, leading to unpredictable results. Use synchronization (`synchronized`, `Lock`) or thread-safe data structures.
- **Deadlock**: Two or more threads are blocked indefinitely, each waiting for the other to release a resource. Prevention strategies: avoid nested locks, acquire locks in a consistent order, use timed `tryLock()`.
- **Starvation**: A thread repeatedly loses the race for a resource or CPU time.
- **Livelock**: Threads are active but not making progress, constantly reacting to each other's actions (e.g., trying to acquire locks repeatedly without success).
- **Not releasing locks**: Forgetting `lock.unlock()` (especially outside a `finally` block) can lead to deadlocks.
- `volatile` **for atomicity**: Remember `volatile` only ensures visibility, not atomicity. For atomic operations (like `count++`), use `AtomicInteger` or `synchronized`.
- **Blocking main thread**: In `main()`, if you use `Thread.sleep()` or `future.get()` excessively without proper management, your main thread might block, making the application unresponsive.
- **Choosing the right concurrency tool**: Don't just use `synchronized` for everything. Consider `ReentrantLock` for advanced locking, `Semaphore` for resource limiting, `ConcurrentHashMap` for thread-safe maps, and `Atomic` classes for atomic operations on single variables.

---

## Java I/O and NIO
Java I/O (Input/Output) provides mechanisms for reading and writing data to various sources (files, network streams, console). NIO (New I/O) offers more advanced, non-blocking I/O capabilities.
- **File Handling (Traditional I/O -** `java.io`:
  - `File` **class**: Represents file and directory pathnames. Used for creating, deleting, renaming files/directories, checking attributes.
  - **Stream-based I/O**:
    - **Byte Streams**: `InputStream` and `OutputStream` (abstract classes). For handling raw binary data.
      - `FileInputStream`, `FileOutputStream`: For reading/writing bytes from/to files.
      - `BufferedInputStream`, `BufferedOutputStream`: Add buffering for improved performance
    - **Character Streams**: `Reader` and `Writer` (abstract classes). For handling character data, automatically handles character encoding.
      - `FileReader`, `FileWriter`: For reading/writing characters from/to files.
      - `BufferedReader`, `BufferedWriter`: Add buffering for improved performance. `readLine()` is very useful.
      - `InputStreamReader`, `OutputStreamWriter`: Bridges between byte streams and character streams, allowing you to specify encoding
    - **Object Streams**: `ObjectInputStream`, `ObjectOutputStream`: Used for serialization and deserialization of Java objects

- **Serialization & Deserialization**:
  - **Serialization**: The process of converting an object's state into a byte stream. This stream can then be saved to a file, transmitted across a network, or stored in a database.
  - **Deserialization**: The reverse process, reconstructing the object from the byte stream.
  - To be serializable, a class must implement the `java.io.Serializable` marker interface.
  - Fields marked `transient` are not serialized
  - Static fields are not serialized
- **Java NIO (** `java.nio` **)**: Introduced to address performance bottlenecks of traditional I/O, especially for high-volume data transfers. It is buffer-oriented and channel-based.
  - **Channels**: Represent connections to entities that are capable of performing I/O operations (e.g., files, sockets). Data is read from/written to channels into/from buffers.
    - `FileChannel`, `SocketChannel`, `ServerSocketChannel`, `DatagramChannel`
  - **Buffers**: Fixed-size blocks of memory that hold data. Data is read from a channel into a buffer, and written from a buffer into a channel.
    - `ByteBuffer`, `CharBuffer`, `IntBuffer`, etc
    - Key properties: `capacity`, `limit`, `position`
  - **Selectors**: Allow a single thread to monitor multiple channels for I/O events (e.g., data ready to be read, channel ready to be written). Enables non-blocking I/O and highly scalable servers.

#### Example Code Snippet
```java
import java.io.*;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.file.Paths;
import java.nio.file.StandardOpenOption;

// Serializable class
class Student implements Serializable {
    private static final long serialVersionUID = 1L; // Recommended for serialization
    private String name;
    private int age;
    private transient String password; // This field will not be serialized

    public Student(String name, int age, String password) {
        this.name = name;
        this.age = age;
        this.password = password;
    }

    @Override
    public String toString() {
        return "Student{" + "name='" + name + '\'' + ", age=" + age + ", password='" + password + '\'' + '}';
    }
}

public class IOAndNIO {

    // Traditional I/O: Writing and Reading a text file
    public static void writeAndReadTextFile(String fileName, String content) {
        System.out.println("\n--- Traditional I/O (Text File) ---");
        // Writing
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(fileName))) {
            writer.write(content);
            System.out.println("Successfully wrote to " + fileName);
        } catch (IOException e) {
            System.err.println("Error writing file: " + e.getMessage());
        }

        // Reading
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String line;
            System.out.println("Content of " + fileName + ":");
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
        }
    }

    // Serialization and Deserialization
    public static void serializeAndDeserialize(String fileName) {
        System.out.println("\n--- Serialization/Deserialization ---");
        Student student = new Student("Alice", 25, "securePassword123");

        // Serialization
        try (FileOutputStream fileOut = new FileOutputStream(fileName);
             ObjectOutputStream objOut = new ObjectOutputStream(fileOut)) {
            objOut.writeObject(student);
            System.out.println("Student object serialized to " + fileName);
        } catch (IOException e) {
            System.err.println("Error during serialization: " + e.getMessage());
        }

        // Deserialization
        try (FileInputStream fileIn = new FileInputStream(fileName);
             ObjectInputStream objIn = new ObjectInputStream(fileIn)) {
            Student deserializedStudent = (Student) objIn.readObject();
            System.out.println("Student object deserialized: " + deserializedStudent);
            // Note: password will be null or default value because it's transient
        } catch (IOException | ClassNotFoundException e) {
            System.err.println("Error during deserialization: " + e.getMessage());
        }
    }

    // NIO: Writing and Reading a text file
    public static void nioFileOperations(String fileName, String content) {
        System.out.println("\n--- Java NIO (File Operations) ---");
        java.nio.file.Path path = Paths.get(fileName);

        // Writing with NIO
        try (FileChannel channel = FileChannel.open(path, StandardOpenOption.CREATE, StandardOpenOption.WRITE)) {
            ByteBuffer buffer = ByteBuffer.wrap(content.getBytes());
            channel.write(buffer);
            System.out.println("NIO: Successfully wrote to " + fileName);
        } catch (IOException e) {
            System.err.println("NIO Error writing file: " + e.getMessage());
        }

        // Reading with NIO
        try (FileChannel channel = FileChannel.open(path, StandardOpenOption.READ)) {
            ByteBuffer buffer = ByteBuffer.allocate(1024); // Allocate a buffer
            int bytesRead = channel.read(buffer); // Read data into buffer
            buffer.flip(); // Prepare buffer for reading (set limit to current position, position to 0)

            System.out.println("NIO: Content of " + fileName + ":");
            while (buffer.hasRemaining()) {
                System.out.print((char) buffer.get()); // Read byte by byte as char
            }
            System.out.println();
        } catch (IOException e) {
            System.err.println("NIO Error reading file: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        String textFileName = "myTextFile.txt";
        String objectFileName = "student.ser";
        String nioFileName = "nioFile.txt";

        writeAndReadTextFile(textFileName, "Hello from traditional I/O!\nThis is line two.");
        serializeAndDeserialize(objectFileName);
        nioFileOperations(nioFileName, "Hello from Java NIO!\nThis is another line.");
    }
}
```

#### Common Mistakes / Tips
- **Forgetting to close streams**: Crucial for releasing resources and preventing resource leaks. Use `try-with-resources` (Java 7+) for automatic closing.
- **Handling** `ClassNotFoundException` **during deserialization**: If the class definition for a serialized object is not found or has changed incompatible, this exception occurs.
- **Performance with traditional I/O**: For large files or high-frequency I/O, `Buffered` streams are essential for performance.
- **Misunderstanding** `volatile` **and** `synchronized` **in concurrent file access**: Even with `FileChannel`, concurrent access to the same file still requires careful synchronization to prevent data corruption.
- **NIO buffer states (**`position`, `limit`, `capacity`**)**: Mastering these is key to effective NIO programming. `flip()` is often forgotten after writing to a buffer and before reading from it. `clear()` and `compact()` are also important.
- **Not using** `serialVersionUID`: While not strictly required, it's highly recommended to declare `serialVersionUID` in `Serializable` classes to ensure version compatibility during deserialization.
- `transient` **keyword misuse**: Only use it for fields that should not be persisted (e.g., sensitive data, calculated fields)

---

## Java 8 and Beyond
### Explanation / Key Concepts
Java 8 introduced significant features that revolutionized how Java code is written, particularly for concurrent and functional programming. Subsequent versions have continued to add valuable enhancements.
- **Lambda Expressions**: Anonymous functions that provide a concise way to represent an instance of a functional interface
  - Syntax: `(parameters) -> { body }`
  - Enable functional programming paradigms
- **Streams API**: A powerful abstraction for processing sequences of elements (collections, arrays, I/O channels).
  - **Pipeline**: A sequence of operations performed on elements.
  - **Source**: The collection or array from which the stream originates.
  - **Intermediate Operations**: Return another stream, allowing chaining (e.g., `filter()`, `map()`, `sorted()`, `distinct()`). They are lazy (don't execute until a terminal operation is called).
  - **Terminal Operations**: Produce a result or a side effect, consuming the stream (e.g., `forEach()`, `collect()`, `reduce()`, `count()`, `min()`, `max()`).
- **Functional Interfaces**: Interfaces with exactly one abstract method.
  - Marked with `@FunctionalInterface` annotation (optional but recommended for clarity).
  - Examples: `Predicate<T>` (boolean test), `Consumer<T>` (void operation), `Function<T, R>` (T to R mapping), `Supplier<T>` (produces T), `BiConsumer`, `BiFunction`, etc.
  - Can be targeted by lambda expressions
- `Optional` **Class**: A container object that may or may not contain a non-null value.
  - Designed to deal with `NullPointerException` by forcing explicit checks for presence/absence of a value.
  - Methods: `isPresent()`, `isEmpty()` (Java 11+), `get()`, `orElse()`, `orElseThrow()`, `ifPresent()`, `map()`, `flatMap()`.
- **Method References**: A shorthand syntax for lambda expressions that simply call an existing method
- **Types**:
  - Static method: `ClassName::staticMethod`
  - Instance method of a particular object: `objectInstance::instanceMethod`
  - Instance method of an arbitrary object of a particular type: `ClassName::instanceMethod` (e.g., `String::length`)
  - Constructor reference: `ClassName::new`

#### Example Code Snippet
```java
import java.util.Arrays;
import java.util.List;
import java.util.Optional;
import java.util.stream.Collectors;

public class Java8AndBeyond {

    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David", "Eve");

        // --- 1. Lambda Expressions and Functional Interfaces ---
        System.out.println("--- Lambda Expressions ---");
        // Using Predicate (functional interface) with lambda
        names.stream()
             .filter(name -> name.startsWith("A")) // Lambda as Predicate
             .forEach(name -> System.out.println("Name starting with A: " + name)); // Lambda as Consumer

        // Using Comparator (functional interface) with lambda for sorting
        List<String> sortedNames = names.stream()
                                        .sorted((s1, s2) -> s1.compareToIgnoreCase(s2))
                                        .collect(Collectors.toList());
        System.out.println("Sorted Names: " + sortedNames);

        // --- 2. Streams API ---
        System.out.println("\n--- Streams API ---");
        // Filter, Map, Collect
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        List<Integer> evenSquares = numbers.stream()
                                          .filter(n -> n % 2 == 0) // Intermediate: filter even numbers
                                          .map(n -> n * n)         // Intermediate: square them
                                          .collect(Collectors.toList()); // Terminal: collect into a new List
        System.out.println("Even Squares: " + evenSquares);

        // Reduce
        int sum = numbers.stream()
                         .reduce(0, (a, b) -> a + b); // Terminal: sum all numbers
        System.out.println("Sum of numbers: " + sum);

        // FlatMap example
        List<List<String>> listOfLists = Arrays.asList(
            Arrays.asList("a", "b"),
            Arrays.asList("c", "d")
        );
        List<String> flattenedList = listOfLists.stream()
                                               .flatMap(Collection::stream) // Flattens list of lists into a single stream
                                               .collect(Collectors.toList());
        System.out.println("Flattened List: " + flattenedList);


        // --- 3. Optional Class ---
        System.out.println("\n--- Optional Class ---");
        String value = "Hello Optional";
        Optional<String> optionalValue = Optional.ofNullable(value); // Can be null

        if (optionalValue.isPresent()) {
            System.out.println("Value present: " + optionalValue.get());
        }

        String defaultValue = optionalValue.orElse("Default String");
        System.out.println("Value or default: " + defaultValue);

        String nullValue = null;
        Optional<String> optionalNull = Optional.ofNullable(nullValue);
        System.out.println("Value or default (null case): " + optionalNull.orElse("Another Default"));

        // Using map with Optional
        Optional<Integer> length = optionalValue.map(String::length);
        length.ifPresent(len -> System.out.println("Length of value: " + len));


        // --- 4. Method References ---
        System.out.println("\n--- Method References ---");
        // Static method reference
        numbers.forEach(System.out::println); // Equivalent to n -> System.out.println(n)

        // Instance method of a particular object
        List<String> upperCaseNames = names.stream()
                                           .map(String::toUpperCase) // Equivalent to s -> s.toUpperCase()
                                           .collect(Collectors.toList());
        System.out.println("Uppercase Names: " + upperCaseNames);

        // Constructor reference
        // (Assuming a simple Person class with a constructor that takes a string name)
        // List<Person> people = names.stream()
        //                            .map(Person::new)
        //                            .collect(Collectors.toList());
    }
}
```

#### Common Mistakes / Tips
- **Modifying stream source within intermediate operations**: Streams are designed to be stateless and non-interfering. Modifying the underlying collection during stream operations can lead to unpredictable behavior.
- **Reusing streams**: A stream can only be consumed once (terminal operation). After a terminal operation, the stream is "closed" and cannot be reused.
- **Overusing** `Optional.get()`: If `get()` is called on an empty `Optional`, it throws `NoSuchElementException`. Always use `isPresent()`, `orElse()`, `orElseThrow()`, or `ifPresent()` to safely access the value.
- **Performance of parallel streams**: While powerful, parallel streams are not always faster. The overhead of parallelization can sometimes outweigh the benefits for small datasets or tasks that are not computationally intensive. Use them judiciously.
- **Debugging streams**: Debugging stream pipelines can be tricky due to their declarative nature and lazy execution. Use `peek()` for intermediate debugging.
- **Over-complicating with lambdas**: Sometimes a traditional loop is more readable or performant for simple tasks.
- **Understanding** `Collector`: The `Collectors` class provides powerful methods for reducing streams into collections, maps, or single values. Familiarize yourself with common collectors like `toList()`, `toSet()`, `toMap()`, `groupingBy()`.

---

## JVM Internals
### Explanation / Key Concepts
The Java Virtual Machine (JVM) is the runtime environment that executes Java bytecode. Understanding its internals is crucial for performance tuning, debugging, and advanced Java development

- **Java Memory Model (JMM)**: Defines how threads interact with memory (heap and stack) and ensures consistency across different processors and caches.
  - **Heap**: Shared by all threads. Stores all objects and arrays.
    - **Young Generation (Eden, S0, S1)**: Where new objects are initially allocated. Minor GC runs here.
    - **Old Generation (Tenured)**: Objects that survive multiple minor GCs are moved here. Major GC/Full GC runs here.
  - **Stack**: Each thread has its own private stack. Stores local variables, method call frames, and references to objects on the heap. Primitive values are stored directly on the stack.
  - **Method Area (Metaspace in Java 8+)**: Stores class-level data (metadata, static variables, method code).
  - **PC Registers**: Each thread has its own PC register, which stores the address of the current instruction being executed.
  - **Native Method Stacks**: For native methods written in other languages (e.g., C/C++)
- **Garbage Collection (GC)**: Automatic memory management process that reclaims memory occupied by objects that are no longer referenced by the program.
    - **Generational GC**: Divides heap into generations (Young, Old) assuming most objects are short-lived. This optimizes GC performance by running frequent minor GCs on the Young Generation
    - **GC Algorithms**:
      - **Serial GC**: Single-threaded, suitable for small applications.
      - **Parallel GC (Throughput Collector)**: Multiple threads for young generation collection, single thread for old generation. Good for multi-core CPUs.
      - **CMS GC (Concurrent Mark-Sweep):** Attempts to minimize stop-the-world pauses by doing most work concurrently with application threads. Deprecated in Java 9, removed in Java 14.
      - **G1 GC (Garbage-First)**: Default since Java 9. Divides heap into regions and prioritizes regions with the most garbage. Aims for predictable pause times.
      - **ZGC / Shenandoah (Low-Pause Collectors)**: Experimental, highly concurrent GCs aiming for very low pause times (less than 10ms), even for very large heaps.
- **Class Loading Mechanism**: The process by which the JVM loads `.class` files into memory.
    - **Loading**: Finds the `.class` file and reads its bytecode.
    - **Linking**:
      - **Verification**: Checks the bytecode for structural correctness.
      - **Preparation**: Allocates memory for static variables and initializes them to default values.
      - **Resolution**: Replaces symbolic references with direct references (e.g., class names with actual memory addresses).
    - **Initialization**: Executes the static initializers and static blocks of the class.
    - **Class Loaders**:
      - **Bootstrap Class Loader**: Loads core Java API classes (rt.jar).
      - **Extension Class Loader**: Loads classes from `jre/lib/ext` directory.
      - **Application Class Loader**: Loads classes from the classpath (your application classes).
      - **Custom Class Loaders**: Can be created by developers for dynamic loading, hot-swapping, etc
    - **Delegation Model**: A class loader delegates the class loading request to its parent first. If the parent cannot find/load the class, the child attempts to load it. This prevents multiple copies of the same class from being loaded.
- **Just-In-Time (JIT) Compiler**:
  - Part of the JVM that converts Java bytecode into native machine code at runtime.
  - Analyzes frequently executed code ("hot spots") and compiles them to optimize performance.
  - Helps achieve performance close to native code.
  - `HotSpot` JVM uses tiered compilation.

#### Example Code Snippet (Conceptual - JVM Internals are usually observed via tools)
```java
public class JVMDemo {
    private static int staticField = 10; // Stored in Method Area (Metaspace)
    private final String name = "JVM Internals"; // Stored in Heap (object data)

    public void myMethod(int parameter) { // Method frame on Stack
        int localVar = 20; // Stored on Stack
        String temp = new String("Temporary"); // "Temporary" is in String pool, object on Heap
        Object obj = new Object(); // Object stored on Heap, 'obj' reference on Stack
        // ... method logic ...
    }

    public static void main(String[] args) {
        JVMDemo demo = new JVMDemo(); // 'demo' reference on Stack, JVMDemo object on Heap
        demo.myMethod(5);

        // Simulate creating many objects to trigger GC
        for (int i = 0; i < 1000000; i++) {
            String s = new String("Ephemeral " + i); // Many short-lived objects
        }
        System.out.println("Objects created, hoping for GC to run.");

        // We can't directly *call* GC, but we can suggest it
        System.gc(); // Suggests a garbage collection run
        System.out.println("JVM Demo complete.");
    }
}
```

#### Common Mistakes / Tips
- **Confusing Heap and Stack**: Heap for objects and shared data, Stack for method calls and local primitives.
- **Believing** `System.gc()` **forces GC**: It's merely a hint to the JVM; GC might or might not run immediately.
- **Memory Leaks**: Long-lived objects referencing short-lived objects that are no longer needed can prevent them from being garbage collected, leading to memory leaks. This often manifests as increasing heap usage over time.
- **Metaspace exhaustion**: In Java 8+, PermGen was replaced by Metaspace. If your application dynamically loads many classes, you might need to adjust MaxMetaspaceSize.
- **Ignoring GC logs**: For performance-critical applications, understanding GC logs (`-Xloggc:filename`, `-XX:+PrintGCDetails`) is crucial for tuning.
- **Misunderstanding JIT**: JIT compiles hot code, not all code. This means some parts of your application might still run as interpreted bytecode.

---

## Design Patterns
### Explanation / Key Concepts
Design patterns are reusable solutions to common problems in software design. They are not direct code snippets but rather templates for how to structure solutions. They promote code reusability, readability, maintainability, and extensibility.
- **Categorization (Gang of Four - GoF patterns)**:
  - **Creational Patterns**: Deal with object creation mechanisms, trying to create objects in a manner suitable to the situation.
    - **Singleton**: Ensures a class has only one instance and provides a global point of access to it. (e.g., Logger, configuration manager).
    - **Factory Method**: Defines an interface for creating an object, but lets subclasses decide which class to instantiate. (e.g., `NumberFormat.getInstance()`)
    - **Abstract Factory**: Provides an interface for creating families of related or dependent objects without specifying their concrete classes
    - **Builder**: Separates the construction of a complex object from its representation, allowing the same construction process to create different representations. (e.g., `StringBuilder`, `Lombok @Builder`).
    - **Prototype**: Creates new objects by copying an existing object, known as the prototype. (e.g., `clone()` method)
  - **Structural Patterns**: Deal with the composition of classes and objects.
    - **Adapter**: Allows incompatible interfaces to work together. (e.g., `InputStreamReader` adapting `InputStream` to `Reader`).
    - **Decorator**: Attaches additional responsibilities to an object dynamically. (e.g., `BufferedReader` wrapping `FileReader`).
    - **Facade**: Provides a unified interface to a set of interfaces in a subsystem. (Simplifies complex systems).
    - **Composite**: Composes objects into tree structures to represent part-whole hierarchies. (e.g., UI components like a folder containing files and subfolders).
    - **Proxy**: Provides a surrogate or placeholder for another object to control access to it. (e.g., Lazy loading, security proxy)
  - **Behavioral Patterns**: Deal with the algorithms and assignment of responsibilities between objects.
    - **Observer**: Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically. (e.g., Event listeners, MVC pattern).
    - **Strategy**: Defines a family of algorithms, encapsulates each one, and makes them interchangeable. (e.g., Different sorting algorithms, payment methods).
    - **Template** Method: Defines the skeleton of an algorithm in a superclass but lets subclasses override specific steps of the algorithm without changing its structure.
    - **Command**: Encapsulates a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.
    - **Iterator**: Provides a way to access the elements of an aggregate object sequentially without exposing its underlying representation. (e.g., Java Collections `Iterator`).

#### Example Code Snippet (Singleton and Strategy)
```java
// --- Singleton Pattern (Lazy Initialization, Thread-Safe) ---
class Logger {
    private static volatile Logger instance; // volatile for visibility across threads

    private Logger() {
        // Private constructor to prevent direct instantiation
        System.out.println("Logger instance created.");
    }

    public static Logger getInstance() {
        if (instance == null) { // First check (no lock)
            synchronized (Logger.class) { // Synchronize on class object
                if (instance == null) { // Second check (inside lock)
                    instance = new Logger();
                }
            }
        }
        return instance;
    }

    public void log(String message) {
        System.out.println("LOG: " + message);
    }
}

// --- Strategy Pattern ---
// 1. Strategy Interface
interface PaymentStrategy {
    void pay(int amount);
}

// 2. Concrete Strategies
class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;

    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }

    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card ending with " + cardNumber.substring(cardNumber.length() - 4));
    }
}

class PayPalPayment implements PaymentStrategy {
    private String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal account: " + email);
    }
}

// 3. Context (uses the strategy)
class ShoppingCart {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void checkout(int amount) {
        if (paymentStrategy == null) {
            System.out.println("No payment strategy set.");
            return;
        }
        paymentStrategy.pay(amount);
    }
}


public class DesignPatternsDemo {
    public static void main(String[] args) {
        // Singleton Demo
        System.out.println("--- Singleton Pattern ---");
        Logger logger1 = Logger.getInstance();
        logger1.log("First log message.");
        Logger logger2 = Logger.getInstance();
        logger2.log("Second log message.");
        System.out.println("Are logger1 and logger2 the same instance? " + (logger1 == logger2) + "\n");

        // Strategy Demo
        System.out.println("--- Strategy Pattern ---");
        ShoppingCart cart = new ShoppingCart();

        // Pay with Credit Card
        cart.setPaymentStrategy(new CreditCardPayment("1234-5678-9012-3456"));
        cart.checkout(100);

        // Pay with PayPal
        cart.setPaymentStrategy(new PayPalPayment("user@example.com"));
        cart.checkout(50);
    }
}
```

#### Common Mistakes / Tips
- **Over-applying patterns**: Don't force a pattern where a simpler solution suffices. Patterns should solve problems, not create complexity.
- **Misunderstanding intent**: Each pattern addresses a specific design problem. Understand why a pattern exists before trying to implement it.
- **Incorrect Singleton implementation**: Forgetting `volatile` or the double-checked locking mechanism can lead to issues in multithreaded environments. Consider using enums for the simplest thread-safe Singleton (Java 5+).
- **Rigidity vs. Flexibility**: Patterns often aim for flexibility. Ensure your implementation doesn't introduce unnecessary rigidity.
- **Knowing the GoF patterns**: While there are many patterns, the 23 GoF patterns are fundamental and frequently asked in interviews. Focus on understanding their intent, structure, and applicability.
- **Be ready to explain trade-offs**: Design patterns often come with trade-offs (e.g., increased complexity for greater flexibility). Be prepared to discuss these.

---

## Unit Testing & Build Tools
### Explanation / Key Concepts
- **Unit Testing**: A software testing method where individual units or components of a software are tested. The goal is to validate that each unit of the software performs as designed.
    - **JUnit**: A popular open-source testing framework for Java.
      - **Annotations**: `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`, `@DisplayName`, `@Disabled`
      - **Assertions**: `assertEquals()`, `assertTrue()`, `assertFalse()`, `assertNull()`, `assertNotNull()`, `assertThrows()`
      - **Test Runner**: Execute tests.
    - **TestNG**: Another powerful testing framework that offers more features than JUnit (e.g., test groups, parallel test execution, dependency testing).
- **Mocking (Mockito)**: A technique used in unit testing where you create mock objects to simulate the behavior of real objects.
  - **Purpose**:
    - Isolate the unit under test from its dependencies.
    - Control the behavior of dependencies (e.g., simulate specific return values or exceptions).
    - Test interactions (verify if a method on a dependency was called).
  - **Mockito**: A widely used mocking framework.
    - `@Mock`, `@InjectMocks`: Annotations to create mocks and inject them.
    - `Mockito.when().thenReturn()`: Stubbing method calls.
    - `Mockito.verify()`: Verifying method calls.
- **Build Tools**: Automate the process of compiling source code, running tests, packaging, and deploying applications.
  - **Maven**: A powerful project management and comprehension tool
    - **POM (Project Object Model)**: An XML file (`pom.xml`) that contains project configuration, dependencies, build phases, plugins.
    - **Convention over Configuration**: Maven tries to minimize configuration by using default directory structures and build lifecycles.
    - **Dependency Management**: Automatically downloads and manages project dependencies from repositories (e.g., Maven Central).
    - **Build Lifecycle**: Defines phases like compile, test, package, install, deploy.
  - **Gradle**: A modern, flexible build automation tool.
    - **Build Scripts**: Uses Groovy or Kotlin DSL (Domain Specific Language) for build scripts (`build.gradle`), offering more flexibility than XML.
    - **Performance**: Uses incremental builds, build cache, and daemon for faster builds.
    - **Dependency** Management: Similar to Maven, supports various repositories.
    - **Polyglot**: Supports building projects in multiple languages

#### Example Code Snippet (JUnit 5 with Mockito)
```java
// --- Service Class to be tested ---
// UserService.java
interface UserRepository {
    String findUserById(String id);
    void saveUser(String id, String username);
}

class UserService {
    private UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public String getUserName(String userId) {
        String name = userRepository.findUserById(userId);
        if (name == null || name.isEmpty()) {
            return "User Not Found";
        }
        return name;
    }

    public boolean registerUser(String userId, String username) {
        if (userRepository.findUserById(userId) != null) {
            return false; // User already exists
        }
        userRepository.saveUser(userId, username);
        return true;
    }
}

// --- JUnit 5 Test Class with Mockito ---
// UserServiceTest.java (usually in src/test/java)

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class UserServiceTest {

    @Mock // Creates a mock object of UserRepository
    private UserRepository userRepository;

    @InjectMocks // Injects the mocks into UserService instance
    private UserService userService;

    @BeforeEach
    void setUp() {
        // Initialize mocks before each test method
        MockitoAnnotations.openMocks(this);
    }

    @Test
    @DisplayName("Should return user name when user exists")
    void getUserName_UserExists() {
        // Stubbing: Define behavior for the mock
        when(userRepository.findUserById("123")).thenReturn("Alice");

        String userName = userService.getUserName("123");

        assertEquals("Alice", userName);
        // Verification: Ensure the method on the mock was called
        verify(userRepository, times(1)).findUserById("123");
    }

    @Test
    @DisplayName("Should return 'User Not Found' when user does not exist")
    void getUserName_UserNotFound() {
        when(userRepository.findUserById("456")).thenReturn(null);

        String userName = userService.getUserName("456");

        assertEquals("User Not Found", userName);
        verify(userRepository, times(1)).findUserById("456");
    }

    @Test
    @DisplayName("Should register new user successfully")
    void registerUser_Success() {
        when(userRepository.findUserById("789")).thenReturn(null); // User does not exist initially

        boolean registered = userService.registerUser("789", "Bob");

        assertTrue(registered);
        // Verify that saveUser was called
        verify(userRepository, times(1)).saveUser("789", "Bob");
    }

    @Test
    @DisplayName("Should not register user if already exists")
    void registerUser_UserAlreadyExists() {
        when(userRepository.findUserById("101")).thenReturn("Charlie"); // User already exists

        boolean registered = userService.registerUser("101", "Charlie");

        assertFalse(registered);
        // Verify that saveUser was NOT called
        verify(userRepository, never()).saveUser(anyString(), anyString());
    }
}
```

#### Common Mistakes / Tips
- **Testing implementation details**: Focus on testing the public behavior of the unit, not its internal implementation.
- **Over-mocking**: Mock only necessary dependencies. If you mock too much, your tests become brittle and might not reflect real-world behavior.
- **Not using** `@BeforeEach`/`@BeforeAll` **for setup**: Leads to redundant code in test methods.
- **Mixing unit and integration tests**: Keep them separate. Unit tests should be fast and independent.
- **Forgetting to add test dependencies**: Ensure `junit-jupiter-api`, `junit-jupiter-engine`, `mockito-core`, `mockito-junit-jupiter` are in your `pom.xml` (Maven) or `build.gradle` (Gradle) with `test` scope.
- **Build tool basics**: Understand the core commands for Maven (`mvn clean install`, `mvn test`) and Gradle (`gradle clean build`, `gradle test`).
- **Artifacts**: Know what a JAR and WAR file are and when they are used.
- **Test coverage**: Aim for high test coverage, but don't just chase numbers. Focus on testing critical paths and edge cases.

---

## Java Frameworks (Basics)
### Explanation / Key Concepts
Frameworks provide a foundation for building applications, offering pre-built components, guidelines, and conventions to streamline development.
- **Spring Framework**: A comprehensive, open-source application development framework for enterprise Java.
  - **Inversion of Control (IOC)**: The framework controls the creation and lifecycle of objects, rather than the developer manually creating them.
  - **Dependency Injection (DI)**: A specific implementation of IOC where dependencies (objects a class needs) are provided to a class from the outside, rather than the class creating them itself. Achieved via constructor injection, setter injection, or field injection (`@Autowired`).
  - **Aspect-Oriented Programming (AOP)**: A programming paradigm that allows developers to modularize cross-cutting concerns (e.g., logging, security, transaction management) into separate modules called aspects.
  - **Spring Boot Basics**: A project built on top of the Spring Framework, designed to simplify the development of production-ready Spring applications.
    - **Convention over Configuration**: Minimizes boilerplate configuration.
    - **Auto-configuration**: Automatically configures Spring beans based on classpath, enabling quick setup.
    - **Embedded Servers**: Allows packaging applications as self-contained JARs with embedded Tomcat, Jetty, or Undertow.
    - **Starters**: Dependency descriptors that pull in all necessary transitive dependencies for a particular feature (e.g., `spring-boot-starter-web`)
- **Hibernate**: An open-source Object-Relational Mapping (ORM) framework
  - **ORM Concepts**: A technique that maps object models (Java classes) to relational database schemas, allowing developers to interact with the database using object-oriented principles rather than raw SQL.
  - **Annotations**: Used to map Java classes to database tables and properties to columns (e.g., `@Entity`, `@Table`, `@Id`, `@Column`, `@OneToMany`, `@ManyToOne`).
  - **Session Handling**: `Session` is the primary interface for interacting with the database in Hibernate. It's a lightweight, non-thread-safe object representing a single unit of work. `SessionFactory` creates Session instances.
- **REST APIs with Spring Boot**:
  - **REST (Representational State Transfer)**: An architectural style for designing networked applications. It's stateless, client-server, cacheable, and uses a uniform interface.
  - **RESTful APIs**: APIs that adhere to the REST principles
    - **Resources**: Identified by URLs (e.g., `/users`, `/products/123`).
    - **HTTP Methods**: Use standard HTTP methods for operations (`GET` for retrieve, `POST` for create, `PUT` for update/replace, `PATCH` for partial update, `DELETE` for remove).
    - **Stateless**: Each request from client to server contains all information needed to understand the request.
    - **Media Types**: Data exchanged typically in JSON or XML
- **Spring @RestController**: A convenience annotation that combines `@Controller` and `@ResponseBody`, indicating that the return values of methods should be directly bound to the web response body
- **@GetMapping, @PostMapping, etc**: Annotations for mapping HTTP requests to handler methods.
- **@PathVariable, @RequestParam, @RequestBody**: For extracting data from incoming requests.

#### Example Code Snippet (Spring Boot REST + basic Hibernate concept)
```java
// --- Spring Boot REST API Example ---
// Assuming a Maven/Gradle project with spring-boot-starter-web and spring-boot-starter-data-jpa dependencies

// 1. Entity (JPA/Hibernate)
// src/main/java/com/example/demo/model/Product.java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity // Marks this class as a JPA entity
public class Product {
    @Id // Marks this field as the primary key
    @GeneratedValue(strategy = GenerationType.IDENTITY) // Auto-generates ID
    private Long id;
    private String name;
    private double price;

    // Constructors, Getters, Setters
    public Product() {}

    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }

    @Override
    public String toString() {
        return "Product{" + "id=" + id + ", name='" + name + '\'' + ", price=" + price + '}';
    }
}

// 2. Repository (Spring Data JPA)
// src/main/java/com/example/demo/repository/ProductRepository.java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository // Marks this interface as a Spring Data JPA repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    // JpaRepository provides CRUD methods automatically
    // Custom query example:
    // List<Product> findByNameContaining(String name);
}

// 3. Service Layer
// src/main/java/com/example/demo/service/ProductService.java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.Optional;

@Service // Marks this class as a Spring service component
public class ProductService {
    private final ProductRepository productRepository;

    @Autowired // Dependency Injection: Spring injects ProductRepository
    public ProductService(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }

    public List<Product> getAllProducts() {
        return productRepository.findAll();
    }

    public Optional<Product> getProductById(Long id) {
        return productRepository.findById(id);
    }

    public Product createProduct(Product product) {
        return productRepository.save(product);
    }

    public Product updateProduct(Long id, Product productDetails) {
        Product product = productRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Product not found for this id :: " + id)); // Example error handling

        product.setName(productDetails.getName());
        product.setPrice(productDetails.getPrice());
        return productRepository.save(product);
    }

    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }
}

// 4. Controller (REST Endpoint)
// src/main/java/com/example/demo/controller/ProductController.java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController // Combination of @Controller and @ResponseBody
@RequestMapping("/api/products") // Base URL for all endpoints in this controller
public class ProductController {
    private final ProductService productService;

    @Autowired
    public ProductController(ProductService productService) {
        this.productService = productService;
    }

    @GetMapping // Maps HTTP GET requests to /api/products
    public List<Product> getAllProducts() {
        return productService.getAllProducts();
    }

    @GetMapping("/{id}") // Maps HTTP GET requests to /api/products/{id}
    public ResponseEntity<Product> getProductById(@PathVariable Long id) {
        return productService.getProductById(id)
                .map(ResponseEntity::ok) // If product found, return 200 OK with product
                .orElse(ResponseEntity.notFound().build()); // Else, return 404 Not Found
    }

    @PostMapping // Maps HTTP POST requests to /api/products
    public Product createProduct(@RequestBody Product product) {
        return productService.createProduct(product);
    }

    @PutMapping("/{id}") // Maps HTTP PUT requests to /api/products/{id}
    public ResponseEntity<Product> updateProduct(@PathVariable Long id, @RequestBody Product productDetails) {
        try {
            Product updatedProduct = productService.updateProduct(id, productDetails);
            return ResponseEntity.ok(updatedProduct);
        } catch (RuntimeException ex) {
            return ResponseEntity.notFound().build();
        }
    }

    @DeleteMapping("/{id}") // Maps HTTP DELETE requests to /api/products/{id}
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
        return ResponseEntity.noContent().build(); // Return 204 No Content
    }
}

// 5. Main Application Class
// src/main/java/com/example/demo/DemoApplication.java
// (Standard Spring Boot application starter)
/*
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // Combines @Configuration, @EnableAutoConfiguration, @ComponentScan
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
*/
```

#### Common Mistakes / Tips
- **Tight Coupling**: Without DI, classes become tightly coupled, making testing and maintenance difficult.
- **Boilerplate XML in Spring**: Spring Boot largely removes this. Emphasize the shift from XML to annotation-based and convention-over-configuration.
- **N+1 problem in Hibernate**: Occurs when fetching a collection or related entities, leading to one query for the parent and N additional queries for each child. Use eager fetching or join fetches (`@Fetch(FetchMode.JOIN)`) to mitigate.
- **Transaction Management**: Crucial for database operations. Spring provides `@Transactional` for declarative transaction management.
- **REST API misuses**: Not adhering to HTTP methods correctly, using non-standard status codes, or not keeping APIs stateless.
- **Security**: Don't forget basic security considerations (authentication, authorization) even in basic framework discussions. Spring Security is a powerful framework for this.
- **Understanding `@Autowired` vs. Constructor Injection**: While `@Autowired` on fields is convenient, constructor injection is generally preferred for better testability and immutability.
- **Spring Data JPA**: Explain how it simplifies repository creation, reducing boilerplate code significantly.

---

## Database Connectivity
### Explanation / Key Concepts
- **JDBC Basics (Java Database Connectivity)**: A Java API for connecting and executing queries with databases. It provides a standard way to interact with different relational databases (e.g., MySQL, PostgreSQL, Oracle) using their respective JDBC drivers
    - **Core Components**:
      - `DriverManager`: Manages a list of JDBC drivers.
      - `Connection`: Represents a connection to a database.
      - `Statement`: Used to execute static SQL queries.
      - `PreparedStatement`: Used to execute pre-compiled SQL queries with parameters. Prevents SQL Injection and improves performance for repeated queries.
      - `CallableStatement`: Used to execute stored procedures.
      - `ResultSet`: Represents the result of a database query, allowing iteration over rows and columns.
    - **Connection Pooling**: A technique for managing and reusing database connections. Instead of opening and closing a new connection for every request (which is expensive), a pool of established connections is maintained.
      - **Benefits**: Improves performance (reduces overhead), better resource utilization, easier management.
      - **Popular Pools**: HikariCP (fastest), Apache DBCP, C3P0.
    - **Transactions and Batch Processing**:
      - **Transactions**: A sequence of operations performed as a single logical unit of work. They adhere to ACID properties (Atomicity, Consistency, Isolation, Durability).
        - `conn.setAutoCommit(false);`
        - `conn.commit();`
        - `conn.rollback();`
      - **Batch Processing**: Executing multiple SQL statements (typically `INSERT`, `UPDATE`, `DELETE`) in a single round trip to the database.
        - `stmt.addBatch(sql);`
        - `stmt.executeBatch();`
        - Significantly improves performance for bulk operations by reducing network overhead.

#### Example Code Snippet (Basic JDBC, PreparedStatement, Transaction)
```java
import java.sql.*;

public class JdbcDemo {

    private static final String JDBC_URL = "jdbc:h2:mem:testdb"; // In-memory H2 database
    private static final String USER = "sa";
    private static final String PASSWORD = "";

    public static void main(String[] args) {
        createTable();
        insertDataWithPreparedStatement("Alice", 30);
        insertDataWithPreparedStatement("Bob", 25);
        queryData();
        updateDataWithTransaction("Alice", 31);
        queryData();
        batchInsertData();
        queryData();
    }

    private static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(JDBC_URL, USER, PASSWORD);
    }

    private static void createTable() {
        try (Connection conn = getConnection();
             Statement stmt = conn.createStatement()) {
            String sql = "CREATE TABLE IF NOT EXISTS users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255), age INT)";
            stmt.execute(sql);
            System.out.println("Table 'users' created or already exists.");
        } catch (SQLException e) {
            System.err.println("Error creating table: " + e.getMessage());
        }
    }

    private static void insertDataWithPreparedStatement(String name, int age) {
        String sql = "INSERT INTO users (name, age) VALUES (?, ?)";
        try (Connection conn = getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, name);
            pstmt.setInt(2, age);
            int rowsAffected = pstmt.executeUpdate();
            System.out.println("Inserted " + rowsAffected + " row: " + name);
        } catch (SQLException e) {
            System.err.println("Error inserting data: " + e.getMessage());
        }
    }

    private static void queryData() {
        String sql = "SELECT id, name, age FROM users";
        try (Connection conn = getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {
            System.out.println("\n--- User Data ---");
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                int age = rs.getInt("age");
                System.out.println("ID: " + id + ", Name: " + name + ", Age: " + age);
            }
            System.out.println("-----------------\n");
        } catch (SQLException e) {
            System.err.println("Error querying data: " + e.getMessage());
        }
    }

    private static void updateDataWithTransaction(String name, int newAge) {
        String sql = "UPDATE users SET age = ? WHERE name = ?";
        Connection conn = null;
        try {
            conn = getConnection();
            conn.setAutoCommit(false); // Start transaction

            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
                pstmt.setInt(1, newAge);
                pstmt.setString(2, name);
                int rowsAffected = pstmt.executeUpdate();
                System.out.println("Updated " + rowsAffected + " rows for " + name);

                // Simulate an error to demonstrate rollback
                // if (name.equals("Alice")) {
                //     throw new SQLException("Simulated error for Alice!");
                // }

                conn.commit(); // Commit transaction
                System.out.println("Transaction committed.");
            } catch (SQLException e) {
                if (conn != null) {
                    conn.rollback(); // Rollback on error
                    System.err.println("Transaction rolled back due to error: " + e.getMessage());
                }
            }
        } catch (SQLException e) {
            System.err.println("Database connection error: " + e.getMessage());
        } finally {
            if (conn != null) {
                try {
                    conn.setAutoCommit(true); // Reset auto-commit mode
                    conn.close();
                } catch (SQLException e) {
                    System.err.println("Error closing connection: " + e.getMessage());
                }
            }
        }
    }

    private static void batchInsertData() {
        String sql = "INSERT INTO users (name, age) VALUES (?, ?)";
        try (Connection conn = getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {
            conn.setAutoCommit(false); // Enable manual transaction for batch

            pstmt.setString(1, "Zara");
            pstmt.setInt(2, 28);
            pstmt.addBatch();

            pstmt.setString(1, "Yash");
            pstmt.setInt(2, 32);
            pstmt.addBatch();

            pstmt.setString(1, "Xavier");
            pstmt.setInt(2, 40);
            pstmt.addBatch();

            int[] updateCounts = pstmt.executeBatch();
            conn.commit(); // Commit the batch
            System.out.println("Batch insert successful. Rows affected: " + Arrays.toString(updateCounts));

        } catch (SQLException e) {
            System.err.println("Error during batch insert: " + e.getMessage());
        }
    }
}
```
 
#### Common Mistakes / Tips
- **SQL Injection vulnerabilities**: Always use `PreparedStatement` with parameters, never concatenate user input directly into SQL strings.
- **Resource leaks**: Forgetting to close `Connection`, `Statement`, `ResultSet` objects. Always use `try-with-resources` (Java 7+) for JDBC resources.
- **Transaction management**: Not understanding setAutoCommit(false), commit(), and rollback(). Ensure transactions are used correctly to maintain data integrity.
- **Performance for bulk operations**: Not using batch updates for multiple INSERT/UPDATE/DELETE operations.
- **Hardcoding credentials**: Never hardcode database credentials in production code. Use configuration files, environment variables, or secret management tools.
- **Choosing the right driver**: Ensure you have the correct JDBC driver for your specific database in your classpath.
- **Error handling**: Properly catch and log `SQLExceptions`.
- **ORM over plain JDBC**: For complex applications, an ORM framework like Hibernate or JPA (Spring Data JPA) is preferred over raw JDBC due to reduced boilerplate, object-oriented approach, and built-in features like caching and transaction management. However, for simple, high-performance tasks, raw JDBC might be chosen.
   
---

## Real-world Java System Design
### Explanation / Key Concepts
System design questions evaluate your ability to apply your knowledge of Java and related technologies to build scalable, reliable, and maintainable applications.

- **Layered Architecture (N-Tier Architecture)**: A common software design pattern that divides an application into logical layers, each with specific responsibilities. This promotes separation of concerns and modularity.
  - **Presentation Layer (Controller/API Layer**: Handles user interactions, receives requests (e.g., HTTP requests in a web application), validates input, and orchestrates calls to the service layer. Returns responses to the client. (e.g., Spring `@RestController`)
  - **Service Layer (Business Logic Layer)**: Contains the core business rules and logic of the application. It orchestrates calls to the data access layer, performs computations, validations, and manages transactions. (e.g., Spring `@Service`)
  - **Data Access Layer (DAO / Repository Layer)**: Provides an abstraction over the underlying data store (e.g., database). It handles all data persistence operations (CRUD). (e.g., Spring Data JPA `Repository`).
  - **Domain Layer (Model Layer)**: Represents the core business entities and their relationships. Contains POJOs (Plain Old Java Objects) or JPA entities. (e.g., Hibernate `@Entity` classes).
- **DTOs (Data Transfer Objects)**: Objects used to transfer data between different layers of an application, or between a client and a server.
  - Contain only data, no business logic.
  - Often used to aggregate data from multiple domain objects or to expose only a subset of data.
  - Can help avoid "over-fetching" or exposing sensitive data.
- **POJOs (Plain Old Java Objects)**: Simple Java objects that do not implement any specific framework interfaces or extend any framework classes. They are typically used to represent business entities
  - Promote loose coupling and testability.
  - JPA entities are often POJOs annotated with persistence metadata.
- **Entity Relationships (Database & ORM)**: How different entities in your domain model are related.
  - **One-to-One**: One instance of Entity A corresponds to one instance of Entity B. (e.g., `User` and `UserProfile`)
  - **One-to-Many**: One instance of Entity A corresponds to multiple instances of Entity B. (e.g., `Department` and `Employees`)
  - **Many-to-One**: Multiple instances of Entity B correspond to one instance of Entity A. (Inverse of One-to-Many)
  - **Many-to-Many**: Multiple instances of Entity A correspond to multiple instances of Entity B. (e.g., `Student` and `Course`)
  - **Mapping**: How these relationships are mapped using JPA/Hibernate annotations (`@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`).
- **REST vs SOAP**: Two popular architectural styles for building web services
  - **REST (Representational State Transfer)**:
    - Lightweight, Stateless, Client-Server, Cacheable.
    - Uses standard HTTP methods (GET, POST, PUT, DELETE).
    - Data format: JSON (prevalent), XML, plain text.
    - Loosely coupled, more flexible.
    - Ideal for mobile applications, web browsers, public APIs.
  - **SOAP (Simple Object Access Protocol)**:
    - Standardized XML-based messaging protocol.
    - Strict contract (WSDL - Web Services Description Language).
    - Relies on XML for message format and HTTP/SMTP for transport.
    - Stateful or stateless.
    - Heavyweight, complex to implement.
    - Built-in error handling and security (WS-Security).
    - Ideal for enterprise applications with strict requirements for security, transactions, and formal contracts.

#### Example Scenario Walkthrough (E-Commerce Order Processing)
**Problem**: Design the backend for an online store's order processing system.

1. **Entities (Domain Layer - POJOs/JPA Entities):**
- `User`: id, name, email, password (hashed), address
- `Product`: id, name, description, price, stockQuantity
- `Order`: id, userId, orderDate, status (e.g., PENDING, SHIPPED, DELIVERED), totalAmount
- `OrderItem`: id, orderId, productId, quantity, unitPrice

2. **Relationships (JPA Mappings):**
- `User` 1:N `Order` (One user can have many orders)
  - `@ManyToOne` in `Order` for `user` field.
  - `@OneToMany` in `User` for `orders` list (optional, for bidirectional).
- `Order` 1:N `OrderItem` (One order can have many order items)
  - `@ManyToOne` in `OrderItem` for `order` field.
  - `@OneToMany` in `Order` for `orderItems` list.
- `Product` 1:N `OrderItem` (One product can be in many order items)
  - `@ManyToOne` in `OrderItem` for `product` field

3. **Layered Architecture:**
- **Presentation Layer (REST Controllers):**
  - `UserController`: `/api/users` (register, login, get user profile)
  - `ProductController`: `/api/products` (get all, get by id, search products)
  - `OrderController`: `/api/orders` (place order, get order by id, get user orders)
  - Input: JSON requests. Output: JSON responses.
  - Uses `@RequestBody`, `@PathVariable`, `@RequestParam`.
- **Service Layer (Business Logic):**
  - `UserService`: Handles user registration, authentication, profile updates.
  - `ProductService`: Manages product catalog, stock updates.
  - `OrderService`:
    - Takes `PlaceOrderRequestDTO` (containing `userId`, `productIds`, quantities).
    - Validates `userId` and product availability/stock.
    - Calculates total price.
    - Creates `Order` and `OrderItem` entities.
    - Updates `Product` stock quantities.
    - Persists entities via repositories.
    - Handles transaction management (`@Transactional`).
    - Returns `OrderConfirmationDTO`.
- **Data Access Layer (Repositories - Spring Data JPA):**
  - `UserRepository`: Extends `JpaRepository<User, Long>`.
  - `ProductRepository`: Extends `JpaRepository<Product, Long>`.
  - `OrderRepository`: Extends `JpaRepository<Order, Long>`.
  - `OrderItemRepository`: Extends `JpaRepository<OrderItem, Long>`.
  - Handles CRUD operations
- **DTOs**:
  - `UserRegistrationDTO` (for `/api/users/register`): email, password, name
  - `LoginRequestDTO`: email, password
  - `PlaceOrderRequestDTO`: userId, List&lt;ItemDTO> (productId, quantity)
  - `OrderConfirmationDTO`: orderId, totalAmount, status
  - `ProductDTO` (for exposing product details to client): id, name, price

4. **Request Flow (Place Order):**
    1. Client: Sends `POST` request to `/api/orders` with `PlaceOrderRequestDTO` (JSON).
    2. `OrderController`:
       - Receives `PlaceOrderRequestDTO`.
       - Calls orderService`.placeOrder(placeOrderRequestDTO)`.
    3. `OrderService`:
       - Starts a transaction (`@Transactional`).
       - Validates `userId` (using `userRepository`).
       - For each item in `PlaceOrderRequestDTO`:
         - Retrieves `Product` from `productRepository`.
         - Checks stock.
         - Calculates `unitPrice`.
         - Decrements `product.stockQuantity` and saves (`productRepository.save()`).
       - Creates `Order` entity, sets `userId`, `orderDate`, `status`, `totalAmount`.
       - Creates `OrderItem` entities for each product.
       - Saves `Order` and `OrderItem` entities (`orderRepository.save()`, `orderItemRepository.saveAll()`).
       - Commits transaction.
       - Converts `Order` entity to `OrderConfirmationDTO`.
       - Returns `OrderConfirmationDTO`.
    4. `OrderController`:
       - Receives `OrderConfirmationDTO`.
       - Returns `ResponseEntity.ok(orderConfirmationDTO)` (200 OK) to the client.
       - If an error occurred (e.g., insufficient stock), the `OrderService` would throw an exception, caught by a global exception handler in the controller layer, returning an appropriate error response (e.g., 400 Bad Request)

#### Common Mistakes / Tips
- **Mixing concerns**: Controllers should not contain business logic; services should not handle direct data access (delegate to repositories).
- **Anemic Domain Model**: Entities with only getters/setters and no behavior. Try to put business logic into the domain entities themselves or the service layer.
- **Ignoring NFRs**: Don't just focus on functional requirements. Think about scalability (stateless services, caching), security (authentication, authorization, input validation), performance (indexing, query optimization), logging, error handling.
- **Over-normalization/De-normalization**: Understand database normalization principles but also know when to de-normalize for performance.
- **Synchronous vs. Asynchronous**: For long-running operations (e.g., sending emails after an order), consider asynchronous processing (e.g., using message queues, Spring's @Async).
- **Microservices vs. Monolith**: Be ready to discuss the trade-offs and when to choose one over the other (monolith first, microservices when needed for scale/team size).
- **Caching**: When and where to implement caching (e.g., Redis, Spring Cache).
- **Observability**: How to monitor your application (logging, metrics, tracing).