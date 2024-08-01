# Making Decisions

## Shortening Code with Pattern Matching

* executes a section of code that meets certain criteria starting from java 16
* it is a bad practice to reassign a pattern variable since doing so can lead to ambiguity about what is and is not
  in scope. The reassignment can be prevented with a `final` modifier

```java
    // OLD
void compareIntegers(Number number) {
    if (number instanceof Integer data) {
        System.out.print(data.compareTo(5));
    }
}

// NEW
void compareIntegers(Number number) {
    // data is called pattern variable
    // creates variable of Integer type if the condition is true only
    if (number instanceof final Integer data) { // to avoid ClassCastException
        data = 10; // DOES NOT COMPILE
        System.out.print(data.compareTo(5));
    }
}
```

* The type of the pattern `variable` must be a subtype of the `variable` on the left side of the
  expression. **It also cannot be the same type**
* pattern variable isn't only in scope inside the if statement. it is possible to use
  a pattern variable outside the if statement, but only when the compiler can definitively
  determine its type.
* `data` is only in scope when that make sense of the condition is true to be in scope

```java
    //1
void printOnlyIntegers(Number number) {
    if (!(number instanceof Integer data))
        return;
    System.out.print(data.intValue());
}

//Make sure to rewrite it again 
void printOnlyIntegers(Number number) {
    if (number instanceof Integer data)
        System.out.print(data.intValue());
    else {
    }
}
```

## Applying switch Statements and Expression

* switch statement has a target variable that is not evaluated until
  runtime
    * `var` is accepted if it's one of the below types
    * primitive types -> `byte`,`char`,`short`,`int`
    * reference types -> `Byte`,`Character`,`Short`,`Integer`,`String`,`enum values`
* the values in each `case` statement must be compile-time constant and same data type as `switch` value, so you can use only literals, enum constants, or `final` constant variables of the same data type.
* can’t have a case statement value that requires executing a method at runtime, even if that method always returns the same value.

```java
int animal = 5;
switch(animal){
    case 1, 2: System.out.print("Lion");
    case 3:System.out.print("Tiger");
}
  ```

* switch expression must return a data type that is compatible with the assignment.

```java
int days = 2;
var result = switch (days) {
    case 0 -> "Sunday";
    case 1 -> {
        System.out.println("Prints");
        yield "Hi";
    }
    default -> "Default must be added in the switch expression";
};
```

```java
    for (initialization; booleanExpression; updateStatement)
        // 1 Initialization statement executes
        // 2 If booleanExpression is true, continue; else exit loop
        // 3 Body executes
        // 4 Execute updateStatement
        // 5 Return to Step 2
```

* `for-each` loops are used with 
  * A built-in Java array
  * An object whose type implements java.lang.Iterable
* inner loop `break` = outer loop `continue`