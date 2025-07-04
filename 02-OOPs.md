Object-Oriented Programming (OOP) is a programming paradigm that organizes software design around data, or objects, rather than functions and logic. Java is a quintessential OOP language, meaning almost everything in Java revolves around classes and objects.
The Purpose of OOP: Solving Problems in Software Development
Before OOP, procedural programming was prevalent. In procedural programming, programs are organized into functions or procedures that operate on data. This approach works well for small, simple programs, but as applications grow in size and complexity, several issues emerge:
 * Maintainability: Changes in one part of the code can have unforeseen side effects in other parts, making it difficult to maintain and debug.
 * Scalability: Adding new features often requires significant changes to existing code, making the system hard to scale.
 * Modularity: Code reusability is limited as functions are tightly coupled with the data they operate on.
 * Real-world Modeling: Procedural programming often struggles to model complex real-world entities and their interactions naturally.
OOP addresses these problems by providing a structured and organized way to design software. Its core idea is to bundle data and the methods (functions) that operate on that data into self-contained units called objects.
OOP vs. Procedural Programming
| Feature | Procedural Programming | Object-Oriented Programming |
|---|---|---|
| Paradigm | Focuses on procedures/functions and sequential logic. | Focuses on objects, which are instances of classes. |
| Data & Code | Separates data from functions that operate on it. | Bundles data and methods into a single unit (object). |
| Design | Top-down approach, breaks problems into functions. | Bottom-up approach, builds around objects. |
| Reusability | Limited reusability; functions often specific to data. | High reusability through inheritance and polymorphism. |
| Maintainability | Difficult to maintain in large projects. | Easier to maintain due to modularity and encapsulation. |
| Complexity | Can become unmanageable for complex systems. | Manages complexity by breaking it into smaller, manageable objects. |
| Real-world | Less intuitive for modeling real-world entities. | More intuitive for modeling real-world entities. |
Why OOP is Essential for Java Applications
OOP is fundamental to building scalable, maintainable, and modular applications in Java for the following reasons:
 * Modularity: OOP promotes breaking down a complex system into smaller, self-contained modules (objects). Each module is responsible for a specific task, making the system easier to understand, develop, and debug.
 * Reusability: Through concepts like inheritance, objects can reuse existing code, reducing development time and effort.
 * Maintainability: Encapsulation and modularity make it easier to locate and fix bugs or introduce changes without affecting other parts of the system.
 * Scalability: New features can be added by creating new classes and objects without significantly altering existing code.
 * Flexibility and Extensibility: Polymorphism allows for flexible and extensible designs where new types can be added without modifying existing client code.
 * Real-World Modeling: OOP's object-centric approach naturally maps to real-world entities and their interactions, leading to more intuitive and understandable software designs.
Object vs. Class: The Blueprint-Instance Model in Java
In Java OOP, the relationship between a class and an object is analogous to that of a blueprint and the actual building constructed from that blueprint.
 * Class: A class is a blueprint, a template, or a prototype that defines the structure (fields/attributes) and behavior (methods) that objects of that class will have. It's a logical construct that doesn't consume memory until an object is created from it. Think of it as the design for a car, specifying that all cars will have wheels, an engine, and the ability to accelerate or brake.
   // Car.java - This is a class
public class Car {
    // Fields (attributes)
    String make;
    String model;
    int year;
    String color;

    // Methods (behaviors)
    public void start() {
        System.out.println(make + " " + model + " is starting.");
    }

    public void stop() {
        System.out.println(make + " " + model + " is stopping.");
    }

    public void accelerate() {
        System.out.println(make + " " + model + " is accelerating.");
    }
}

 * Object: An object is a concrete instance of a class. It's a real-world entity that exists in memory and has specific values for the attributes defined in its class. You can create multiple objects from a single class, and each object will have its own unique set of attribute values. Using the car analogy, an object would be a specific Toyota Camry with a red color, built in 2023, or a blue Ford Mustang built in 2024.
   // Main.java
public class Main {
    public static void main(String[] args) {
        // Creating objects (instances) of the Car class
        Car myCar = new Car(); // myCar is an object
        myCar.make = "Toyota";
        myCar.model = "Camry";
        myCar.year = 2023;
        myCar.color = "Red";
        myCar.start();

        Car yourCar = new Car(); // yourCar is another object
        yourCar.make = "Ford";
        yourCar.model = "Mustang";
        yourCar.year = 2024;
        yourCar.color = "Blue";
        yourCar.accelerate();
    }
}

   In this example, myCar and yourCar are distinct objects, each with its own state (values for make, model, year, color).
The Four Main Pillars of OOP
The four main pillars of OOP are Encapsulation, Inheritance, Polymorphism, and Abstraction. These principles work together to provide the benefits of OOP.
1. Encapsulation: Hiding Implementation Details
Concept: Encapsulation is the bundling of data (fields) and the methods that operate on that data into a single unit (a class). It also involves restricting direct access to some of an object's components, typically its internal state. This is often referred to as "data hiding."
How Java Uses it:
Java uses classes to bundle data and methods. Access modifiers (like private, public) are used to control the visibility of fields and methods.
 * Private Fields: By declaring fields as private, their direct access from outside the class is prevented. This hides the internal representation of the object.
 * Public Getters/Setters (Accessor/Mutator methods): To interact with the private data, public methods (getters to read data, setters to modify data) are provided. These methods act as controlled interfaces to the object's internal state.
Example (Beginner Level): BankAccount
public class BankAccount {
    private String accountNumber; // private field
    private double balance;       // private field

    public BankAccount(String accountNumber, double initialBalance) {
        this.accountNumber = accountNumber;
        // Data validation in setter for balance
        setBalance(initialBalance);
    }

    // Public getter for accountNumber
    public String getAccountNumber() {
        return accountNumber;
    }

    // Public getter for balance
    public double getBalance() {
        return balance;
    }

    // Public setter for balance with data validation
    public void setBalance(double balance) {
        if (balance >= 0) { // Ensures balance is not negative
            this.balance = balance;
        } else {
            System.out.println("Invalid balance. Balance cannot be negative.");
        }
    }

    public void deposit(double amount) {
        if (amount > 0) {
            this.balance += amount;
            System.out.println("Deposited: " + amount + ". New balance: " + this.balance);
        } else {
            System.out.println("Deposit amount must be positive.");
        }
    }

    public void withdraw(double amount) {
        if (amount > 0 && this.balance >= amount) {
            this.balance -= amount;
            System.out.println("Withdrew: " + amount + ". New balance: " + this.balance);
        } else if (amount <= 0) {
            System.out.println("Withdrawal amount must be positive.");
        } else {
            System.out.println("Insufficient funds. Current balance: " + this.balance);
        }
    }

    public static void main(String[] args) {
        BankAccount myAccount = new BankAccount("12345", 1000.0);
        System.out.println("Account Number: " + myAccount.getAccountNumber());
        System.out.println("Current Balance: " + myAccount.getBalance());

        myAccount.deposit(500.0);
        myAccount.withdraw(200.0);
        myAccount.withdraw(2000.0); // Insufficient funds
        myAccount.setBalance(-100.0); // Invalid balance (data validation)
        System.out.println("Final Balance: " + myAccount.getBalance());
    }
}

Benefits in Real-World Scenarios:
 * Data Validation: Setters can include logic to validate input data before assigning it to the private fields, ensuring that the object's state remains valid. (e.g., setBalance in BankAccount).
 * Immutability (Advanced): By making fields private and providing only public getters (no setters), and initializing all fields in the constructor, you can create immutable objects. Once an immutable object is created, its state cannot be changed. This is crucial for thread safety and simplifies reasoning about object state.
   public final class ImmutablePerson { // final class prevents inheritance
    private final String name;       // final field
    private final int age;           // final field

    public ImmutablePerson(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    // No setters, thus immutable
    public static void main(String[] args) {
        ImmutablePerson person = new ImmutablePerson("Alice", 30);
        System.out.println("Name: " + person.getName() + ", Age: " + person.getAge());
        // person.name = "Bob"; // Compile-time error: cannot assign to final variable
    }
}

 * Control over Access: Encapsulation allows you to control how external code interacts with your object's data, protecting its integrity.
 * Flexibility: You can change the internal implementation of a class without affecting the code that uses the class, as long as the public interface (getters/setters/public methods) remains the same.
2. Inheritance: Code Reusability and Specialization
Concept: Inheritance is a mechanism where one class (the subclass or child class) acquires the fields and methods of another class (the superclass or parent class). It represents an "IS-A" relationship.
How Java Uses it:
Java uses the extends keyword to establish an inheritance relationship. A subclass can inherit public and protected members of its superclass.
Example (Intermediate Level): Animal - Dog - Cat Hierarchy
// Superclass
class Animal {
    String name;
    int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("Animal constructor called for " + name);
    }

    public void eat() {
        System.out.println(name + " is eating.");
    }

    public void sleep() {
        System.out.println(name + " is sleeping.");
    }
}

// Subclass
class Dog extends Animal {
    String breed;

    public Dog(String name, int age, String breed) {
        super(name, age); // Calls the Animal class constructor
        this.breed = breed;
        System.out.println("Dog constructor called for " + name);
    }

    public void bark() {
        System.out.println(name + " barks loudly!");
    }

    // Method overriding: providing a specific implementation for an inherited method
    @Override
    public void eat() {
        System.out.println(name + " (a " + breed + ") is eating dog food.");
    }
}

// Another Subclass
class Cat extends Animal {
    String color;

    public Cat(String name, int age, String color) {
        super(name, age); // Calls the Animal class constructor
        this.color = color;
        System.out.println("Cat constructor called for " + name);
    }

    public void meow() {
        System.out.println(name + " meows softly.");
    }

    @Override
    public void eat() {
        System.out.println(name + " (a " + color + " cat) is eating fish.");
    }
}

public class InheritanceDemo {
    public static void main(String[] args) {
        Dog myDog = new Dog("Buddy", 3, "Golden Retriever");
        myDog.eat();    // Calls overridden eat() from Dog
        myDog.bark();
        myDog.sleep();  // Inherited from Animal

        System.out.println("---");

        Cat myCat = new Cat("Whiskers", 5, "Tabby");
        myCat.eat();    // Calls overridden eat() from Cat
        myCat.meow();
        myCat.sleep();  // Inherited from Animal
    }
}

Role of super keyword:
 * super(): Used to call the constructor of the superclass. This must be the first statement in the subclass constructor.
 * super.methodName(): Used to call a method of the superclass, particularly useful when a method is overridden in the subclass and you still want to execute the superclass's version.
 * super.fieldName: Used to access a field of the superclass (less common as fields are often private).
Method Overriding:
When a subclass provides its own specific implementation for a method that is already defined in its superclass, it's called method overriding. The @Override annotation is optional but recommended as it helps the compiler detect errors if the method signature doesn't match the superclass method.
Constructors in Inheritance:
Subclass constructors must implicitly or explicitly call a superclass constructor. If you don't explicitly call super(), Java automatically inserts a call to the no-argument constructor of the superclass. If the superclass only has parameterized constructors, you must explicitly call one using super().
Use Cases:
 * Code Reusability: Common logic and attributes are defined once in the superclass and reused by multiple subclasses.
 * Specialization: Subclasses can add new functionalities or override inherited ones to provide specific behaviors.
 * Polymorphism: A fundamental enabler for runtime polymorphism.
Pros and Cons:
Pros:
 * Code Reusability: Reduces code duplication.
 * Modularity: Organizes code into logical hierarchies.
 * Polymorphism: Enables flexible and extensible designs.
Cons:
 * Tight Coupling: Subclasses are tightly coupled to their superclass. Changes in the superclass can affect all subclasses.
 * Fragile Base Class Problem: Modifying a superclass can inadvertently break the functionality of its subclasses, even if the subclasses don't directly depend on the modified part.
 * Single Inheritance (in Java): A class can only inherit from one direct superclass, which can limit design flexibility in some scenarios.
When to Favor Composition over Inheritance (HAS-A vs IS-A):
 * IS-A Relationship (Inheritance): Use inheritance when a subclass truly is a type of its superclass (e.g., Dog IS-A Animal).
 * HAS-A Relationship (Composition): Use composition when a class has a relationship with another class (e.g., a Car HAS-A Engine). Instead of inheriting, one class contains an instance of another class. Composition offers more flexibility, loose coupling, and avoids the fragile base class problem.
   // Composition Example
class Engine {
    public void start() {
        System.out.println("Engine started.");
    }
}

class CarComposition {
    private Engine engine; // Car HAS-A Engine

    public CarComposition() {
        this.engine = new Engine();
    }

    public void start() {
        engine.start(); // Delegate the start operation to the Engine object
        System.out.println("Car with composition started.");
    }

    public static void main(String[] args) {
        CarComposition car = new CarComposition();
        car.start();
    }
}

3. Polymorphism: "Many Forms"
Concept: Polymorphism means "many forms." In OOP, it refers to the ability of an object to take on many forms. Specifically, it allows objects of different classes to be treated as objects of a common type (their superclass or interface).
How Java Supports it:
Java supports two main types of polymorphism:
a) Compile-time Polymorphism (Method Overloading)
 * Concept: Achieved when a class has multiple methods with the same name but different parameter lists (number, type, or order of arguments). The compiler determines which method to call based on the arguments provided at compile time.
 * Example (Beginner Level):
   class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) { // Overloaded method
        return a + b;
    }

    public int add(int a, int b, int c) { // Overloaded method
        return a + b + c;
    }

    public static void main(String[] args) {
        Calculator calc = new Calculator();
        System.out.println(calc.add(5, 10));         // Calls int add(int, int)
        System.out.println(calc.add(5.5, 10.5));     // Calls double add(double, double)
        System.out.println(calc.add(1, 2, 3));       // Calls int add(int, int, int)
    }
}

b) Runtime Polymorphism (Method Overriding)
 * Concept: Achieved through method overriding. When an overridden method is called, the JVM determines which version of the method (from the superclass or subclass) to execute at runtime, based on the actual type of the object. This is also known as Dynamic Method Dispatch or Late Binding.
 * Example (Intermediate Level): Using the Animal hierarchy from inheritance.
   // Reusing Animal, Dog, Cat classes from Inheritance section
public class RuntimePolymorphismDemo {
    public static void main(String[] args) {
        Animal myAnimal1 = new Dog("Max", 2, "Labrador"); // Animal reference, Dog object
        Animal myAnimal2 = new Cat("Kitty", 4, "Siamese"); // Animal reference, Cat object
        Animal myAnimal3 = new Animal("Generic Animal", 1); // Animal reference, Animal object

        myAnimal1.eat(); // Calls Dog's eat() method at runtime
        myAnimal2.eat(); // Calls Cat's eat() method at runtime
        myAnimal3.eat(); // Calls Animal's eat() method at runtime

        // Upcasting: Treating a subclass object as its superclass type
        // Animal animalRef = new Dog(...); is an example of upcasting.
        // It's safe and implicit. You can only call methods defined in the Animal class.

        // Downcasting: Treating a superclass reference as a subclass type
        // Requires explicit cast and `instanceof` for type safety.
        Animal genericAnimal = new Dog("Rocky", 6, "Bulldog");
        if (genericAnimal instanceof Dog) { // Check if it's actually a Dog object
            Dog specificDog = (Dog) genericAnimal; // Downcasting
            specificDog.bark(); // Now you can call Dog-specific methods
        } else {
            System.out.println("genericAnimal is not a Dog.");
        }

        // Demonstrating `instanceof` with another example
        Animal anotherAnimal = new Cat("Smokey", 2, "Black");
        if (anotherAnimal instanceof Dog) {
            Dog anotherDog = (Dog) anotherAnimal; // This would cause ClassCastException without instanceof
            anotherDog.bark();
        } else {
            System.out.println("anotherAnimal is not a Dog, it's a Cat.");
        }
    }
}

Interfaces and Polymorphism (Advanced):
Interfaces play a crucial role in achieving polymorphism in Java by defining contracts. Different classes can implement the same interface, providing their own implementations of the interface methods. This allows you to treat objects of different classes uniformly, as long as they implement the same interface.
Example: Movable Interface
interface Movable {
    void move();
}

class Car implements Movable {
    @Override
    public void move() {
        System.out.println("Car drives on the road.");
    }
}

class Bicycle implements Movable {
    @Override
    public void move() {
        System.out.println("Bicycle pedals on the path.");
    }
}

class Plane implements Movable {
    @Override
    public void move() {
        System.out.println("Plane flies in the sky.");
    }
}

public class InterfacePolymorphismDemo {
    public static void main(String[] args) {
        Movable[] movables = new Movable[3];
        movables[0] = new Car();
        movables[1] = new Bicycle();
        movables[2] = new Plane();

        for (Movable m : movables) {
            m.move(); // Polymorphic call: different move() implementations are called
        }
    }
}

Here, movables is an array of Movable interface types. When m.move() is called, the actual move() method of the Car, Bicycle, or Plane object is executed at runtime. This demonstrates how interfaces enable powerful polymorphic behavior.
4. Abstraction: Defining Contracts and Hiding Details
Concept: Abstraction is the process of hiding the complex implementation details and showing only the essential features of an object. It focuses on "what an object does" rather than "how it does it."
How Java Uses it:
Java achieves abstraction using abstract classes and interfaces.
a) Abstract Classes: Partial Implementations
 * Concept: A class declared with the abstract keyword. It can have both abstract (without implementation) and concrete (with implementation) methods. Abstract classes cannot be instantiated directly; they must be subclassed, and the subclasses must provide implementations for all abstract methods (unless the subclass is also abstract).
 * Purpose: To provide a common base for related subclasses, sharing some common behavior while forcing subclasses to implement specific behaviors.
 * Example (Intermediate Level): Shape
   // Abstract class
abstract class Shape {
    String color;

    public Shape(String color) {
        this.color = color;
    }

    // Abstract method - must be implemented by concrete subclasses
    public abstract double calculateArea();

    // Concrete method - has implementation
    public void displayColor() {
        System.out.println("This shape is " + color);
    }
}

class Circle extends Shape {
    double radius;

    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}

class Rectangle extends Shape {
    double length;
    double width;

    public Rectangle(String color, double length, double width) {
        super(color);
        this.length = length;
        this.width = width;
    }

    @Override
    public double calculateArea() {
        return length * width;
    }
}

public class AbstractClassDemo {
    public static void main(String[] args) {
        // Shape myShape = new Shape("Red"); // Compile-time error: Cannot instantiate abstract class

        Circle circle = new Circle("Blue", 5.0);
        System.out.println("Circle Area: " + circle.calculateArea());
        circle.displayColor();

        Rectangle rectangle = new Rectangle("Green", 4.0, 6.0);
        System.out.println("Rectangle Area: " + rectangle.calculateArea());
        rectangle.displayColor();

        // Polymorphism with abstract class
        Shape[] shapes = new Shape[2];
        shapes[0] = circle;
        shapes[1] = rectangle;

        for (Shape s : shapes) {
            System.out.println("Area of " + s.color + " shape: " + s.calculateArea());
        }
    }
}

b) Interfaces: Pure Abstraction (Contracts)
 * Concept: A blueprint of a class. It can contain method signatures (abstract methods), default methods, static methods, and private methods (Java 9+). Interfaces provide a way to achieve 100% abstraction and support multiple inheritance of type (not implementation). A class implements an interface.
 * Purpose: To define a contract that implementing classes must adhere to. They specify "what to do" without specifying "how to do it."
 * Example (Intermediate Level): Printable
   interface Printable {
    void print(); // Abstract method (implicitly public abstract)

    // Default method (Java 8+) - provides a default implementation
    default void showInfo() {
        System.out.println("This object is printable.");
    }

    // Static method (Java 8+) - belongs to the interface itself, not instances
    static void printGuide() {
        System.out.println("Printing Guide: Ensure printer is on.");
    }

    // Private method (Java 9+) - helper for default methods
    private void connectToPrinter() {
        System.out.println("Connecting to printer...");
    }

    default void printWithConnection() {
        connectToPrinter(); // Using private method
        print();
    }
}

class Document implements Printable {
    @Override
    public void print() {
        System.out.println("Printing a document.");
    }
}

class Photo implements Printable {
    @Override
    public void print() {
        System.out.println("Printing a photo.");
    }
}

public class InterfaceDemo {
    public static void main(String[] args) {
        Printable doc = new Document();
        doc.print();
        doc.showInfo();
        doc.printWithConnection();

        Printable photo = new Photo();
        photo.print();

        Printable.printGuide(); // Calling static method on interface
    }
}

Differences between Abstract Classes vs. Interfaces:
| Feature | Abstract Class | Interface |
|---|---|---|
| Methods | Can have abstract and concrete methods. | Can have abstract, default, static, and private methods (Java 8/9+). |
| Fields | Can have instance and static fields (any access modifier). | Fields are implicitly public static final. |
| Constructors | Can have constructors. | Cannot have constructors. |
| Inheritance | A class can extend only one abstract class. | A class can implement multiple interfaces (multiple inheritance of type). |
| Access Modifiers | Can have any access modifier for members. | Methods are implicitly public abstract (before default/static). |
| Purpose | Defines a common base for subclasses, with shared implementation. | Defines a contract/API. |
Real-world Design Examples for Abstraction:
 * Payment Gateway: An abstract class PaymentGateway could define abstract methods like processPayment() and refund(), with concrete implementations for common tasks. Subclasses like PayPalGateway and StripeGateway would implement the specific payment processing logic.
 * Data Access Layer (DAO): An interface UserDao might define methods like createUser(), getUserById(), updateUser(), deleteUser(). Different concrete implementations (e.g., JdbcUserDao, HibernateUserDao) can then be swapped without affecting the client code that uses the UserDao interface. This is a common pattern in enterprise applications.
Deeper OOP Topics
Constructor Overloading and Chaining
Constructor Overloading: A class can have multiple constructors with different parameter lists, similar to method overloading. This allows creating objects in different ways.
Constructor Chaining:
 * this(...): Used to call another constructor within the same class. This is useful to avoid code duplication among constructors.
 * super(...): As discussed, used to call a superclass constructor.
Example:
class Person {
    String name;
    int age;
    String address;

    // Constructor 1: Basic
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("Person (name, age) constructor called.");
    }

    // Constructor 2: Overloaded, chains to Constructor 1
    public Person(String name, int age, String address) {
        this(name, age); // Calls Constructor 1
        this.address = address;
        System.out.println("Person (name, age, address) constructor called.");
    }

    public static void main(String[] args) {
        Person p1 = new Person("Alice", 30);
        System.out.println(p1.name + ", " + p1.age);

        Person p2 = new Person("Bob", 25, "123 Main St");
        System.out.println(p2.name + ", " + p2.age + ", " + p2.address);
    }
}

this and super Keywords
 * this:
   * Refers to the current object.
   * Used to differentiate between instance variables and local variables (e.g., this.name = name;).
   * Used to invoke the current class's constructor (this(...)).
   * Used to invoke the current class's method (less common, this.methodName()).
 * super:
   * Refers to the immediate superclass object.
   * Used to invoke the immediate superclass's constructor (super(...)).
   * Used to invoke the immediate superclass's method (super.methodName()).
   * Used to access a superclass's field (super.fieldName).
Instance vs. Static Members
| Feature | Instance Members | Static Members |
|---|---|---|
| Belongs to | Objects (instances) of the class. | The class itself. |
| Creation | Created when an object is instantiated. | Created when the class is loaded into memory. |
| Access | Accessed via an object reference (object.field, object.method()). | Accessed via the class name (ClassName.field, ClassName.method()). |
| Memory | Each object has its own copy of instance members. | Only one copy shared by all objects of the class. |
| this/super | Can use this and super. | Cannot use this or super directly (they refer to objects). |
Example:
class Counter {
    int instanceCount; // Instance member
    static int staticCount = 0; // Static member

    public Counter() {
        instanceCount++;
        staticCount++;
    }

    public void displayInstanceCount() {
        System.out.println("Instance Count: " + this.instanceCount);
    }

    public static void displayStaticCount() {
        System.out.println("Static Count: " + staticCount);
    }

    public static void main(String[] args) {
        Counter c1 = new Counter();
        c1.displayInstanceCount(); // 1
        Counter.displayStaticCount(); // 1

        Counter c2 = new Counter();
        c2.displayInstanceCount(); // 1 (each instance has its own)
        Counter.displayStaticCount(); // 2 (shared by all instances)

        Counter c3 = new Counter();
        c3.displayInstanceCount(); // 1
        Counter.displayStaticCount(); // 3
    }
}

Inner Classes (Nested Classes)
A class defined within another class. They can be categorized as:
 * Non-Static Inner Classes (Inner Classes):
   * Depend on an instance of the outer class.
   * Can access all members (including private) of the outer class.
   * Example:
     class Outer {
    private int outerX = 10;

    class Inner { // Non-static inner class
        public void display() {
            System.out.println("Outer x: " + outerX); // Accesses outerX
        }
    }

    public void createInner() {
        Inner inner = new Inner();
        inner.display();
    }

    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.createInner();
        // Or directly: Outer.Inner inner = new Outer().new Inner();
    }
}

 * Static Nested Classes:
   * Behave like top-level classes but are nested for packaging convenience.
   * Do not have access to non-static members of the outer class.
   * Can be accessed directly using the outer class name.
   * Example:
     class OuterStatic {
    private static int outerStaticX = 20;
    private int outerNonStaticY = 30;

    static class StaticInner { // Static nested class
        public void display() {
            System.out.println("Outer static x: " + outerStaticX); // Accesses static member
            // System.out.println("Outer non-static y: " + outerNonStaticY); // Compile-time error
        }
    }

    public static void main(String[] args) {
        OuterStatic.StaticInner staticInner = new OuterStatic.StaticInner();
        staticInner.display();
    }
}

Use Cases for Inner Classes:
 * Encapsulation: Grouping a class that is only used by another class.
 * Logical Grouping: Keeping related classes together.
 * Event Handling: Often used in GUI applications for listeners.
 * Anonymous Inner Classes: Used for concise, one-time implementations of interfaces or abstract classes.
Access Modifiers
Access modifiers control the visibility and accessibility of classes, fields, methods, and constructors.
| Modifier | Within Class | Within Package | Outside Package (Subclass) | Outside Package (Any Class) |
|---|---|---|---|---|
| private | Yes | No | No | No |
| default | Yes | Yes | No | No |
| protected | Yes | Yes | Yes | No |
| public | Yes | Yes | Yes | Yes |
 * private: Most restrictive. Members are only accessible within the class they are declared in. Used for internal implementation details (strong encapsulation).
 * default (no modifier): Accessible only within the same package. If no access modifier is specified, it's considered default.
 * protected: Accessible within the same package and by subclasses in any package. Used when you want to allow subclasses to access members but restrict outside access.
 * public: Least restrictive. Members are accessible from anywhere. Used for defining the public interface of a class.
Example:
// Package 1: com.example.model
package com.example.model;

public class Employee {
    private String privateField = "Private Data";
    String defaultField = "Default Data"; // default access
    protected String protectedField = "Protected Data";
    public String publicField = "Public Data";

    private void privateMethod() { System.out.println("Private method."); }
    void defaultMethod() { System.out.println("Default method."); }
    protected void protectedMethod() { System.out.println("Protected method."); }
    public void publicMethod() { System.out.println("Public method."); }

    public static void main(String[] args) {
        Employee emp = new Employee();
        System.out.println(emp.privateField); // Accessible
        emp.privateMethod(); // Accessible
    }
}

// Package 1: com.example.model (Same package)
package com.example.model;

class EmployeeManager {
    public void manage() {
        Employee emp = new Employee();
        // System.out.println(emp.privateField); // Error
        System.out.println(emp.defaultField); // Accessible
        System.out.println(emp.protectedField); // Accessible
        System.out.println(emp.publicField); // Accessible

        // emp.privateMethod(); // Error
        emp.defaultMethod(); // Accessible
        emp.protectedMethod(); // Accessible
        emp.publicMethod(); // Accessible
    }
}

// Package 2: com.example.app
package com.example.app;

import com.example.model.Employee;

class Department extends Employee { // Subclass in different package
    public void departmentDetails() {
        // System.out.println(privateField); // Error
        // System.out.println(defaultField); // Error
        System.out.println(protectedField); // Accessible (protected members inherited)
        System.out.println(publicField); // Accessible

        // privateMethod(); // Error
        // defaultMethod(); // Error
        protectedMethod(); // Accessible
        publicMethod(); // Accessible
    }
}

// Package 2: com.example.app (Any class in different package)
package com.example.app;

import com.example.model.Employee;

public class Company {
    public static void main(String[] args) {
        Employee emp = new Employee();
        // System.out.println(emp.privateField); // Error
        // System.out.println(emp.defaultField); // Error
        // System.out.println(emp.protectedField); // Error
        System.out.println(emp.publicField); // Accessible

        // emp.privateMethod(); // Error
        // emp.defaultMethod(); // Error
        // emp.protectedMethod(); // Error
        emp.publicMethod(); // Accessible
    }
}

Interfaces in Java (Deeper Dive)
Java interfaces are powerful tools for achieving abstraction, defining contracts, and supporting multiple inheritance of type.
default, static, and private methods in Interfaces (Java 8+)
Prior to Java 8, interfaces could only have abstract methods. Java 8 introduced default and static methods, and Java 9 introduced private methods.
 * default methods:
   * Provide a default implementation for a method in an interface.
   * Classes implementing the interface don't have to implement these methods (they can use the default implementation or override it).
   * Useful for adding new methods to existing interfaces without breaking backward compatibility for all implementing classes.
   * Example: showInfo() and printWithConnection() in the Printable interface example above.
 * static methods:
   * Belong to the interface itself, not to any implementing object.
   * Can only be called using the interface name (InterfaceName.staticMethod()).
   * Cannot be overridden by implementing classes.
   * Useful for utility methods related to the interface.
   * Example: printGuide() in the Printable interface example above.
 * private methods (Java 9+):
   * Introduced to allow common helper methods to be shared among default methods within the same interface without exposing them to implementing classes.
   * Can be static or non-static.
   * Example: connectToPrinter() in the Printable interface example above.
Functional Interfaces, @FunctionalInterface, and Lambda Expressions
 * Functional Interface: An interface with exactly one abstract method. They are a core concept for enabling lambda expressions.
 * @FunctionalInterface Annotation: An optional annotation that you can use to mark an interface as a functional interface. The compiler will enforce the "single abstract method" rule if this annotation is present, helping to prevent accidental additions of extra abstract methods.
 * Lambda Expressions: A concise way to represent an instance of a functional interface. They provide a compact syntax for implementing the single abstract method without writing a full anonymous inner class.
Example:
@FunctionalInterface // Optional but good practice
interface MyCalculator {
    int operate(int a, int b); // Single abstract method
}

public class LambdaDemo {
    public static void main(String[] args) {
        // Using an anonymous inner class (pre-Java 8)
        MyCalculator adderAnonymous = new MyCalculator() {
            @Override
            public int operate(int a, int b) {
                return a + b;
            }
        };
        System.out.println("Anonymous Add: " + adderAnonymous.operate(10, 5));

        // Using a lambda expression (Java 8+) for addition
        MyCalculator adderLambda = (a, b) -> a + b;
        System.out.println("Lambda Add: " + adderLambda.operate(10, 5));

        // Using a lambda expression for subtraction
        MyCalculator subtractorLambda = (x, y) -> x - y;
        System.out.println("Lambda Subtract: " + subtractorLambda.operate(10, 5));

        // Lambda with more complex logic (multi-statement body)
        MyCalculator multiplierLambda = (num1, num2) -> {
            int result = num1 * num2;
            System.out.println("Multiplying " + num1 + " by " + num2);
            return result;
        };
        System.out.println("Lambda Multiply: " + multiplierLambda.operate(4, 6));
    }
}

Lambda expressions are widely used with Java's Stream API for functional programming constructs.
Upcasting, Downcasting, and Type Safety
 * Upcasting (Implicit/Safe): Assigning a subclass object to a superclass reference variable. This is always safe because a subclass object is a superclass object. You can only call methods defined in the superclass (or overridden methods of the subclass).
   Animal animal = new Dog("Charlie", 1, "Beagle"); // Upcasting: Dog object assigned to Animal reference
animal.eat(); // Calls Dog's eat() due to runtime polymorphism
// animal.bark(); // Compile-time error: bark() is not in Animal

 * Downcasting (Explicit/Risky): Assigning a superclass reference to a subclass reference variable. This requires an explicit cast and can lead to a ClassCastException at runtime if the object being cast is not actually an instance of the target subclass.
   Animal animal = new Dog("Charlie", 1, "Beagle"); // Upcasted reference
if (animal instanceof Dog) { // Check for type safety before downcasting
    Dog dog = (Dog) animal; // Downcasting
    dog.bark(); // Now you can call Dog-specific methods
}

Animal anotherAnimal = new Cat("Lucy", 2, "Persian");
// Dog anotherDog = (Dog) anotherAnimal; // This would throw ClassCastException at runtime
if (anotherAnimal instanceof Dog) {
    Dog anotherDog = (Dog) anotherAnimal;
    anotherDog.bark();
} else {
    System.out.println("Cannot downcast Cat to Dog.");
}

 * instanceof operator: Used to check if an object is an instance of a particular class or interface. It's crucial for type safety before performing downcasting.
Dynamic Binding and Late Binding
These terms are synonymous with Runtime Polymorphism.
 * Dynamic Binding (or Late Binding): The process of resolving method calls at runtime rather than compile time. In Java, when you have an overridden method, the JVM determines which version of the method to execute based on the actual type of the object, not the reference type. This allows for flexible and extensible code.
   // Example from RuntimePolymorphismDemo
Animal myAnimal1 = new Dog("Max", 2, "Labrador"); // Reference type is Animal, object type is Dog
myAnimal1.eat(); // At runtime, JVM looks at the actual object (Dog) and calls Dog's eat()

SOLID Principles as Design Best Practices in OOP
SOLID is an acronym for five design principles intended to make software designs more understandable, flexible, and maintainable. Java's OOP features directly support these principles.
 * S - Single Responsibility Principle (SRP):
   * Principle: A class should have only one reason to change, meaning it should have only one job or responsibility.
   * Java Support: Encapsulation helps enforce SRP. A well-designed class encapsulates its data and behavior related to a single responsibility.
   * Example: A User class should primarily manage user data (name, email, password), not also handle database persistence or UI display logic. Separate classes like UserRepository and UserView should handle those responsibilities.
 * O - Open/Closed Principle (OCP):
   * Principle: Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification. You should be able to add new functionality without changing existing code.
   * Java Support: Polymorphism (especially interfaces and abstract classes) is key. You define an interface (closed for modification) and create new implementations (open for extension) without changing the client code that uses the interface.
   * Example: A PaymentProcessor interface with concrete implementations like CreditCardProcessor and PayPalProcessor. When a new payment method is needed, you create a new class implementing PaymentProcessor without modifying the existing PaymentProcessor interface or the code that uses it.
 * L - Liskov Substitution Principle (LSP):
   * Principle: Subtypes must be substitutable for their base types without altering the correctness of the program. If class B is a subclass of class A, then objects of type A may be replaced with objects of type B without breaking the application.
   * Java Support: Relates directly to inheritance and polymorphism. Method overriding should adhere to this principle (e.g., return types should be covariant, parameters contravariant, no new exceptions).
   * Example: If a Rectangle class has methods setWidth and setHeight, and a Square class extends Rectangle, overriding setWidth and setHeight to always set both dimensions equally would violate LSP if a client expects a Rectangle's width and height to be independent.
 * I - Interface Segregation Principle (ISP):
   * Principle: Clients should not be forced to depend on interfaces they do not use. Rather than one large, monolithic interface, many small, role-specific interfaces are better.
   * Java Support: Java's ability to implement multiple interfaces.
   * Example: Instead of a single Worker interface with methods like eat(), sleep(), work(), manageTeam(), code(), it's better to have smaller interfaces like Eatable, Sleepable, Workable, Manageable, Codable. A Developer might implement Eatable, Sleepable, Workable, Codable, while a Manager might implement Eatable, Sleepable, Workable, Manageable.
 * D - Dependency Inversion Principle (DIP):
   * Principle:
     * High-level modules should not depend on low-level modules. Both should depend on abstractions.
     * Abstractions should not depend on details. Details should depend on abstractions.
   * Java Support: Achieved through interfaces and dependency injection. Instead of directly instantiating concrete classes, you inject dependencies via constructors or setters, allowing for loose coupling.
   * Example: A OrderService (high-level module) should not directly depend on JdbcProductRepository (low-level detail). Instead, both should depend on a ProductRepository interface. The OrderService would receive an instance of ProductRepository through its constructor (constructor injection).
   <!-- end list -->
   // Before DIP (tight coupling)
class OrderServiceWithoutDIP {
    private JdbcProductRepository productRepository = new JdbcProductRepository(); // Direct dependency
    // ...
}

// After DIP (loose coupling using abstraction and dependency injection)
interface ProductRepository {
    Product findById(String id);
}

class JdbcProductRepository implements ProductRepository {
    // ... implementation for JDBC
}

class HibernateProductRepository implements ProductRepository {
    // ... implementation for Hibernate
}

class OrderServiceWithDIP {
    private ProductRepository productRepository; // Depends on abstraction

    // Dependency Injection via constructor
    public OrderServiceWithDIP(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }

    // ...
}

Common Pitfalls and Best Practices in OOP
Common Pitfalls:
 * Over-engineering/Excessive Abstraction: Creating too many classes, interfaces, or inheritance hierarchies when simpler solutions suffice can lead to complex and hard-to-understand code.
 * Deep Inheritance Hierarchies: Can lead to the "Fragile Base Class" problem and make code harder to maintain. Favor composition for "HAS-A" relationships.
 * Anemic Domain Model: Classes with only getters and setters and no business logic. This pushes the logic into service layers, effectively becoming procedural programming.
 * Ignoring SOLID Principles: Leads to rigid, fragile, and unmaintainable code.
 * Not using Access Modifiers Effectively: Exposing internal state (fields as public) or making methods too accessible, breaking encapsulation.
 * Improper Use of Static Members: Overusing static methods or fields can indicate a lack of object orientation or lead to global state issues, making testing difficult.
 * Mutable Objects in Concurrent Environments: Modifying shared mutable state without proper synchronization can lead to subtle and hard-to-debug concurrency issues.
Best Practices:
 * "Program to an Interface, Not an Implementation": Always use interface types for variable declarations, method parameters, and return types where possible. This makes your code more flexible and adaptable to changes.
 * Favor Composition Over Inheritance: Use inheritance only when there's a clear "IS-A" relationship. For "HAS-A" relationships, use composition.
 * Keep Classes Small and Focused (SRP): Each class should have a single, well-defined responsibility.
 * Encapsulate Data: Always make fields private and provide controlled access through public getters and setters (or other public methods).
 * Use Meaningful Names: Clear and descriptive names for classes, objects, methods, and variables improve code readability.
 * Immutability for Shared Data: When data needs to be shared across threads or its state shouldn't change after creation, consider making objects immutable.
 * Design for Testability: Write code that is easy to test independently. Dependency injection is key here.
 * Apply SOLID Principles: Regularly review your designs against the SOLID principles to ensure maintainability, flexibility, and extensibility.
 * Use Design Patterns: Learn and apply common design patterns (e.g., Factory, Strategy, Observer, Singleton, Builder) to solve recurring design problems in a structured way.
Applying OOP Effectively in Enterprise Applications with Frameworks like Spring
Enterprise applications heavily leverage OOP principles, and frameworks like Spring are built upon them.
Spring and OOP Concepts:
 * Dependency Injection (DIP Support): Spring's core feature, Dependency Injection (DI), is a direct application of the Dependency Inversion Principle. Instead of classes creating their dependencies, Spring "injects" them. This promotes loose coupling and testability.
   * Constructor Injection: The most common and preferred way to inject dependencies, where the dependencies are passed as arguments to the class's constructor.
   * Setter Injection: Dependencies are injected via setter methods.
   * Field Injection: Dependencies are injected directly into fields using annotations (less recommended due to testing difficulties).
 * Bean Inheritance: In Spring, you can define a "parent" bean definition and child bean definitions that inherit configuration from the parent. This is a form of configuration inheritance.
 * Polymorphism in Spring:
   * Service Layer: You often define interfaces for services (e.g., UserService) and have different implementations (e.g., UserServiceImpl, MockUserServiceImpl). Spring injects the appropriate implementation based on configuration.
   * Data Access (DAO/Repository): Similar to services, you define repository interfaces (e.g., UserRepository) and Spring Data JPA provides concrete implementations.
 * AOP (Aspect-Oriented Programming): While not strictly an OOP pillar, Spring AOP allows cross-cutting concerns (like logging, security, transaction management) to be modularized and applied across different parts of the application without modifying the core business logic. This enhances modularity, which is a key OOP goal.
Interview Scenarios and Design Patterns:
 * Factory Pattern: Used to encapsulate the object creation logic. Useful when you need to create different types of objects based on some criteria, without exposing the instantiation logic to the client. (e.g., ShapeFactory returns Circle or Rectangle objects based on input).
 * Strategy Pattern: Defines a family of algorithms, encapsulates each one, and makes them interchangeable. Allows the algorithm to vary independently from clients that use it. (e.g., different payment strategies like CreditCardPayment, PayPalPayment implementing a PaymentStrategy interface).
 * Observer Pattern: Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically. (e.g., UI elements updating when underlying data changes).
 * Singleton Pattern: Ensures a class has only one instance and provides a global point of access to it. Often used for logging, configuration managers, or resource managers. (Use with caution, can lead to tight coupling).
Example of DIP and Strategy Pattern with Spring (Conceptual):
// Strategy Interface
public interface PaymentStrategy {
    void pay(double amount);
}

// Concrete Strategy Implementations
@Component("creditCardPayment") // Spring annotation for a bean
public class CreditCardPaymentStrategy implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " via Credit Card.");
    }
}

@Component("paypalPayment")
public class PayPalPaymentStrategy implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " via PayPal.");
    }
}

// High-level module (depends on abstraction)
@Service // Spring annotation for a service bean
public class OrderService {
    private final PaymentStrategy paymentStrategy; // Dependency on abstraction

    // Constructor Injection (preferred in Spring)
    public OrderService(@Qualifier("creditCardPayment") PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void checkout(double totalAmount) {
        System.out.println("Processing order for " + totalAmount);
        paymentStrategy.pay(totalAmount);
    }
}

// In your Spring configuration or main application:
// @SpringBootApplication // Or @Configuration with @ComponentScan
// public class MyApplication {
//     public static void main(String[] args) {
//         ApplicationContext context = SpringApplication.run(MyApplication.class, args);
//         OrderService orderService = context.getBean(OrderService.class);
//         orderService.checkout(150.0);
//     }
// }

In this example, OrderService doesn't care about the concrete PaymentStrategy implementation. It just knows it needs a PaymentStrategy. Spring, through @Autowired or constructor injection, provides the correct implementation (CreditCardPaymentStrategy in this case, specified by @Qualifier). This demonstrates the power of OOP principles, especially DIP, facilitated by frameworks like Spring, to create flexible, maintainable, and scalable enterprise applications.
By understanding these core concepts and applying them through best practices and design patterns, you can effectively leverage OOP in Java to build robust and high-quality software.
