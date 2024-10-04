# Lambdas and Functional Interfaces

## Lambda

* Lambdas work with interfaces that have exactly one abstract method.
* parentheses are optional only when there is one parameter and it doesn’t have a type declared.
* The only thing lambdas cannot access are variables that are not final or effectively final.
* It’s not a problem to assign a value to a non-final variable. However, once the lambda tries to use it, we do have a problem

### Valid lambdas that return a `boolean`

|                     Lambda                     | # of parameters |
| :--------------------------------------------: | :-------------: |
|                  `() -> true`                  |        0        |
|          `x -> x.startsWith("test")`           |        1        |
|      `(String x) -> x.startsWith("test")`      |        1        |
|  `(x, y) -> { return x.startsWith("test"); }`  |        2        |
| `(String x, String y) -> x.startsWith("test")` |        2        |

### Invalid lambdas that should return a boolean

|              Invalid lambda               |             Reason              |
| :---------------------------------------: | :-----------------------------: |
|      `x, y -> x.startsWith("fish")`       |   Missing parentheses on left   |
|     `x -> { x.startsWith("camel"); }`     |     Missing return on right     |
| `x -> { return x.startsWith("giraffe") }` | Missing semicolon inside braces |
|     `String x -> x.endsWith("eagle")`     |   Missing parentheses on left   |

## Functional Interfaces

* A functional interface is an interface that contains a single abstract method. Your friend Sam can help you remember this because it is officially known as a **_single abstract method (SAM)_** rule.
* All classes inherit certain methods from Object. You should know the following Object method signatures:
  * `public String toString()`
  * `public boolean equals(Object)`
  * `public int hashCode()`

> If a functional interface includes an abstract method with the same signature as a public method found in Object, those methods do not count toward the single abstract method test. The motivation behind this rule is that any class that implements the interface will inherit from Object, as all classes do, and therefore always implement these methods.
> Since Java assumes all classes extend from Object, you also cannot declare an interface method that is incompatible with Object. For example, declaring an abstract method int toString() in an interface would not compile since Object’s version of the method returns a String.

```java
public interface Hibernate {
 String toString();
 public boolean equals(Hibernate o);//should be equals(Object o)
 public abstract int hashCode();
 public void rest();
}
```

## Using Method References

* There are four formats for method references:
  * `static` methods
  * Instance methods on a particular object
  * Instance methods on a parameter to be determined at runtime
  * Constructors
  
### Calling static Methods

```java
interface Converter {
 long round(double num);
}
Converter methodRef = Math::round;
Converter lambda = x -> Math.round(x);
System.out.println(methodRef.round(100.1)); // 100
```

### Calling Instance Methods on a Particular Object

```java
interface StringStart {
 boolean beginningCheck(String prefix);
}
var str = "Zoo";
StringStart methodRef = str::startsWith;
StringStart lambda = s -> str.startsWith(s);
System.out.println(methodRef.beginningCheck("A")); // false
```

* While all method references can be turned into lambdas, the opposite is not always true. For example, consider this code:

```java
interface StringChecker {
boolean check();
}
var str = "";
StringChecker methodRef = str::isEmpty
StringChecker lambda = () -> str.isEmpty();
System.out.print(methodRef.check()); // true
StringChecker lambda = () -> str.startsWith("Zoo");
StringChecker methodReference = str::startsWith; // DOES NOT COMPILE
StringChecker methodReference = str::startsWith("Zoo"); // DOES NOT COMPILE
```

### Calling Instance Methods on a Parameter

```java
interface StringParameterChecker {
 boolean check(String text);
}
//This is not static it's a method reference declaring that the instance of the object will be specified later
StringParameterChecker methodRef = String::isEmpty;
StringParameterChecker lambda = s -> s.isEmpty();
System.out.println(methodRef.check("Zoo")); // false
```

### Calling Constructors

```java
interface EmptyStringCreator {
 String create();
}
EmptyStringCreator methodRef = String::new;
EmptyStringCreator lambda = () -> new String();
var myString = methodRef.create();
System.out.println(myString.equals("Snake")); // false
```

* This means you can’t always determine which method can be called by looking at the method reference. Instead, you have to look at the context to see what parameters are used and if there is a return type. In this example, Java sees that we are passing a String parameter and calls the constructor of String that takes such a parameter.

```java
interface StringCopier {
 String copy(String value);
}
StringCopier methodRef = String::new;
StringCopier lambda = x -> new String(x);
var myString = methodRef.copy("Zebra");
System.out.println(myString.equals("Zebra")); // true
```

## Reviewing Method References

|                  Type                   |      Before colon      | After colon |      Example      |
| :-------------------------------------: | :--------------------: | :---------: | :---------------: |
|            `static` methods             |       Class name       | Method name |  `Math::random`   |
| Instance methods on a particular object | Instance variable name | Method name | `str::startsWith` |
|     Instance methods on a parameter     |       Class name       | Method name | `String::isEmpty` |
|               Constructor               |       Class name       |    `new`    |   `String::new`   |

## Working with Built-in Functional Interfaces

| Functional interface  | Return type |  Method name  | # of parameters |
| :-------------------: | :---------: | :-----------: | :-------------: |
|     `Supplier<T>`     |     `T`     |    `get()`    |       `0`       |
|     `Consumer<T>`     |   `void`    |  `accept(T)`  |     `1 (T)`     |
|  `BiConsumer<T, U>`   |   `void`    | `accept(T,U)` |   `2 (T, U)`    |
|    `Predicate<T>`     |  `boolean`  |   `test(T)`   |     `1 (T)`     |
|  `BiPredicate<T, U>`  |  `boolean`  |  `test(T,U)`  |   `2 (T, U)`    |
|   `Function<T, R>`    |     `R`     |  `apply(T)`   |     `1 (T)`     |
| `BiFunction<T, U, R>` |     `R`     | `apply(T,U)`  |   `2 (T, U)`    |
|  `UnaryOperator<T>`   |     `T`     |  `apply(T)`   |     `1 (T)`     |
|  `BinaryOperator<T>`  |     `T`     | `apply(T,T)`  |   `2 (T, T)`    |

### Implementing Supplier

* A `Supplier` is used when you want to generate or supply values without taking any input.

```java
Supplier<ArrayList<String>> s3 = ArrayList::new;
ArrayList<String> a1 = s3.get();
System.out.println(a1); // []
//functionalinterface.BuiltIns$$Lambda$1/0x0000000800066840@4909b8da
System.out.println(s3);
```

* That’s the result of calling `toString()` on a lambda. Yuck. This actually does mean something. Our test `class` is named `BuiltIns`, and it is in a `package` that we created named `functionalinterface`. Then comes $$, which means that the class doesn’t exist in a class file on the file system. It exists only in memory. You don’t need to worry about the rest.

### Implementing Consumer and BiConsumer

* You use a `Consumer` when you want to do something with a parameter but not return anything. `BiConsumer` does the same thing, except that it takes two parameters.
* Printing is a common use of the `Consumer` interface:

```java
Consumer<String> c1 = System.out::println;
Consumer<String> c2 = x -> System.out.println(x);
c1.accept("Annie"); // Annie
c2.accept("Annie"); // Annie

var map = new HashMap<String, Integer>();
BiConsumer<String, Integer> b1 = map::put;
BiConsumer<String, Integer> b2 = (k, v) -> map.put(k, v);
b1.accept("chicken", 7);
b2.accept("chick", 1);
System.out.println(map); // {chicken=7, chick=1}
```

## Implementing Predicate and BiPredicate

* `Predicate` is often used when filtering or matching. Both are common operations. A `BiPredicate` is just like a `Predicate`, except that it takes two parameters instead of one.
  
```java
Predicate<String> p1 = String::isEmpty;
Predicate<String> p2 = x -> x.isEmpty();
System.out.println(p1.test("")); // true
System.out.println(p2.test("")); // true

BiPredicate<String, String> b1 = String::startsWith;
BiPredicate<String, String> b2 = (string, prefix) -> string.startsWith(prefix);
System.out.println(b1.test("chicken", "chick")); // true
System.out.println(b2.test("chicken", "chick")); // true
```

## Implementing Function and BiFunction

* A `Function` is responsible for turning one parameter into a value of a potentially different type and returning it. Similarly, a `BiFunction` is responsible for turning two parameters into a value and returning it.

```java
Function<String, Integer> f1 = String::length;
Function<String, Integer> f2 = x -> x.length();
System.out.println(f1.apply("cluck")); // 5
System.out.println(f2.apply("cluck")); // 5

BiFunction<String, String, String> b1 = String::concat;
BiFunction<String, String, String> b2 = (string, toAdd) -> string.concat(toAdd);
System.out.println(b1.apply("baby ", "chick")); // baby chick
System.out.println(b2.apply("baby ", "chick")); // baby chick
```

## Implementing UnaryOperator and BinaryOperator

* `UnaryOperator` and `BinaryOperator` are special cases of a `Function`. They require all type parameters to be the same type. A `UnaryOperator` transforms its value into one of the same type. For example, incrementing by one is a unary operation. In fact, `UnaryOperator extends Function`. A `BinaryOperator` merges two values into one of the same type. Adding two numbers is a binary operation. Similarly, `BinaryOperator extends BiFunction`.

```java
UnaryOperator<String> u1 = String::toUpperCase;
UnaryOperator<String> u2 = x -> x.toUpperCase();
System.out.println(u1.apply("chirp")); // CHIRP
System.out.println(u2.apply("chirp")); // CHIRP

BinaryOperator<String> b1 = String::concat;
BinaryOperator<String> b2 = (string, toAdd) -> string.concat(toAdd);
System.out.println(b1.apply("baby ", "chick")); // baby chick
System.out.println(b2.apply("baby ", "chick")); // baby chick
```

## Using Convenience Methods on Functional Interfaces

* Several of the common functional interfaces provide a number of helpful default interface methods.
* Note that Table shows only the main interfaces. The `BiConsumer`, `BiFunction`, and `BiPredicate` interfaces have similar methods available

| Interface instance | Method return type | Method name | Method parameters |
| :----------------: | :----------------: | :---------: | :---------------: |
|     `Consumer`     |     `Consumer`     | `andThen()` |    `Consumer`     |
|     `Function`     |     `Function`     | `andThen()` |    `Function`     |
|     `Function`     |     `Function`     | `compose()` |    `Function`     |
|    `Predicate`     |    `Predicate`     |   `and()`   |    `Predicate`    |
|    `Predicate`     |    `Predicate`     | `negate()`  |        `-`        |
|    `Predicate`     |    `Predicate`     |   `or()`    |    `Predicate`    |

```java
//FROM
Predicate<String> brownEggs = s -> s.contains("egg") && s.contains("brown");
Predicate<String> otherEggs = s -> s.contains("egg") && !s.contains("brown");
//TO
Predicate<String> brownEggs = egg.and(brown);
Predicate<String> otherEggs = egg.and(brown.negate());
```

```java
Consumer<String> c1 = x -> System.out.print("1: " + x);
Consumer<String> c2 = x -> System.out.print(",2: " + x);
Consumer<String> combined = c1.andThen(c2);
combined.accept("Annie"); // 1: Annie,2: Annie
```

* Notice how the same parameter is passed to both c1 and c2. This shows that the Consumer instances are run in sequence and are independent of each other. By contrast, the compose() method on Function chains functional interfaces. However, it passes along the output of one to the input of another.

```java
Function<Integer, Integer> before = x -> x + 1;
Function<Integer, Integer> after = x -> x * 2;
Function<Integer, Integer> combined = after.compose(before);
System.out.println(combined.apply(3)); // 8
```

## Learning the Functional Interfaces for Primitives

* Most of them are for the `double`, `int`, and `long` types. There is one exception, which is `BooleanSupplier`.

### Functional Interfaces for boolean

```java
BooleanSupplier b1 = () -> true;
BooleanSupplier b2 = () -> Math.random()> .5;
System.out.println(b1.getAsBoolean()); // true
System.out.println(b2.getAsBoolean()); // false
```

### Functional Interfaces for double, int, and long

| Functional interfaces  | Return type | Single abstract method |   # of parameters    |
| :--------------------: | :---------: | :--------------------: | :------------------: |
|    `DoubleSupplier`    |  `double`   |    `getAsDouble()`     |         `0`          |
|     `IntSupplier`      |    `int`    |      `getAsInt()`      |         `0`          |
|     `LongSupplier`     |   `long`    |     `getAsLong()`      |         `0`          |
|    `DoubleConsumer`    |   `void`    |       `accept()`       |     `1 (double)`     |
|     `IntConsumer`      |   `void`    |       `accept()`       |      `1 (int)`       |
|     `LongConsumer`     |   `void`    |       `accept()`       |      `1 (long)`      |
|   `DoublePredicate`    |  `boolean`  |        `test()`        |     `1 (double)`     |
|     `IntPredicate`     |  `boolean`  |        `test()`        |      `1 (int)`       |
|    `LongPredicate`     |  `boolean`  |        `test()`        |      `1 (long)`      |
|  `DoubleFunction<R>`   |     `R`     |       `apply()`        |     `1 (double)`     |
|    `IntFunction<R>`    |     `R`     |       `apply()`        |      `1 (int)`       |
|   `LongFunction<R>`    |     `R`     |       `apply()`        |      `1 (long)`      |
| `DoubleUnaryOperator`  |  `double`   |   `applyAsDouble()`    |     `1 (double)`     |
|   `IntUnaryOperator`   |    `int`    |     `applyAsInt()`     |      `1 (int)`       |
|  `LongUnaryOperator`   |   `long`    |    `applyAsLong()`     |      `1 (long)`      |
| `DoubleBinaryOperator` |  `double`   |   `applyAsDouble()`    | `2 (double, double)` |
|  `IntBinaryOperator`   |    `int`    |     `applyAsInt()`     |    `2 (int, int)`    |
|  `LongBinaryOperator`  |   `long`    |    `applyAsLong()`     |   `2 (long, long)`   |

### Primitive- specific functional interfaces

|    Functional Interface    | Return Type | Single Abstract Method | # of Parameters |
| :------------------------: | :---------: | :--------------------: | :-------------: |
|   `ToDoubleFunction<T>`    |  `double`   |    `applyAsDouble`     |      1 (T)      |
|     `ToIntFunction<T>`     |    `int`    |      `applyAsInt`      |      1 (T)      |
|    `ToLongFunction<T>`     |   `long`    |     `applyAsLong`      |      1 (T)      |
| `ToDoubleBiFunction<T, U>` |  `double`   |    `applyAsDouble`     |    2 (T, U)     |
|  `ToIntBiFunction<T, U>`   |    `int`    |      `applyAsInt`      |    2 (T, U)     |
|  `ToLongBiFunction<T, U>`  |   `long`    |     `applyAsLong`      |    2 (T, U)     |
|   `DoubleToIntFunction`    |    `int`    |      `applyAsInt`      |   1 (double)    |
|   `DoubleToLongFunction`   |   `long`    |     `applyAsLong`      |   1 (double)    |
|   `IntToDoubleFunction`    |  `double`   |    `applyAsDouble`     |     1 (int)     |
|    `IntToLongFunction`     |   `long`    |     `applyAsLong`      |     1 (int)     |
|   `LongToDoubleFunction`   |  `double`   |    `applyAsDouble`     |    1 (long)     |
|    `LongToIntFunction`     |    `int`    |      `applyAsInt`      |    1 (long)     |
|   `ObjDoubleConsumer<T>`   |   `void`    |        `accept`        |  2 (T, double)  |
|    `ObjIntConsumer<T>`     |   `void`    |        `accept`        |   2 (T, int)    |
|    `ObjLongConsumer<T>`    |   `void`    |        `accept`        |   2 (T, long)   |

### Rules for accessing a variable from a lambda body inside a method

|   Variable Type   |                 Rule                  |
| :---------------: | :-----------------------------------: |
| Instance variable |                Allowed                |
|  Static variable  |                Allowed                |
|  Local variable   | Allowed if final or effectively final |
| Method parameter  | Allowed if final or effectively final |
| Lambda parameter  |                Allowed                |
