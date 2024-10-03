# Beyond Classes

## Implementing Interfaces

* To add a method body for an interface method we either mark it as `default` or `static`
* an interface cannot be instantiated or marked as `final` for the same reason that abstract classes cannot be marked as `final`. In other words, marking an `interface final` implies no class could ever implement it.
* the implicit modifiers for interfaces
  * Interfaces are implicitly `abstract`.
  * Interface variables are implicitly `public`, `static`, and `final`.
  * Interface methods without a body are implicitly `abstract`.
  * Interface methods without the `private` modifier are implicitly `public`
* The **concrete** `class` implementing the `interface` must `override` the `abstract` methods and explicitly declare the access modifier as `public`
* Unlike a `class`, which can extend only one `class`, an `interface` can extend multiple `interfaces`

> Extending two interfaces is permitted because interfaces are not initialized as part of a class hierarchy. Unlike abstract classes, they do not contain constructors and are not part of instance initialization. Interfaces simply define a set of rules and methods that a class implementing them must follow

* class can implement an interface, a class cannot extend an interface and interface can extend another interface, an interface cannot implement another interface

#### Interface member types

|                         | Membership type | Required modifiers |     Implicit modifiers      | Has value or body |
| :---------------------: | :-------------: | :----------------: | :-------------------------: | :---------------: |
|    Constant variable    |      Class      |         -          | `public`, `static`, `final` |        Yes        |
|    `abstract` method    |    Instance     |         -          |    `public`, `abstract`     |        No         |
|    `default` method     |    Instance     |     `default`      |          `public`           |        Yes        |
|     `static` method     |      Class      |      `static`      |          `public`           |        Yes        |
|    `private` method     |    Instance     |     `private`      |              -              |        Yes        |
| `private static` method |      Class      | `private`,`static` |              -              |        Yes        |

* Default Interface Method Definition Rules
  * A `default` method may be declared only within an interface
  * A `default` method must be marked with the `default` keyword and include a method body.
  * A `default` method is implicitly `public`.
  * A `default` method cannot be marked `abstract`, `final`, or `static`.
  * A `default` method may be overridden by a class that `implements` the `interface`
  * If a `class` inherits two or more `default` methods with the same method signature, then the class must `override` the method

```java
public class Cat implements Walk, Run {
    public int getWalkSpeed() {
        return Walk.super.getSpeed();//Interface.super.methodName
    }
}
```

* Static Interface Method Definition Rules
  * A `static` method must be marked with the `static` keyword and include a method body
  * A `static` method without an access modifier is implicitly `public`.
  * A `static` method cannot be marked `abstract` or `final`
  * A static `method` is not inherited and cannot be accessed in a class implementing the interface without a reference to the interface name.
* Java “solved” the multiple inheritance problem of `static` interface methods by not allowing them to be inherited
* Private Interface Method Definition Rules
  * A `private` interface method must be marked with the `private` modifier and include a method body
  * A `private` `static` interface method may be called by any method within the interface definition
  * A `private` interface method may only be called by `default` and other `private` nonstatic methods within the interface definition

#### Interface member access

Accessible without an instance of the interface?

|                         | Accessible from default and private methods within the interface? | Accessible from static methods within the interface? | Accessible from methods in classes inheriting the interface? | Accessible without an instance of the interface? |
| :---------------------: | :---------------------------------------------------------------: | :--------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------: |
|    Constant variable    |                                Yes                                |                         Yes                          |                             Yes                              |                       Yes                        |
|    `abstract` method    |                                Yes                                |                          No                          |                             Yes                              |                        No                        |
|    `default` method     |                                Yes                                |                          No                          |                             Yes                              |                        No                        |
|     `static` method     |                                Yes                                |                         Yes                          |                 Yes (interface namerequired)                 |           Yes (interface namerequired)           |
|    `private` method     |                                Yes                                |                          No                          |                              No                              |                        No                        |
| `private static` method |                                Yes                                |                         Yes                          |                              No                              |                        No                        |

* **some quick tips**
  * Treat `abstract`, `default`, and **non-static** `private` methods as belonging to an instance of the interface.
  * Treat `static` methods and variables as belonging to the interface class object.
  * All `private` interface method types are only accessible within the interface declaration

## Working with Enums

* Enumerations show up whenever you have a set of items whose types are known at compile time
* you can use `equals()` or `==` to compare `enums`, since each enum value is initialized only once in the JVM
* **You can't extend an `enum` or compare an `int` and an `enum` value directly anyway since an enum is a type and not primitive int**
* All enum constructors are implicitly `private` and won't work if it's `public` or `protected`

```java
public enum Season {
    WINTER("Low"), SPRING("Medium"), SUMMER("High"), FALL("Medium");
    private final String expectedVisitors;//private and final

    private Season(String expectedVisitors) {
        this.expectedVisitors = expectedVisitors;
    }

    public void printExpectedVisitors() {
        System.out.println(expectedVisitors);
    }
}

var s = Season.SUMMER;
System.out.println(Season.SUMMER); // SUMMER
//can be compared using == because they are like static final constants
System.out.println(s ==Season.SUMMER); // true
for(var season:Season.values()){
    /*
      WINTER 0
      SPRING 1
      SUMMER 2
      FALL 3      
     */
        System.out.println(season.name() +" "+season.ordinal());
        }
Season s = Season.valueOf("SUMMER"); // SUMMER
Season t = Season.valueOf("summer"); // IllegalArgumentException
```

```java
public interface Weather { int getAverageTemperature(); }
public enum Season implements Weather{
 WINTER {
 public String getHours() { return "10am-3pm"; }
 },
 SUMMER {
 public String getHours() { return "9am-7pm"; }
 },
  FALL,SPRING {
    public String getHours() { return "9am-5pm"; }
  };
  public abstract String getHours(); //if you want all members to implement this method
  public int getAverageTemperature() {return 5;};
}
```

## Sealing Classes

* A sealed class is a class that restricts which other classes may directly extend it.
* sealed class needs to be declared (and compiled) in the same package as its direct subclasses.
* The subclasses must each extend the sealed class.
* Every class that **_directly_** extends a `sealed` class must specify exactly one of the following three modifiers: `final`, `sealed`, or `non-sealed`.
* `permits` clause is optional and can be omitted if the classes both at the same file or a subclass is inner-class.

|       Location of direct subclasses       |      `permits` clause       |
| :---------------------------------------: | :-------------------------: |
| In a different file from the sealed class |          Required           |
|   In the same file as the sealed class    | Permitted, but not required |
|     Nested inside of the sealed class     | Permitted, but not required |

```java
// Snake.java
public sealed class Snake {}
final class Cobra extends Snake {}
// OR
// Snake.java
public sealed class Snake {
final class Cobra extends Snake {}
}
```
 
```java
public sealed class Bear permits Kodiak, Panda {}
public final class Kodiak extends Bear {}
public non-sealed class Panda extends Bear {}//Can be extended by by unspecified classes
```

```java
public sealed class Snake permits Cobra { //DOES NOT COMPILE
final class Cobra extends Snake {}
}
public sealed class Snake permits Snake.Cobra {// COMPILEs
final class Cobra extends Snake {}
}
```

## Sealing Interfaces

* interfaces that extend a sealed interface can only be marked `sealed` or `non-sealed`. They cannot be marked final.
* One distinct feature of a sealed interface is that the permits list can apply to a class that implements the interface or an interface that extends the interface.
* Classes that implements `sealed` interface should either be `final`, `sealed` or `non-sealed`
* Sealed Class Rules:
  * Sealed classes are declared with the sealed and permits modifiers.
  * Sealed classes must be declared in the same package or named module as their direct subclasses.
  * Direct subclasses of sealed classes must be marked final, sealed, or non- sealed.
  * The permits clause is optional if the sealed class and its direct subclasses are declared within the same file or the subclasses are nested within the sealed class.
  * Interfaces can be sealed to limit the classes that implement them or the interfaces that extend them.

## Encapsulating Data with Records

* the compiler inserts useful implementations of the Object methods `equals()`, `hashCode()`, and `toString()`.
* Members Automatically Added to Records:
  * **_Constructor_**: A constructor with the parameters in the same order as the record declaration.
  * _**Accessor method**_: One accessor for each field
  * `equals()`: A method to compare two elements that returns true if each field is equal in terms of `equals()`.
  * `hashCode()`: A consistent `hashCode()` method using all of the fields
  * `toString()`: A `toString()` implementation that prints each field of the record in a convenient, easy-to-read format.
* records don’t have setters. Every field is inherently final and cannot be modified after it has been written in the constructor.
* Just as interfaces are implicitly `abstract`, records are also implicitly `final`. The `final` modifier is optional but assumed.
* Like enums, that means you can’t extend or inherit a record.
* Also like enums, a record can implement a regular or sealed interface, provided it implements all of the abstract methods.

```java
public record Crane(int numberEggs, String name) {
//Normal Constructor
public Crane(int numberEggs, String name) {
if (numberEggs < 0) throw new IllegalArgumentException();
this.numberEggs = numberEggs;
this.name = name;
 }
//Compact Constructor
public Crane {//No parentheses or constructor parameters
if (numberEggs < 0) throw new IllegalArgumentException();
name = name.toUpperCase();//Refers to input parameters (not instance members)
 }
//Overloaded Constructor
public Crane(String firstName, String lastName) {
this(0, firstName + " " + lastName);
  }
}
```

* Java calls the full constructor after the compact constructor but with the modified constructor parameters.
* compact constructors can modify the constructor parameters, they cannot modify the fields of the record.

```java
public record Crane(int numberEggs, String name) {
public Crane {
this.numberEggs = 10; // DOES NOT COMPILE
    }
}
```

* You can also create overloaded constructors that take a completely different list of parameters.
* The first line of an overloaded constructor must be an explicit call to another constructor via `this()`.
* Also, unlike compact constructors, you can only transform the data on the first line. After the first line, all of the fields will already be assigned, and the object is immutable.
* While you can add methods, static fields, and other data types, you cannot add instance fields outside the record declaration, even if they are private. Doing so  the purpose of using a record and could break immutability!
* Records also do not support instance initializers. All initialization for the fields of a record must happen in a constructor.

## Creating Nested Classes

* A nested class can come in one of four flavors:
  * **_Inner class_**: A non-static type defined at the member level of a class
  * **_Static nested_** class: A `static` type defined at the member level of a class
  * **_Local class_**: A class defined within a method body
  * _**Anonymous class**_: A special case of a local class that does not have a name
* local variable references are allowed if they are `final` or _**effectively final**_.

```java
public void processData() {
 final int length = 5;
 int width = 10;
 int height = 2;
 class VolumeCalculator {
 public int multiply() {
 return length * width * height; // DOES NOT COMPILE
 }
 }
 width = 2;
}
```

* Why Can Local Classes Only Access final or Effectively Final Variables?

> The compiler generates a separate .class file for each inner class. A separate class has no way to refer to a local variable. However, if the local variable is final or effectively final, Java can handle it by passing a copy of the value or reference variable to the constructor of the local class. If it weren’t final or effectively final, these tricks wouldn’t work because the value could change after the copy was made.

* Anonymous classes must extend an existing class or implement an existing interface.
  
```java
1: public class ZooGiftShop {
2: abstract class SaleTodayOnly {
3: abstract int dollarsOff();
4: }
5: public int admission(int basePrice) {
6: SaleTodayOnly sale = new SaleTodayOnly() {
7: int dollarsOff() { return 3; }
8: }; // Don't forget the semicolon!
9: return basePrice - sale.dollarsOff();
10: } }
```

```java
1: public class ZooGiftShop {
2: interface SaleTodayOnly {
3: int dollarsOff();
4: }
5: public int admission(int basePrice) {
6: SaleTodayOnly sale = new SaleTodayOnly() {
7: public int dollarsOff() { return 3; }
8: };
9: return basePrice - sale.dollarsOff();
10: } }
```

* But what if we want to both implement an interface and extend a class? You can’t do so with an anonymous class unless the class to extend is `java.lang.Object`.

```java
public class Gorilla {
 interface Climb {}
 Climb climbing = new Climb() {};//n anonymous class implementing the interface
}
```

### Modifiers in nested classes

| Permitted modifiers | Inner class | static nested class | Local class | Anonymous class |
| :-----------------: | :---------: | :-----------------: | :---------: | :-------------: |
|  Access modifiers   |     All     |         All         |    None     |      None       |
|      abstract       |     Yes     |         Yes         |     Yes     |       No        |
|        final        |     Yes     |         Yes         |     Yes     |       No        |

### Nested class access rules

|                                                           | Inner class | static nested class |               Local class               |                   Anonymous class                    |
| :-------------------------------------------------------: | :---------: | :-----------------: | :-------------------------------------: | :--------------------------------------------------: |
| Can extend a class or implement any number of interfaces? |     Yes     |         Yes         |                   Yes                   | No—must have exactly one superclass or one interface |
|      Can access instance members of enclosing class?      |     Yes     |         No          | Yes (if declared in an instance method) |       Yes (if declared in an instance method)        |
|      Can access local variables of enclosing method?      |     N/A     |         N/A         |   Yes (if final or effectively final)   |         Yes (if final or effectively final)          |

## Understanding Polymorphism

* Java object may be accessed using:
  * A reference with the same type as the object.
  * A reference that is a superclass of the object.
  * A reference that defines an interface the object implements or inherits.
* We can summarize the Polymorphism principle with the following two rules:
  * The type of the object determines which properties exist within the object in memory.
  * The type of the reference to the object determines which methods and variables are accessible to the Java program.

## Casting Objects

* We summarize these concepts into a set of rules for you to memorize:
  * Casting a reference from a subtype to a supertype doesn’t require an explicit cast.
  * Casting a reference from a supertype to a subtype requires an explicit cast
  * At runtime, an invalid cast of a reference to an incompatible type results in a `ClassCastException` being thrown.
  * The compiler disallows casts to unrelated types.
* we created a single instance of a subclass object and accessed it via superclass and interface references. Once we changed the reference type, though, we lost access to more specific members defined in the subclass that still exist within the object. We can reclaim those references by casting the object back to the specific subclass it came from:

```java
Lemur lemur = new Lemur();
Primate primate = lemur; // Implicit Cast to supertype
Lemur lemur2 = (Lemur)primate; // Explicit Cast to subtype
Lemur lemur3 = primate; // DOES NOT COMPILE (missing cast)
```

## Casting Interfaces

* While the compiler can enforce rules about casting to unrelated types for classes, it cannot always do the same for interfaces
* The `instanceof` operator can be used to check whether an object belongs to a particular class or interface and to prevent a `ClassCastException` at runtime.
* 
```java
interface Canine {}
interface Dog {}
class Wolf implements Canine {}
 public class BadCasts {
 public static void main(String[] args) {
 Wolf wolfy = new Wolf();
 Dog badWolf = (Dog)wolfy;//ClassCastException at runtime.
 } }
```
```java
class Penguin {
 public static int getHeight() { return 3; }
 public void printInfo() {
 System.out.println(this.getHeight());
 }
}
public class CrestedPenguin extends Penguin {
 public static int getHeight() { return 8; }
 public static void main(String... fish) {
 new CrestedPenguin().printInfo();//3
 }
}
```
```java
class Marsupial {
 protected int age = 2;
 public static boolean isBiped() {
 return false;
 } }
public class Kangaroo extends Marsupial {
 protected int age = 6;
 public static boolean isBiped() {
 return true;
 }
 public static void main(String[] args) {
 Kangaroo joey = new Kangaroo();
 Marsupial moey = joey;
 System.out.println(joey.isBiped());//true
 System.out.println(moey.isBiped());//false
 System.out.println(joey.age);//6
 System.out.println(moey.age);//2
 } }
```