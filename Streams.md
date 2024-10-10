# Streams

## Returning an Optional

* Express “we don’t know” or “not applicable” answer in Java using `Optional`
* An `Optional` is created using a factory

```java
public static Optional<Double> average(int... scores) {
if (scores.length == 0) return Optional.empty();
int sum = 0;
for (int score: scores) sum += score;
return Optional.of((double) sum / scores.length);
}
```

```java
Optional<Double> opt = average(90, 100);
if (opt.isPresent())// To avoid NoSuchElementException
System.out.println(opt.get()); // 95.0

Optional o = (value == null) ? Optional.empty() : Optional.of(value);
//Or
Optional o = Optional.ofNullable(value);
System.out.println(opt.orElseThrow(() - > new IllegalStateException()));
```

### Common Optional instance methods

|          Method          |             When Optional is empty             | When Optionalcontains value |
| :----------------------: | :--------------------------------------------: | :-------------------------: |
|         `get()`          |               Throws `exception`               |        Returns value        |
| `ifPresent(Consumer c)`  |                  Does nothing                  |  Calls Consumer with value  |
|      `isPresent()`       |                Returns `false`                 |        Returns true         |
|    `orElse(T other)`     |            Returns other parameter             |        Returns value        |
| `orElseGet(Supplier s)`  |       Returns result of calling Supplier       |        Returns value        |
|     `orElseThrow()`      |        Throws `NoSuchElementException`         |        Returns value        |
| `orElseThrow(Suppliers)` | Throws exception created by calling `Supplier` |        Returns value        |

## Using Streams

* the data isn’t generated up front—it is created when needed. This is an example of **_lazy evaluation_**, which delays execution until necessary.
* There are three parts to a stream pipeline:
  * **_Source:_** Where the stream comes from.
  * **_Intermediate operations:_** Transforms the stream into another one. There can be as few or as many intermediate operations as you’d like. Since streams use lazy evaluation, the intermediate operations do not run until the terminal operation runs.
  * **_Terminal operation:_** Produces a result. Since streams can be used only once, the stream is no longer valid after a terminal operation completes.

|               Scenario                | Intermediate operation | Terminal operation |
| :-----------------------------------: | :--------------------: | :----------------: |
|   Required part of useful pipeline?   |           No           |        Yes         |
| Can exist multiple times in pipeline? |          Yes           |         No         |
|      Return type is stream type?      |          Yes           |         No         |
|      Executed upon method call?       |           No           |        Yes         |
|       Stream valid after call?        |          Yes           |         No         |

### Creating Finite Streams

```java
Stream<String> empty = Stream.empty(); // count = 0
Stream<Integer> singleElement = Stream.of(1); // count = 1
Stream<Integer> fromArray = Stream.of(1, 2, 3); // count = 3

var list = List.of("a", "b", "c");
Stream<String> fromList = list.stream();
Stream<String> fromListParallel = list.parallelStream();
```

### Creating Infinite Streams

```java
Stream<Double> randoms = Stream.generate(Math::random);
randoms.forEach(System.out::println);//print random numbers until you kill it
Stream<Integer> oddNumbers = Stream.iterate(1, n -> n + 2);
Stream<Integer> oddNumberUnder100 = Stream.iterate(
1, // seed
n - > n < 100, // Predicate to specify when done
n - > n + 2); // UnaryOperator to get next value
```

### Reviewing Stream Creation Methods

|                     Method                     | Finite or infinite? |                                                                          Notes                                                                           |
| :--------------------------------------------: | :-----------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                `Stream.empty()`                |       Finite        |                                                            Creates Stream with zero elements                                                             |
|              `Stream.of(varargs)`              |       Finite        |                                                           Creates Stream with elements listed                                                            |
|                `coll.stream()`                 |       Finite        |                                                              Creates Stream from Collection                                                              |
|            `coll.parallelStream()`             |       Finite        |                                           Creates Stream from Collection where the stream can run in parallel                                            |
|          `Stream.generate(supplier)`           |      Infinite       |                                             Creates Stream by calling Supplier for each element upon request                                             |
|      `Stream.iterate(seed,unaryOperator)`      |      Infinite       |                  Creates Stream by using seed for first element and then calling UnaryOperator for each subsequent element upon request                  |
| `Stream.iterate(seed,predicate,unaryOperator)` | Finite or infinite  | Creates Stream by using seed for first element and then calling UnaryOperator for each subsequent element upon request. Stops if Predicate returns false |

### Using Common Terminal Operations

* You can perform a terminal operation without any intermediate operations but not the other way around.
* Reductions are a special type of terminal operation where all of the contents of the stream are combined into a single primitive or `Object`. For example, you might have an `int` or a `Collection`

|                  Method                   | What happens for infinite streams | Return value  | Reduction |
| :---------------------------------------: | :-------------------------------: | :-----------: | :-------: |
|                 `count()`                 |        Does not terminate         |    `long`     |    Yes    |
|             `min()`, `max()`              |        Does not terminate         | `Optional<T>` |    Yes    |
|        `findAny()` , `findFirst()`        |            Terminates             | `Optional<T>` |    No     |
| `allMatch()`, `anyMatch()`, `noneMatch()` |       Sometimes terminates        |   `boolean`   |    No     |
|                `forEach()`                |        Does not terminate         |    `void`     |    No     |
|                `reduce()`                 |        Does not terminate         |    Varies     |    Yes    |
|                `collect()`                |        Does not terminate         |    Varies     |    Yes    |

```java
//Counting
public long count();//Method Signature

Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
System.out.println(s.count()); // 3

//Finding the Minimum and Maximum
public Optional<T> min(Comparator<? super T> comparator);//Method Signature
public Optional<T> max(Comparator<? super T> comparator);//Method Signature

Stream<String> s = Stream.of("monkey", "ape", "bonobo");
Optional<String> min = s.min((s1, s2) -> s1.length()-s2.length());
min.ifPresent(System.out::println); // ape

//stream is empty, the comparator is never called, and no value is present in the Optional
Optional<?> minEmpty = Stream.empty().min((s1, s2) -> 0);
System.out.println(minEmpty.isPresent()); // false

//Finding a Value
//terminal operations but not reductions The reason is that they sometimes return without processing all of the elements.
//This means that they return a value based on the stream but do not reduce the entire stream into one value.
// return a random element when working with parallel streams.
public Optional<T> findAny();//Method Signature
public Optional<T> findFirst();//Method Signature

Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
Stream<String> infinite = Stream.generate(() -> "chimp");
s.findAny().ifPresent(System.out::println); // monkey (usually)
infinite.findAny().ifPresent(System.out::println); // chimp

//Matching
public boolean anyMatch(Predicate <? super T> predicate);//Method Signature
public boolean allMatch(Predicate <? super T> predicate);//Method Signature
public boolean noneMatch(Predicate <? super T> predicate);//Method Signature

var list = List.of("monkey", "2", "chimp");
Stream<String> infinite = Stream.generate(() -> "chimp");
Predicate<String> pred = x -> Character.isLetter(x.charAt(0));
System.out.println(list.stream().anyMatch(pred)); // true
System.out.println(list.stream().allMatch(pred)); // false and run until we killed the program.
System.out.println(list.stream().noneMatch(pred)); // false
System.out.println(infinite.anyMatch(pred)); // true

//Iterating
public void forEach(Consumer<? super T> action);//Method Signature

Stream<String> s = Stream.of("Monkey", "Gorilla", "Bonobo");
s.forEach(System.out::print); // MonkeyGorillaBonobo

//Reducing
// start with an initial value and keep merging it with the next value.
public T reduce(T identity, BinaryOperator<T> accumulator);//Method Signature
public Optional<T> reduce(BinaryOperator<T> accumulator);//Method Signature
public <U> U reduce(U identity,BiFunction<U,? super T,U> accumulator,BinaryOperator<U> combiner);//Method Signature

Stream<String> stream = Stream.of("w", "o", "l", "f");
String word = stream.reduce("", (s, c) -> s + c);
//Or
String word = stream.reduce("", String::concat);
System.out.println(word); // wolf

Stream<Integer> stream = Stream.of(3, 5, 6);
System.out.println(stream.reduce(1, (a, b) -> a*b)); // 90

int length = stream.reduce(0, //value for the initializer
(i, s) -> i+s.length(), //accumulator
(a, b) -> a+b);//combiner
System.out.println(length); // 5

BinaryOperator<Integer> op = (a, b) -> a * b;

Stream<Integer> empty = Stream.empty();
empty.reduce(op).ifPresent(System.out::println); // no output

Stream<Integer> oneElement = Stream.of(3);
oneElement.reduce(op).ifPresent(System.out::println); // 3

Stream<Integer> threeElements = Stream.of(3, 5, 6);
threeElements.reduce(op).ifPresent(System.out::println); // 90

//Collecting
//The collect() method is a special type of reduction called a mutable reduction
public <R> R collect(Supplier<R> supplier,BiConsumer<R, ? super T> accumulator,BiConsumer<R, R> combiner);//Method Signature
public <R,A> R collect(Collector<? super T, A,R> collector);//Method Signature
Stream<String> stream = Stream.of("w", "o", "l", "f");
StringBuilder word = stream.collect(
StringBuilder::new,//supplier
StringBuilder::append,//accumulator
StringBuilder::append);//combiner
System.out.println(word); // wolf

Stream<String> stream = Stream.of("w", "o", "l", "f");
TreeSet<String> set = stream.collect(
TreeSet::new,//supplier creates an empty TreeSet
TreeSet::add,// accumulator adds a single String from the Stream to the TreeSet
TreeSet::addAll);//combiner adds all of the elements of one TreeSet to another in case the operations were done in parallel andneed to be merged.
System.out.println(set); // [f, l, o, w]

//OR
Stream<String> stream = Stream.of("w", "o", "l", "f");
TreeSet<String> set = stream.collect(Collectors.toCollection(TreeSet::new));
System.out.println(set); // [f, l, o, w]

//If Not Sorted
Stream<String> stream = Stream.of("w", "o", "l", "f");
Set<String> set = stream.collect(Collectors.toSet());
System.out.println(set); // [f, w, l, o]
```

### Using Common Intermediate Operations

* intermediate operation produces a stream as its result and can deal with an infinite stream simply by returning another infinite stream

```java
//Filtering
public Stream<T> filter(Predicate<? super T> predicate);//Method Signature
Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
s.filter(x -> x.startsWith("m")).forEach(System.out::print); // monkey 

//Removing Duplicates
public Stream<T> distinct();//Method Signature
Stream<String> s = Stream.of("duck", "duck", "duck", "goose");
s.distinct().forEach(System.out::print); // duckgoose

//Restricting by Position
public Stream<T> limit(long maxSize);//Method Signature
public Stream<T> skip(long n);//Method Signature
Stream<Integer> s = Stream.iterate(1, n -> n + 1);//creates an infinite stream of numbers counting from 1
s.skip(5).limit(2).forEach(System.out::print); // 67

//Mapping
//creates a one- to- one mapping from the elements in the stream to the elements of the next step in the stream
public <R> Stream<R> map(Function<? super T, ? extends R> mapper);//Method Signature
Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
s.map(String::length).forEach(System.out::print); // 676

//Using flatMap
//takes each element in the stream and makes any elements it contains top-level elements in a single stream
//to remove empty elements from a stream or combine a stream of lists.
public <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);//Method Signature
List<String> zero = List.of();
var one = List.of("Bonobo");
var two = List.of("Mama Gorilla", "Baby Gorilla");
Stream<List<String>> animals = Stream.of(zero, one, two);
animals.flatMap(m -> m.stream()).forEach(System.out::println);//Bonobo, Mama Gorilla, Baby Gorilla
//We can also concat two streams
Stream.concat(one, two).forEach(System.out::println);

//Sorting
public Stream<T> sorted();//Method Signature
public Stream<T> sorted(Comparator<? super T> comparator);//Method Signature
Stream<String> s = Stream.of("brown-", "bear-");
s.sorted().forEach(System.out::print); // bear-brown-

Stream<String> s = Stream.of("brown bear-", "grizzly-");
s.sorted(Comparator.reverseOrder()).forEach(System.out::print); // grizzly-brown bear-

//Taking a Peek
// intermediate version of forEach()
public Stream<T> peek(Consumer<? super T> action);//Method Signature
var stream = Stream.of("black bear", "brown bear", "grizzly");
long count = stream.filter(s -> s.startsWith("g")).peek(System.out::println).count(); // grizzly
System.out.println(count); // 1
```

### Putting Together the Pipeline

```java
var list = List.of("Toby", "Anna", "Leroy", "Alex");
list.stream()
.filter(n - > n.length() == 4)
.sorted()
.limit(2)
.forEach(System.out::println);
```


