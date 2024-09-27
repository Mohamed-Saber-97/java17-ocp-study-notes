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
* conversion from a subtype to a supertype called **_upcasting_** and the other way around is called **_downcasting_**
* **_downcasting_** require a runtime check and can throw a `ClassCastException`
* Unboxing a wrapper reference that has the `null` value results in a `NullPointerException`
```java
Integer iRef = 10; // (1) Implicit boxing: Integer <----- int
Double dRef = Double.valueOf(3.14); // (2) Explicit boxing: Double <----- double
int i = iRef; // (3) Implicit unboxing: int <----- Integer
double d = dRef.doubleValue(); // (4) Explicit unboxing: double <----- Double
```

## Precedence and Associativity Rules For Operators

## Evaluation Order Of Operands

## The Simple Assignment Operator

## Arithmetic Operators

## The Binary String Concatenation Operator +

## Variable Increment and Decrement Operator ++,--

## Boolean Expression

## Relational Operators <, <=, >, >=

## Equality

## Boolean logical Operators !, ^, &, |

## Conditional Operators: &&, ||

## Integer Bitwise Operators: ~, &, |, ^

## Shift Operators: <<, >>, >>>

## The Conditional Operator ?:

## Other Operators: new, [], instanceof, ->