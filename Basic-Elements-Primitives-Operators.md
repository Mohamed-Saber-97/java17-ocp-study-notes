# Basic Elements, Primitive Data Types, and Operators

## Basic Language Elements

* Identifiers can contain characters A-Z, a-z, 0-9, A connecting punctuation character (such as underscore _) and any
  currency symbol (such as $, ¢, ¥, or £) are also allowed as letters
* the underscore (_) on its own is not a legal identifier name, but a keyword
* Examples of Legal Identifiers
    * number, Number, sum_$, bingo, $$_100, _007, mål, grüß
* Examples of Illegal Identifiers
    * `48chevy` can't start with digit
    * `all@hands` @ is not a legal character in an identifier. It is also not a legal operator
    * `grand-sum` The character `-` is not a legal character in an identifier, but it is a legal operator
    * `_` An underscore (_) by itself is not a legal identifier.
* All Java keywords are lowercase, and incorrect usage results in compile-time errors.
* The default data type of integer literal is always `int`, but it can be specified as `long` by appending the
  suffix `L`
* Examples of Legal Use of Underscores in Numerical Literals
    * 0b0111_1111_1111_1111_1111_1111_1111_1111
    * 0_377_777_777
    * -123_456.00
    * 2009__08__13
    * 0xff_ff_ff_ff
    * 1_2.345_678e1_2
    * 49_03_01d
* Examples of Illegal Use of Underscores in Numerical Literals
    * _0_b_01111111111111111111111111111111_
    * _0377777777_
    * _0_x_ffffffff_
    * +_123456_._00_
    * _12_._345678_e_12_
    * _20090813_
    * _490301_d_
* the Unicode values \u000a and \u000d should not be used to represent a newline and a carriage return in the source
  code
* These values are interpreted as line-terminator characters by the compiler and will cause compile-time errors. You
  should use the escape sequences \n and \r, respectively
* trying to nest multiple-line comments will result in a compile-time error

## Primitive Data Types

* Primitive data types in Java can be divided into three main categories:
    * **_Integral types_**: represent signed integers (`byte`, `short`, `int`, `long`) and unsigned character
      values (`char`)
    * **_Floating-point types_** (`float`, `double`): represent fractional signed numbers
    * **_Boolean type_** (`boolean`): represents logical values
* Primitive data values are not objects, but each primitive data type has a corresponding wrapper class that can be used
  to represent a primitive value as an object.

## Conversions

* For the primitive data types the value of a **_narrower_** data type can be converted to a value of a **_wider_** data
  type. This is called a **_widening primitive conversion_**.
* Widening Primitive Conversions
    * byte -> short, char -> int -> long -> float -> double
* conversions between char and the two integer types byte and short are considered narrowing primitive conversions.
* For the reference data types conversion from a subtype to a supertype called **_upcasting_** and the other way
  around is called **_downcasting_**
* **_downcasting_** require a runtime check and can throw a `ClassCastException` at runtime if the object is not of the
  subclass type.
* Unboxing a wrapper reference that has the `null` value results in a `NullPointerException`

```java
Integer iRef = 10; // (1) Implicit boxing: Integer <----- int
Double dRef = Double.valueOf(3.14); // (2) Explicit boxing: Double <----- double
int i = iRef; // (3) Implicit unboxing: int <----- Integer
double d = dRef.doubleValue(); // (4) Explicit unboxing: double <----- Double
```

### Assignment Conversion

* An assignment conversion converts the type of expression to the type of target variable.
* `byte b = 10; // Narrowing conversion: byte <--- int`

### Method Invocation Context

* Method invocation conversions do not include the implicit narrowing conversion performed for non-long integral
  constant expressions.

```java
// Assignment: (1) Implicit narrowing followed by (2) boxing.
Character space1 = 32; // Character <-(2)-- char <-(1)-- int
// Invocation of method with signature: valueOf(char)
Character space2 = Character.valueOf(32); // Compile-time error!, Call signature: valueOf(int)
Character space3 = Character.valueOf((char) 32); // OK!, Call signature: valueOf(char)
```

### Casting Context of the Unary Type Cast Operator (type)

* Java, being a strongly typed language, checks for type compatibility (i.e., it checks whether a type can substitute
  for another type in a given context) at **_compile time_**.
* some checks are possible only at runtime (e.g., which type of object a **_reference_** actually denotes during
  execution)
* At runtime, a cast results in a new value of type, which best represents the value of the expression in the old type.

```java
public static void main() {
    long l = (long) 10;// Widening primitive conversion: long <--- int
    int i = (int) l;// (1) Narrowing primitive conversion: int <--- long
    Object obj = (Object) "7Up"; // Widening ref conversion: Object <--- String
    String str = (String) obj;// (2) Narrowing ref conversion: String <--- Object
    Integer iRef = (Integer) i;// Boxing: Integer <--- int
    i = (int) iRef;// Unboxing: int <--- Integer
}
```
* Casting can be applied to primitive values as well as references.
* Casting between primitive data types and reference types is not permitted, except where boxing and unboxing is applicable.
* Boolean values cannot be cast to other data values, and vice versa.
* The reference literal null can be cast to any reference type.
### Numeric Promotion Context
#### Unary Numeric Promotion
* Unary numeric promotion proceeds as follows:
  * If f the single operand is of type `Byte`, `Short`, `Character`, or `Integer`, it is unboxed. If the resulting value is narrower than `int` , it is promoted to a value of type `int` by a widening conversion.
  * Otherwise, if the single operand is of type `Long`, `Float`, or `Double`, it is unboxed.
  * Otherwise, if the single operand is of a type narrower than `int`, its value is promoted to a value of type `int` by a widening conversion.
  * Otherwise, the operand remains unchanged.
* **_unary numeric promotion results in an operand value that is either `int` or wider_**
* Unary numeric promotion is applied in the following expressions:
  * Operand of the unary arithmetic operators `+` and `-`
  * Array creation expression; for example, new int[20], where the dimension expression (in this case, 20) must evaluate to an int value
  * Indexing array elements; for example, objArray['a'], where the index expression (in this case, 'a') must 
    evaluate to an int value
#### Binary Numeric Promotion
* Binary numeric promotion implicitly applies appropriate widening primitive conversions so that the widest numeric type of a pair of operands is always at least `int`.
* If `T` is wider than `int`, both operands are converted to `T`; otherwise, both operands are converted to `int`.
* This means that the resulting type of the operands is at least `int`.
* Binary numeric promotion is applied in the following expressions:
  * Operands of the arithmetic operators `*`, `/`, `%`, `+`, and `-`
  * Operands of the relational operators `<`, `<=`, `>`, and `>=`
  * Operands of the numerical equality operators `==` and `!=`
  * Operands of the conditional operator `? :`, under certain circumstances
## Precedence and Associativity Rules For Operators
*  Parentheses, (), can be used to override precedence and associativity.
* All operators not identified previously as unary or ternary are binary—that is, they require two operands.
* 

| Precedence |                          Operator                          |                            Symbols and examples                            |  Evaluation   |
|:----------:|:----------------------------------------------------------:|:--------------------------------------------------------------------------:|:-------------:|
|     1      | Array element access, member access, and method invocation |                              `[]`, `.`, `()`                               | Left-to-right |
|     2      |                  Unary postfix operators                   |                       `expression++`, `expression--`                       | Left-to-right |
|     3      |                   Unary prefix operators                   |   `++expression`, `--expression`, `+expression`, `-expression`, `~`, `!`   | Right-to-left |
|     4      |                  Cast and object creation                  |                              `(type)`, `new`                               | Right-to-left |
|     5      |                       Multiplicative                       |                               `*`, `/`, `%`                                | Left-to-right |
|     6      |                          Additive                          |                                  `+`, `-`                                  | Left-to-right |
|     7      |                      Shift operators                       |                             `<<`, `>>`, `>>>`                              | Left-to-right |
|     8      |                    Relational operators                    |                     `<`, `>`, `<=`, `>=`, `instanceof`                     | Left-to-right |
|     9      |                          Equality                          |                                 `==`, `!=`                                 | Left-to-right |
|     10     |                        Bitwise AND                         |                                    `&`                                     | Left-to-right |
|     11     |                        Bitwise XOR                         |                                    `^`                                     | Left-to-right |
|     12     |                         Bitwise OR                         |                                    `\|`                                    | Left-to-right |
|     13     |                        Logical AND                         |                                    `&&`                                    | Left-to-right |
|     14     |                         Logical OR                         |                                   `\|\|`                                   | Left-to-right |
|     15     |                      Ternary operator                      |              `boolean_expression ? expression1 : expression2`              | Right-to-left |
|     16     |                    Assignment operators                    | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `^=`, `\|=`, `<<=`, `>>=`, `>>>=` | Right-to-left |
|     17     |                     Lambda expression                      |                                    `->`                                    | Right-to-left |

## The Simple Assignment Operator
* Copying reference values by assignment creates **_aliases_**.
* Reference assignment also does not copy the state of the source object to any object denoted by the reference variable on the left-hand side.
```java
Pizza pizzaOne, pizzaTwo;
pizzaOne = pizzaTwo = new Pizza("Supreme"); // Aliases
int[] a = {10, 20, 30, 40, 50}; // An array of int
int index = 4;
a[index] = index = 2;
// The evaluation proceeds as follows
a[index] = index = 2;
a[4] = index = 2;
a[4] = (index = 2); // index gets the value 2. = is right associative.
a[4] = 2; // The value of a[4] is changed from 50 to 2.
int v1 = v2 = 2016; // Only v1 is declared. Compile-time error!
```
### Valid narrowing primitive conversions without cast:
```java
// Conditions fulfilled for implicit narrowing primitive conversions.
short s1 = 10; // int value in range.
short s2 = 'a'; // char value in range.
char c1 = 32; // int value in range.
char c2 = (byte)35; // byte value in range. (int value in range, without cast.)
byte b1 = 40; // int value in range.
byte b2 = (short)40; // short value in range. (int value in range, without cast.)
final int i1 = 20; // Constant variable
byte b3 = i1; // final value of i1 in range.
```
### All other narrowing primitive conversions will produce a compile-time error on assignment and will explicitly require a cast:
```java
// Conditions not fulfilled for implicit narrowing primitive conversions.
// A cast is required.
int i2 = -20; // i2 is not a constant variable. i2 is not final.
final int i3 = i2; // i3 is not a constant variable, since i2 is not.
final int i4 = 200; // i4 is a constant variable.
final int i5; // i5 is not a constant variable.
short s3 = (short) i2; // Not constant expression.
char c3 = (char) i3; // Final value of i3 not determinable at compile time.
char c4 = (char) i2; // Not constant expression.
byte b4 = (byte) 128; // int value not in range.
byte b5 = (byte) i4; // Value of constant variable i4 is not in range.
i5 = 100; // Initialized at runtime.
short s4 = (short) i5; // Final value of i5 not determinable at compile time.
```
### boxing and unboxing in an assignment context:
```java
Boolean boolRef = true; // Boxing.
Byte bRef = 2; // Constant in range: narrowing, then boxing.
// Byte bRef2 = 257; // Constant not in range. Compile-time error!
short s = 10; // Narrowing from int to short.
// Integer iRef1 = s; // short not assignable to Integer.
Integer iRef3 = (int) s; // Explicit widening with cast to int and boxing
boolean bv1 = boolRef; // Unboxing.
byte b1 = bRef; // Unboxing.
int iVal = bRef; // Unboxing and widening.
Integer iRefVal = null; // Always allowed.
// int j = iRefVal; // NullPointerException at runtime.
if (iRef3 != null) iVal = iRef3; // Avoids exception at runtime.
```
## Arithmetic Operators
* Integer arithmetic always returns a value that is in range, except in the case of integer division by zero and remainder by zero, which cause an `ArithmeticException`.
* Adding or multiplying two very large floating-point numbers or division by zero can result in **_infinity_**.
* Floating-point arithmetic can also result in **_underflow_** to zero.
* Calculating the square root of –1 or `0.0/0.0` results in a NaN.
* Any operation involving NaN produces NaN. Any comparison (except inequality, !=) involving NaN and any other value 
  (including NaN) returns false.An inequality comparison of NaN with another value (including NaN) always returns true
* decrementing a literal would result in compile-time error.
## Variable Increment and Decrement Operator ++,--
* `++i` adds 1 to the value in `i`, and stores the new value in `i`. It returns the new value as the value of the expression
* `j++` adds 1 to the value in `j`, and stores the new value in `j`. It returns the original value that was in `j` as the value of the expression.
## Relational Operators <, <=, >, >=
```java
int a = 1, b = 7, c = 10;
boolean illegal = a <= b <= c; // (1) Illegal. Compile-time error!
boolean valid2 = a <= b && b <= c; // (2) OK.
```
## Equality
```java
Pizza pizzaA = new Pizza("Sweet&Sour"); // new object
Pizza pizzaB = new Pizza("Sweet&Sour"); // new object
Pizza pizzaC = new Pizza("Hot&Spicy"); // new object
String banner = "Come and get it!"; // new object
boolean test = banner == pizzaA; // (1) Compile-time error
boolean test1 = pizzaA == pizzaB; // false
boolean test2 = pizzaA == pizzaC; // false
pizzaA = pizzaB; // Denote the same object; are aliases
boolean test3 = pizzaA == pizzaB; // true
```
```java
// Equality for String objects means identical character sequences.
String movie1 = new String("The Revenge of the Exception Handler");
String movie2 = new String("High Noon at the Java Corral");
String movie3 = new String("The Revenge of the Exception Handler");
boolean test0 = movie1.equals(movie2); // false.
boolean test1 = movie1.equals(movie3); // true.
// Equality for wrapper classes means same type and same primitive value.
Boolean flag1 = true; // Boxing.
Boolean flag2 = false; // Boxing.
boolean test2 = flag1.equals("true"); // false. Not same type.
boolean test3 = flag1.equals(!flag2); // true. Same type and value.
Integer iRef = 100; // Boxing.
Short sRef = 100; // Boxing <--- short <--- int
boolean test4 = iRef.equals(100); // true. Same type and value.
boolean test5 = iRef.equals(sRef); // false. Not same type.
boolean test6 = iRef.equals(3.14); // false. Not same type.
// The Pizza class does not override the equals() method, so we can use either
// equals() method inherited from the Object class or equality operator ==.
Pizza pizza1 = new Pizza("Veggies Delight");
Pizza pizza2 = new Pizza("Veggies Delight");
Pizza pizza3 = new Pizza("Cheese Delight");
boolean test7 = pizza1.equals(pizza2); // false.
boolean test8 = pizza1.equals(pizza3); // false.
boolean test9 = pizza1 == pizza2; // false.
pizza1 = pizza2; // Creates aliases.
boolean test10 = pizza1.equals(pizza2); // true.
boolean test11 = pizza1 == pizza2; // true.
```