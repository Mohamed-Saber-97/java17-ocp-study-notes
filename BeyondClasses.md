# Beyond Classes

## Implementing Interfaces

* To add a method body for an interface method we either mark it as `default` or `static`
* an interface cannot be instantiated or marked as `final` for the same reason that abstract classes cannot be marked as
  `final`. In other words, marking an `interface final` implies no class could ever implement it.
* the implicit modifiers for interfaces
    * Interfaces are implicitly `abstract`.
    * Interface variables are implicitly `public`, `static`, and `final`.
    * Interface methods without a body are implicitly `abstract`.
    * Interface methods without the `private` modifier are implicitly `public`
* The **concrete** `class` implementing the `interface` must `override` the `abstract` methods and explicitly declare
  the access modifier as `public`
* Unlike a `class`, which can extend only one `class`, an `interface` can extend multiple `interfaces`

> Extending two interfaces is permitted because interfaces
> are not initialized as part of a class hierarchy. Unlike abstract classes, they do not contain
> constructors and are not part of instance initialization. Interfaces simply define a set of rules
> and methods that a class implementing them must follow

* class can implement an interface, a class cannot extend an interface and interface can extend another interface, an
  interface cannot implement another interface

#### Interface member types

|                         | Membership type | Required modifiers |     Implicit modifiers      | Has value or body |
|:-----------------------:|:---------------:|:------------------:|:---------------------------:|:-----------------:|
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
    * If a `class` inherits two or more `default` methods with the same method signature, then the class must `override`
      the
      method

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
    * A static `method` is not inherited and cannot be accessed in a class implementing the interface without a
      reference to the interface name.
* Java “solved” the multiple inheritance problem of `static` interface methods by not allowing them to be inherited
* Private Interface Method Definition Rules
    * A `private` interface method must be marked with the `private` modifier and include a method body
    * A `private` `static` interface method may be called by any method within the interface definition
    * A `private` interface method may only be called by `default` and other `private` nonstatic methods within the
      interface definition

#### Interface member access

Accessible without an instance of the interface?

|                         | Accessible from default and private methods within the interface? | Accessible from static methods within the interface? | Accessible from methods in classes inheriting the interface? | Accessible without an instance of the interface? |
|:-----------------------:|:-----------------------------------------------------------------:|:----------------------------------------------------:|:------------------------------------------------------------:|:------------------------------------------------:|
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
* **You can't extend an `enum` or compare an `int` and an `enum` value directly anyway since an enum is a type and not a
  primitive int**
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
  public abstract String getHours();
}
```