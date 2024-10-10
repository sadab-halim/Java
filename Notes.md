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

-------------------------------------

# Collections in Java

## What is Java Collections Framework?
- Added in Java Version 1.2
- Collections is nothing but a group of Objects
- Present in java.util package
- Framework provides us the architecture to manage these "group of objects" i.e., add, update, delete, search, etc.

### Why we need Java Collections Framework?
- Prior to JCF, we have Array, Vector and Hash Tables
- But problem with that is, there is no common interface, so its difficult to remember the methods for each
    ```java
    public class Main {
        public static void main(String[] args) {
            int[] arr = new int[4];
            //insert an elemebt in an array
            arr[0] = 1;
            //get front element
            int val = arr[0];

            Vector<Integer> vector = new Vector();
            //insert an element in vector
            vector.add(1);
            //get element
            vector.get(0);
        }
    }
    ```

## Collections Hierarcy
![java collections hierarcy](<java collections hierarchy.webp>)

### Iteratable
Used to TRAVERSE the collection <br/>
Below are the methods which are frequently used:
| S.No. | Method | Available in | Usage |
| ----- | ------ | ------------ | ----- |
| 1 | iterator() | Java 1.5 | It returns the iterator object, which provides methods showin in the below table to iterate the collections |
| 2 | forEach() | Java 1.8 | Iterate Collection using Lambda Expression. Lambda Expression is called for each element in the collection. |

iterator() methods 👇<br/>
| Method | Usage |
| ------ | ----- |
| hasNext() | returns true, if there are more elements in collection |
| next() | returns the next element in the iteration |
| remove() | removes the last element returned by iterator |

#### Example
```java
public class Main {
    public static void main(String[] args) {
        List<Integer> values = new ArrayList<>();
        values.add(1);
        values.add(2);
        values.add(3);
        values.add(4);

        //using iterator
        System.out.println("Iterating the values using iterator method");
        Iterator<Integer> valuesIterator = values.iterator();
        while(valuesIterator.hasNext()) {
            int value = valuesIterator.hasNext();
            System.out.println(val);
            if(value == 3) {
                valuesIterator.remove();
            }
        }

        System.out.println("Iterating the values using for-each loop");
        for(int val: values) {
            System.out.println(val);
        }

        //using forEach method
        System.out.println("Testing forEach method");
        values.forEach(Integer value) -> System.out.println(value);
    }
}
```

#### Output
```
Iterating the values using iterator method
1
2
4
Iterating the values using for-each loop
1
2
4
Testing forEach method
1
2
4
```

### Collection
I represents the group of objects. Its an interface which provides methods to work on group of objects <br/>
| S.No. | Method | Available In | Usage |
| ----- | ------ | ------------ | ----- |
| 1 | size() | Java 1.2 | It returns the total number of elemebts present in the collection |
| 2 | isEmpty() | Java 1.2 | Used to check if collection is empty or has some value. It returns true/false |
| 3 | contains() | Java 1.2 | Used to search an element in the collection, returns true/false |
| 4 | toArray() | Java 1.2 | It converts collection into an Array |
| 5 | add() | Java 1.2 | Used to insert an element in the collection |
| 6 | addAll() | Java 1.2 | Used to insert one collection in another collection |
| 7 | remove() | Java 1.2 | Used to remove an element from the collection |
| 8 | removeAll() | Java 1.2 | Remove all the elements from the collections, which are present in the collection passed in the parameter |
| 9 | clear() | Java 1.2 | Remove all the elements from the collection |
| 10 | equals() | Java 1.2 | Used to check if 2 collections are equal or not |
| 11 | stream() and parallerStream() | Java 1.8 | Provides effective way to work with collection like filtering, processing data, etc... |
| 12 | iterator() | Java 1.2 | As Iteratable Interface added in Java 1.5, so before this, this method was used to iterate the collection and still can be used |

#### Example
```java
public class Main {
    public static void main(String[] args) {
        List<Integer> values = new ArrayList<>();
        values.add(2);
        values.add(3);
        values.add(4);
        
        System.out.println("size: " + values.size()); //size
        System.out.println("isEmpty: " + values.isEmpty());
        System.out.println("Contains: " + values.contains(5));
        //add
        values.add(5);
        System.out.println("Added: " + values.contains(5));
        //remove using index
        values.remove(3);
        System.out.println("Removed using index: " + values.contains(5));
        //remove using Object, removes the first occurrence of the value
        values.remove(Integer.valueOf(3));
        System.out.println("Removed using object: " + values.contains(3));
        
        Stack<Integer> stackValues = new Stack<>();
        stackValues.add(6);
        stackValues.add(7);
        stackValues.add(8);
        //addAll
        values.addAll(stackValues);
        System.out.println("addAll testing using containsAll: " + values.containsAll(stackValues));
        //containsAll
        values.remove(Integer.valueOf(7));
        System.out.println("containsAll after removing 1 element: " + values.containsAll(stackValues));
        //removeAll
        values.removeAll(stackValues);
        System.out.println("removeAll: " + values.contains(8));
        //clear
        values.clear();
        System.out.println("clear: " + values.isEmpty());
    }
}
```

#### Output
```
size: 3
isEmpty: false
contains: false
added: true
removed using index: false
removed using object: false
addAll test using containsAll
containsAll after removing
removeAll: false
clear: true
```

### Collection vs Collections
- **Collection**: is part of Java Collection Framework, and its an interface, which exposes various methods implemented by various collection classes like ArrayList, Stack, LinkedList, etc.
- **Collectioins**: is a utility class and provide static method, which are used to operate on collections like swapping, searching, reverse, copy, etc.

#### Methods
- sort
- binarySearch
- get
- reverse
- shuffle
- swap
- copy
- min
- max
- rotate
- unmodifiableCollection

#### Example
```java
public class Main {
    public static void main(String[] args) {
        List<Integer> values = new ArrayList<>();
        values.add(1);
        values.add(2);
        values.add(3);
        values.add(4);

        System.out.println("Max Values: " + Collections.max(values));
        System.out.println("Min Values: " + Collections.min(values));
        Collections.sort(values);
        System.out.println("Sorted");
        values.forEach(Integer val) -> System.out.println(val);
    }
}
```

