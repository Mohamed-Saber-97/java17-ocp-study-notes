# Methods
## Designing Methods
* **This chapter primarily concerned with access modifiers applied to methods and fields only not classes**
```java
//Example
public final void nap(int minutes) throws InterruptedException {
 // take a nap
}
```
|      Element       |   Value in `nap()` example    |             Required?             |
|:------------------:|:-----------------------------:|:---------------------------------:|
|  Access modifier   |           `public`            |                No                 |
| Optional specifier |            `final`            |                No                 |
|    Return type     |            `void`             |                Yes                |
|    Method name     |             `nap`             |                Yes                |
|   Parameter list   |        `(int minutes)`        | Yes, but can be empty parentheses |
|  Method signature  |      `nap(int minutes)`       |                Yes                |
|   Exception list   | `throws InterruptedException` |                No                 |
|    Method body     |          `{//BODY}`           | Yes, except for abstract methods  |

* The method name and parameter list are *method signature* that provides instructions for how callers can reference this method
    * **Doesn't include `return` type and access modifiers**
* it is common to list the access modifier first. method are written in a specific order: name, parameter list, exception list, body.

### Access Modifiers
* Determines what classes a method can be accessed from like a security guard
  * **`private`** -> Only accessible within the same class
  * **package access**  -> : `private` plus other members of the same package. Sometimes referred to as package-private or `default` access.
  * **protected** -> package access plus access within subclasses.The subclass gains access to all `protected` and `public` members of the parent class 
  * **public** -> `protected` plus classes in the other packages. 
* By extending a class, the subclass gains access to all protected and public members of the parent class, as if they were declared in the subclass. If the two classes are in the same package, then the subclass also gains access to all package members.

```java
public class ParkTrip {
 public void skip1() {}
 //default is not a valid access modifier
 default void skip2() {} // DOES NOT COMPILE
 void public skip3() {} // DOES NOT COMPILE
 void skip4() {}
}
```
### Optional specifiers for methods

|    Modifier    |                                                  Description                                                  |
|:--------------:|:-------------------------------------------------------------------------------------------------------------:|
|    `static`    |                          Indicates the method is a member of the shared class object                          |
|   `abstract`   |                    Used in an abstract class or interface when the method body is excluded                    |
|    `final`     |                         Specifies that the method may not be overridden in a subclass                         |
|   `default`    | Used in an interface to provide a default implementation of a method for classes that implement the interface |
| `synchronized` |                                         Used with multithreaded code                                          |
|    `native`    |                   Used when interacting with code written in another language, such as C++                    |
|   `strictfp`   |                             Used for making floating-point calculations portable                              |

### Method Name and Return Types
```java
//There is a return statement, but it doesn’t always get run.
//The compiler won’t fully evaluate the if statement and requires a return statement if this condition is false.
String hike7(int a) { // DOES NOT COMPILE
 if (1 < 2) return "orange";
 }
//The code compiles, although the compiler will produce a warning about unreachable code (or dead code)
String hike8(int a) {
    if (1 < 2) return "orange";
    return "apple"; // COMPILER WARNING
}
```
* A method signature, composed of the method name and parameter list and Java uses to uniquely determine exactly which method you are attempting to call.
* names of the parameters in the method are not part of the *method signature*. only the _**types**_ of parameters and their _**order**_.
* Rules for Creating a Method with a Varargs Parameter
    * A method can have at most one varargs parameter
    * If a method contains a varargs parameter, it must be the last parameter in the list.
* Method _**overloading**_ occurs when methods in the same class have the same **_name_** but different method **_signatures_**, which means they use different **_parameter lists_**
* Everything other than the method name can vary for overloading methods. This means there can be different access modifiers, optional specifiers (like static), return types, and exception lists.

```java
public class Trip {
 public void visitZoo(String name, int waitTime) {}//Signature -> visitZoo(String,int);
 public void visitZoo(String attraction, int rainFall) {} //Signature -> visitZoo(String,int); SO IT DOES NOT COMPILE
}
```
## Declaring Local and Instance Variables
* marking a local variable final is often a good practice
* all local variable references are destroyed after the block is executed, but the objects they point to may still be accessible.
* `final int rest; rest = 5;` we don’t need to assign a value when a final variable is declared. It needs to be done before it's used.
* The final attribute only refers to the variable reference
  * The reference for the `primitive` types are the value stored in memory
  * The reference for the `objects` are the value (address of object) stored in memory.
*  _**effectively final**_ local variable is one that is not modified after it is assigned.
  * add the final keyword to the variable. If the code still compiles, it is effectively final.
* **Effective Final Parameters -> “Lambdas and Functional Interfaces can only reference local variables that are final or effectively final**
### Instance Variable Modifiers
*  If an instance variable is marked final, then it must be assigned a value when it is declared or when the object is instantiated.
* The compiler does not apply a default value to final variables
* A final instance or final static variable must receive a value when it is declared or as part of initialization.

|  Modifier   |                                              Description                                              |
|:-----------:|:-----------------------------------------------------------------------------------------------------:|
|   `final`   | Specifies that the instance variable must be initialized with each instance of the class exactly once |   
| `volatile`  |          Instructs the JVM that the value in this variable may be modified by other threads           |   
| `transient` |          Used to indicate that an instance variable should not be serialized with the class           |   

## Reviewing Access Modifiers

|                                           | `private` | **package** | `protected` | `public` |
|:-----------------------------------------:|:---------:|:-----------:|:-----------:|:--------:|
|              the same class               |    Yes    |     Yes     |     Yes     |   Yes    |
|     another class in the same package     |    No     |     Yes     |     Yes     |   Yes    |
|     a subclass in a different package     |    No     |     No      |     Yes     |   Yes    |
| an unrelated class in a different package |    No     |     No      |     No      |   Yes    |

## Accessing static Data
* You can use an instance of the object to call a static method. The compiler checks for the type of the reference 
  and uses that instead of the object. **even if the objects points to `null` the static data can still be called**.
* Remember to look at the reference type not the value for a variable when you see a static method or variable
* the static blocks, initializers are the only ways to initialize the `static final` variables
* `static imports` are only for importing `members` like a `method` or `variable` and not a **class**
* If a variable is `static final`, it must be set exactly once, and it must be in the declaration line or in a 
  `static` initialization block, and you can't set it in `main()`
## Autoboxing and Unboxing Variables
```java
//Java will cast or autobox the value automatically, but not both at the same time.
Long badGorilla = 8; // DOES NOT COMPILE
```
* calling any method on `null` gives a `NullPointerException` so we can't unbox a `null`