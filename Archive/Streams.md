# Streams

## What is Stream?
Java 8 introduced the Stream API to enable functional-style operations on collections. Streams help in processing large amounts of data efficiently.

**Key Features:**
- Works with Collections, Arrays, and I/O.
- Supports functional programming.
- Enables parallel execution.
- Allows intermediate and terminal operations.

Collection --- _Step 1_ ---> Create Stream --- _Step 2_ ---> Intermediate Operations --- _Step 3_ ---> Terminal Operations 

- **At Step 1**: Streams are created from the data source like collection or array etc.
- **At Step 2**: Intermediate Operations (_discussed in detail: [click here](#3-intermediate-operations-lazy-evaluation)_)
- **At Step 3**: Terminal Operations _(discussed in detail: [click here](#4-terminal-operations-triggers-execution)_)

### Different ways to create a Stream
1. From Collection:
   ```java
   List<Integer> salaryList = Arrays.asList(3000, 4100, 9000, 1000, 3500);
   Stream<Integer> streamFromIntegerList = salaryList.stream();
   ```
2. From Array:
   ```java
   Integer[] salaryArray = {3000, 4100, 9000, 3500};
   Stream<Integer> streamFromIntegerArray = Arrays.stream(salaryArray);
   ```
3. From Static Method:
   ```java
   Stream<Integer> streamFromStaticMethod = Stream.of(1000, 4500, 3500, 9000);
   ```
4. From Stream Builder:
   ```java
    Stream.Builder<Integer> streamBuilder = Stream.builder();
    streamBuilder.add(1000).add(9000).add(4500);
    Stream<Integer> streamFromStreamBuilder = streamBuilder.build();
   ```
5. From Stream Iterate:
   ```java
   Stream<Integer> streamFromIterate = Stream.iterate(1000, (Integer n) -> n + 5000).limit(5);
   ```
   
### Different Intermediate Operations
We can chain multiple intermediate operations together to perform more complex processing before applying terminal operation to produce the result.

1. **filter(Predicate<T> predicate)** : filters the element
    ```java
    Stream<String> nameStream = Stream.of("HELLO", "EVERYBODY", "HOW", "ARE", "YOU", "DOING");
    Stream<String> filteredStream = nameStream.filter((String name) - > name.length() <= 3);
    List<String> filteredNameList = filteredStream.collect(Collectors.toList());
    ```

2. **map(Function<T, R> mapper)** : used to transform each element
   ```java
   Stream<String> nameStream = Stream.of("HELLO", "EVERYBODY", "HOW", "ARE", "YOU", "DOING");
   Stream<String> filteredNames = nameStream.map((String name) -> name.toLowerCase());
   ```
3. **flatMap(Function<T, Stream<R>> mapper)** : used to iterate over each element of the complex collection, and helps to flatten it.
   ```java
   List<List<String>> sentenceList = Arrays.asList(
        Arrays.asList("I", "LOVE", "JAVA"),
        Arrays.asList("CONCEPTS", "ARE", "CLEAR"),
        Arrays.asList("ITS", "VERY", "EASY"));
   
   Stream<String> wordsStream1 = sentenceList.stream().flatMap(List<String> sentence) -> sentence.stream());
   Stream<String> wordsStream2 = sentenceList.stream().flatMap(List<String> sentence) -> sentence.stream().map((String value) -> value.toLowerCase()));
   ```
   
4. **distinct()** : it removes duplicate from the stream
   ```java
   Integer[] arr = {1,5,2,3,7,3,4,7,8,5};
   Stream<Integer> arrStream = Arrays.stream(arr).distinct();
   ```
   
5. **sorted()** : sorts the elements
   ```java
   Integer[] arr = {1,5,2,3,7,3,4,7,8,5};
   Stream<Integer> arrStream = Arrays.stream(arr).sorted();
   ```
   ```java
   Integer[] arr = {1,5,2,3,7,3,4,7,8,5};
   Stream<Integer> arrStream = Arrays.stream(arr).sorted((Integer val1, Integer val2) -> val2-val1);
   ```

6. **peek(Consumer<T> action)** : helps you to see the intermediate result of the stream which is getting processed
   ```java
    List<Integer> numbers = Arrays.asList(2,1,4,5,7);
    Stream<Integer> numberStream = numbers.stream()
        .filter((Integer val) -> val > 2)
        .peek((Integer val) -> System.out.println(val))
        .map((Integer val) -> -1 * val);
    List<Integer> numberList = numberStream.collect(Collectors.toList());
   ```

7. **limit(long maxSize)** : truncate the stream, to have no longer than given maxSize
    ```java
    List<Integer> numbers = Arrays.asList(2,1,3,4,5);
    Stream<Integer> numberStream = numbers.stream().limit(3);
    List<Integer> numberList = numberStream.collect(Collectors.toList()); 
   ```

8. **skip(long n)** : skip the first n elements of the stream
   ```java
   List<Integer> numbers = Arrays.asList(2,1,3,4,5);
   Stream<Integer> numberStream = numbers.stream().skip(3);
   List<Integer> numberList = numberStream.collect(Collectors.toList());
   ```

9. **mapToInt(ToIntFunction<T> mapper)** : helps to work with primitive "int" data types
    ```java
    import java.util.Arrays;import java.util.stream.IntStream;List<String> numbers = Arrays.asList("2", "1", "4", "5");
    IntStream numberStream = numbers.stream().mapToInt((String val) -> Integer.parseInt(val));
    
    int[] numberArray = numberStream.toArray();
    int[] numbersArray = {3,2,5,1};
    IntStream numbersStream = Arrays.stream(numbersArray);
    numbersStream.filter((int val) -> val > 2);
    int[] filteredArray = numbersStream.toArray();
    ```

10. **mapToLong(ToLongFunction<T> mapper)** : helps to work with primitive "long" data types
    ```java
    ```

12. **mapToDouble(ToDoubleFunction<T> mapper)** : helps to work with primitive "double" data types
    ```java
    ```

### Why we call Intermediate Operation: "Lazy" ?

------

### Different Intermediate Operations

```java
import java.util.stream.Collectors;

public class StreamExample {
   public static void main(String[] args) {
      List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
      Stream<Integer> numbersStream = numbers.stream()
              .filter((Integer val) -> val >= 3)
              .peek((Integer val) -> System.out.println("After filter: " + val))
              .map((Integer val) -> (val * -1))
              .peek((Integer val) -> System.out.println("After negating: " + val))
              .sorted()
              .peek((Integer val) -> System.out.println("After sorting: " + val));
      List<Integer> filteredNumberStream = numbersStream.collect(Collectors.toList());
   }
}
```

#### Expected Output
```
After filter:4
After filter:7
After filter:10

After negating:-4
After negating:-7
After negating:-10

After sorted:-10
After sorted:-7
After sorted:-4
```

#### Actual Output
```
After filter:4
After negating:-4
After filter:7
After negating:-7
After filter:10
After negating:-10
After sorted:-10
After sorted:-7
After sorted:-4
```

### Different Terminal Operations
Methods in terminal operations end the stream pipeline and produce a result. It triggers the processing of the stream

1. **forEach(Consumer<T> action)** : perform action on each element of the Stream. DO NOT return any value.
    ```java
    List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
    numbers.stream()
      .filter((Integer val) -> val >= 3)
      .forEach((Integer val) -> System.out.println(val));
    ```

2. **toArray()** : collects the elements of the stream into an array
   ```java
   List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
   Object[] filteredNumberArrType1 = numbers.stream()
      .filter((Integer val) -> val >= 3)
      .toArray();
   
   Integer[] filteredNumberArrType2 = numbers.stream()
      .filter((Integer val) -> val >= 3)
      .toArray((int size) -> new Integer[size]);
   ```
3. **reduce(BinaryOperator<T> accumulator)** : does reduction on the elements of the stream, performs associative aggregation function.
   ```java
   List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
   Optional<Integer> reducedValue = numbers.stream().reduce((Integer val1, Integer val2) -> val1 + val2);
   System.out.println(reducedValue.get());
   ```

4. **collect(Collector<T, A, R> collector)** : can be used to collect the elements of the stream into a List
   ```java
   List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
   List<Integer> filteredNumber = numbers.stream()
      .filter((Integer val) -> val >= 3)
      .collect(Collectors.toList());
   ```

5. **min(Comparator<T> comparator) and max(Comparator<T> comparator)** : finds the minimum or maximum element from the stream based on the comparator provided.
   ```java
   List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
   Optional<Integer> minimumValueType1 = numbers.stream()
        .filter((Integer val) -> val >= 3)
        .min((Integer val1, Integer val2) -> val1 - val2);
   
   System.out.println(minimumValueType1.get());
   
   Optional<Integer> getMinimumValueType2 = numbers.stream()
        .filter((Integer val) -> val >= 3)
        .min((Integer val1, Integer val2) -> val2 - val1);
   
   System.out.println(minimumValueType2.get());
   ```

6. **count()** : returns the count of element present in the stream
   ```java
    List<Integer> numbers = Arrays.asList(2,1,4,5,7);
    long noOfValuesPresent = numbers.stream()
        .filter((Integer val1) -> val1 >= 3)
        .count();
    System.out.println(noOfValuesPresent);
   ```

7. **anyMatch(Predicate<T> predicate)** : checks if any value in the stream match the given predicate and return the boolean.
    ```java
    List<Integer> numbers = Arrays.asList(2,1,3,4,5);
    boolean hasValueGreaterThanThree = numbers.stream().anyMatch((Integer val) -> val > 3);
    System.out.println(hasValueGreaterThanThree); 
   ```

8. **allMatch(Predicate<T> predicate)** : checks if all values in the stream match the given predicate and return the boolean
   ```java
   
   ```

9. **noneMatch(Predicate<T> predicate)** : checks if no value in the stream match the given predicate and return the boolean
    ```java
   
    ```

10. **findFirst()** : finds the first element of the stream
    ```java
    List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
    Optional<Integer> firstValue = numbers.stream()
        .filter((Integer val) -> val >= 3)
        .findFirst();
    System.out.println(firstValue.get());
    ```

12. **findAny()** : finds the random element of the stream
    ```java
    
    ```
#### How many times can we use a single stream?
One terminal operation is used on a Stream, it is closed/consumed and cannot be used again for another terminal operation.

```java
List<Integer> numbers = Arrays.asList(2, 1, 4, 7, 10);
Stream<Integer> filteredNumbers = numbers.stream()
        .filter((Integer val) -> val >= 3);

filteredNumbers.forEach((Integer val) -> System.out.println(val));
//trying to use the closed stream again
List<Integer> listFromStream = filteredNumbers.collect(Collectors.toList());
```

#### Output
```
4
7
10
Exception in thread "main" java.lang.IllegalStateException: stream has already been operated upon or closed at StreamExample.main(...)
```

### 5. Parallel Streams
- Parallel Streams helps to perform operation on stream concurrently, improving performance.
- ParallelStream() method is used instead of regular stream() method.
- Internally it does:
  - Task splitting: It uses "spliterator" functino to split the data into multiple chunks.
  - Task submission and parallel processing: Uses **fork-join** pool technique.

```java
public class StreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(11, 22, 33, 44, 55, 66, 77, 88, 99, 110);
        //sequential processing
        long sequentialProcessingStartTime = System.currentTimeMillis();
        numbers.stream()
                .map((Integer val) -> val * val)
                .forEach((Integer val) -> System.out.println(val));
        System.out.println("Sequential processing time taken: " + (System.currentTimeMillis() - sequentialProcessingStartTime) * " millisecond");
        //parallel processing
        long parallelProcessingStartTime = System.currentTimeMillis();
        numbers.parallelStream()
                .map((Integer val) -> val * val);
                .forEach((Integer val) -> System.out.println(val));
        System.out.println("Parallel processing time taken: " + (System.currentTimeMillis() - parallelProcessingStartTime) + " millisecond");
    }
}
```

#### Output
```
121
484
1089
1936
3025
4356
5929
7744
9801
12100
Sequential processing time taken: 64 milliseconds
7744
121
5929
4356
1936
484
12100
1089
9801
3025
Parallel processing time taken: 5 milliseconds
```

### Stream vs Collection

| Feature | Stream | Collection |
|---------|--------|-----------|
| Storage | No | Yes |
| Iteration | Internal | External |
| Modification | Immutable | Mutable |
| Processing | Parallel possible | Sequential |
| Consumption | One-time | Reusable |


### Performance Considerations
✔ Use **parallelStream()** for large datasets.  
✔ Avoid modifying shared state inside streams.  
✔ Prefer method references over lambdas when possible.  
✔ Debugging is harder with streams.  
✔ Use **collect()** for grouping, partitioning, and mapping.

### Summary
- Java 8 Streams enable functional-style operations.
- Intermediate operations allow transformations.
- Terminal operations produce results.
- Parallel streams improve efficiency but should be used carefully.
- Best suited for transformation, filtering, sorting, and aggregation.