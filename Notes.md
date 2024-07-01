# Java Basics-to-Advanced Notes
------------------

# Functional Interfact and Lambda Expressions - Java 8 Features

## Functional Interface
- If an interface contains only 1 abstract method, then it is known as Functional Interface.
- Also known as SAM Interface (Single Abstract Method)
- @FunctionalInterface keyword can be used at top of the interface
    ```java
    @FuntionalInterface //optional
    public interface Bird {
        void canFly(String value);
    }
    ```
- @FunctionalInterface annotation restrict us and throws compilation error, if we try to add more than 1 abstract method
    ```java
    @FunctionalInterface
    public interface Bird {
        void canFly(String value);
        void getHeight(); //❌
    }
    ```
- In Functional Interface, only 1 abstract method is allowed, but other methods like default, static method or methods inherited from the class are allowed
    ```java
    @FunctionalInterface
    public interface Bird {
        void canFly(String value);
        default void getHeight() {
            //default method implementation
        }
        static void canEat() {
            //my static method implementation
        }
        String toString(); //object class method
    }
    ```

## Lambda Expressions
- Lambda Expression is a way of implementing the Functional Interface

### Different Ways to Implement the Functional Interface
- **using "implements"**
    ```java
    @FunctionalInterface
    public interface Bird {
        void canFly(String value);
    }

    public class Eagle implements Bird {
        @Override
        public void canFly(String value) {
            System.out.println("Eagle Bird Implementation");
        }
    }
    ```
- **using "anonymous" class**
    ```java
    @FunctionalInterface
    public interface Bird {
        void canFly(String value);
    }

    public class Main {
        public static void main (String args[]) {
            //anonymous class 👇
            Bird eagleObject = new Bird() {
                @Override
                public void canFly(String value) {
                    System.out.println("Eagle Bird Implementation");
                }
            };
            eagleObject.canFly("Vertical");
        }
    }
    ```
- **using **Lambda Expressions**
    ```java
    @FunctionalInterface
    public interface Bird {
        void canFly(String value);
    }

    public class Main {
        Bird eagleObject = (String value) -> {
            System.out.println("Eagle Bird Implementation");
            };
        eagleObject.canFly("Vertical");
    }
    ```

## Types of Functional Interface
- Consumer
- Supplier
- Function
- Predicate

### Consumer
- Represent an operation, that accept a single input parameter and returns no result.
- Present in package: java.util.function;

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept (T t);
}

public class Main {
    public static void main(String args[]) {
        Consumer<Integer> loggingObject = (Integer value) -> {
            if(value > 10) {
                System.out.println("Logging");
            }
        };
        loggingObject.accept(11);
    }
}
```

### Supplier
- Represent the supplier of the result. Accepts no input parameter but produce a result.
- Present in package: java.util.function;

```java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}

public class Main {
    public static void main(String args[]) {
        Supplier<String> isEvenNumber = () -> "This is the data I am returning.";
        System.out.println(isEvenNumber.get());
    }
}
```

### Function
- Represent function, that accepts one argument process it and produces a result
- Present in package: java.util.function;

```java
public interface Function<T, R> {
    R apply(T t);
}

public class Main {
    public static void main(String args[]) {
        Function<Integer, String> integerToString = (Integer num) -> {
            String output = num.toString();
            return output;
        };
        System.out.println(integerToString.apply(64));
    }
}
```

### Predicate
- Represent function, that accepts one argument and returns boolean
- Present in package: java.util.function;
    ```java
    @FunctionalInterface
    public interface Predicate<T> {
        boolean test(T t);
    }

    public class Main {
        public static void main(String args[]) {
            Predicate<Integer> isEven = (Integer value) -> {
                if(value % 2 == 0) {
                    return true;
                } else {
                    return false;
                }
            };
            System.out.println(isEven.test(19));
        }
    }
    ```

## Handle Use Cases When Functional Interface Extends from Other Interface
### Use Case 1: Functional Interface extending Non Functional Interface
#### Incorrect ❌
```java
public interface LivingThing {
    public void canBreathe();
}

@FunctionalInterface
public inteface Bird extends LivingThing {
    void canFly(String value);
}
```

#### Correct ✅
```java
public interface LivingThing {
    default public boolean canBreathe() {
        return true;
    }
}

@FunctionalInterface
public interface Bird extends LivingThing {
    void canFly(String value);
}
```

### Use Case 2: Interface extending Functional Interface
```java
@FunctionalInterface
public interface LivingThing {
    public boolean canBreathe();
}

public interface Bird extends LivingThing {
    void canFly(String value);
}
```

### Use Case 3 : Functional Interface extending Other Functional Interface
```java
@FunctionalInterface
public interface LivingThing {
    public boolean canBreathe();
}

```