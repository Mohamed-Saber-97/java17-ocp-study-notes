# Class Design

## Understanding Inheritance
* Trying to declare a top-level class with `protected` or `private` class will lead to a compiler error.
* When one class inherits from a parent class, all `public` and `protected` members are automatically available as part of the child class.
* The parent class can have `private` members that can hold data or modify an object. The subclass doesn’t have direct access to them.
### Class Modifiers
| Class Modifier |                                             Description                                             |
|:--------------:|:---------------------------------------------------------------------------------------------------:|
|    `final`     |                                    The class may not be extended                                    |
|   `abstract`   | The class is abstract, may contain abstract methods,and requires a concrete subclass to instantiate |
|    `sealed`    |                    The class may only be extended by a specific list of classes                     |
|  `non-sealed`  |                 A subclass of a sealed class permits potentially unnamed subclasses                 |
|    `static`    |                        Used for static nested classes defined within a class                        |

```java
class Insect {
    protected int numberOfLegs = 4;
    String label = "buggy";
}

public class Beetle extends Insect {
    protected int numberOfLegs = 6;
    short age = 3;

    public void printData() {
        // accessible via both this and super references
        System.out.println(this.label);
    }
}
```
* `this` includes current and inherited members, `super` only includes inherited members
* When Java sees the `new` keyword, it allocates memory for the new `object`. It then looks for a `constructor` with a matching signature and calls it
*  compiler only *inserts the default constructor when no constructors are defined*.
* Calling `this` or `super` should be the **first** statement in the `method`
* **rules you should know about constructors**
  * A class can contain many overloaded constructors, provided the signature for each is distinct.
  * The compiler inserts a default no-argument constructor if no constructors are declared
  * The first line of every constructor is a call to a parent constructor using `super()` or an overloaded constructor using `this()`
  * If the constructor does not contain a `this()` or `super()` reference, then the compiler automatically inserts `super()` with no arguments as the first line of the constructor
  * Java does not allow cyclic constructor calls
* super() Always Refers to the Most Direct Parent
```java
public Hamster(int weight) {
    System.out.println("chew");
    // Set weight and default color
    this(weight, "brown"); // DOES NOT COMPILE as it needs to be in the first line
    this(8);//DOES NOT COMPILE as it calls itself infinitely
}
```
## Initializing Objects
* We initialize the class, which involves invoking all `static` members in the class hierarchy, starting with the highest superclass and working downward. This is sometimes referred to as _loading_ the class.
* Class initialization is that it happens at most once for each class. The class may also never be loaded if it is not used in the program
* **The class may be initialized when:**
  * The program first starts
  * When a static member of the class is referenced
  * Shortly before an instance of the class is created
* **the order of initialization for a class**
  * If there is a superclass Y of X, then initialize class Y first
  * Process all static variable declarations in the order in which they appear in the class
  * Process all static initializers in the order in which they appear in the class
* **_one important rule: by the time the `constructor` completes, all `final` instance variables must be assigned a value exactly once_**
* Unlike local final variables, which are not required to have a value unless they are actually used, final instance variables must be assigned a value
* **Initialize Instance of X**
  * Initialize class X if it has not been previously initialized
  * If there is a superclass Y of X, then initialize the instance of Y first
  * Process all instance variable declarations in the order in which they appear in the class
  * Process all instance initializers in the order in which they appear in the class.
  * Initialize the constructor, including any overloaded constructors referenced with this().
* _**We conclude this section by listing important rules you should know:**_
  * A class is initialized at most once by the JVM before it is referenced or used
  * All static `final variables` must be assigned a value exactly once, either when they are declared or in a static 
    initializer
  * All `final` fields must be assigned a value exactly once, either when they are declared, in an instance initializer, or in a constructor
  * Non-`final static` and instance variables defined without a value are assigned a default value based on their type.
  * Order of initialization is as follows: variable declarations, then initializers, and finally constructors
## Inheriting Members

* **_object can be used in a variety of ways, in part based on the reference variable used to call the object_**
* `overriding` a method occurs when a subclass declares a new implementation for an inherited method with the same 
  **_signature_** and **_compatible return type_**
* **_To override a method, you must follow a number of rules. The compiler performs the following checks when you override a method:_**
  * The method in the child class must have the same signature as the method in the parent class
  * The method in the child class must be at least as accessible as the method in the parent class
  * The method in the child class may not declare a checked exception that is new or broader than the class of any 
    exception declared in the parent class method
  * If the method returns a value, it must be the same or a subtype of the method in the parent class, known as _**covariant return**_ types
    * > A simple test for covariance is the following: given an inherited return
      type A and an overriding return type B, can you assign an instance of B
      to a reference variable for A without a cast? If so, then they are covariant.
      This rule applies to primitive types and object types alike. If one of the
      return types is void, then they both must be void, as nothing is covariant with void except itself
* you can’t override `private` methods since they are not inherited. Java permits you to redeclare a new method in the child class with the same or modified
  signature as the method in the parent class
* A static method cannot be overridden because class objects do not inherit from each other in the same way as instance objects. On the other hand, they can be hidden
* A hidden method occurs when a child class defines a static method with the same name and signature as an
  inherited static method defined in a parent class
* **_a new rule is added to the previous 4 of overriding_** 
  * The method defined in the child class must be marked as static if it is marked as static in a parent class.
* Java doesn’t allow variables to be overridden. Variables can be hidden, though
* **_Overriding a method replaces the parent method on all reference variables (other than super), whereas hiding a 
  method or variable replaces the member only if a child reference type is used_**
* By marking a method final, you forbid a child class from replacing this method either by overriding or hiding.

## Creating Abstract Classes
* Used when we want to force all objects of a class to have a particular type at runtime
* **_There are some rules you need to be aware of:_**
  * Only instance methods can be marked abstract within a class, not variables, constructors, or `static` methods
  * An abstract method can only be declared in an abstract class
  * A non-abstract class that extends an abstract class must implement all inherited abstract methods
  * Overriding an abstract method follows the existing rules for overriding methods.
* abstract classes are initialized with constructors in the same way as non-abstract classes
* Java does not permit a class or method to be marked both `abstract` and `final`, `private` or `static`

## Creating Immutable Objects
*  **_Strategy for making a class immutable:_**
  * Mark the class as `final` or make all of the constructors `private`
  * Mark all the instance variables private and final.
  * Don’t define any setter methods
  * Don’t allow referenced mutable objects to be modified.
  * Use a constructor to set all properties of the object, making a copy if needed. This can be an expensive operation if called frequently by the caller
