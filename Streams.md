# Streams


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
