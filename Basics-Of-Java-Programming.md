# Basics Of Java Programming

## The Java Ecosystem

* Java programs are compiled to bytecode that is interpreted by the JVM.
* The specification of the bytecode is architecture neutral, meaning it is independent of any hardware architecture.
* Java does not have a preprocessor, and it does not allow pointer handling, user-defined operator overloading, or
  multiple class inheritance.
* The JVM can dynamically load class libraries from the local file system as well as from machines on the network, when
  those libraries are needed at runtime.
* The language does not allow direct access to memory. A bytecode verifier determines whether any untrusted code loaded
  in the JVM is safe.
* The sandbox model is used to confine and execute any untrusted code, limiting the damage that such code can cause.
* The JIT monitors the program at runtime to identify performance-critical bytecode (called hotspots) that can be
  optimized. Such code is usually translated to machine code to boost performance.

## Classes

* A class models an abstraction by defining the properties and behaviors of the objects representing the abstraction.
* The contract (interface) defines _**which**_ services are provided, and the implementation (class) defines _**how**_
  these services are provided by the class.
* Clients (i.e., other objects) need only know the contract of an object, and not its implementation, to avail
  themselves of the object’s services.
* constructor is executed when an object is created from the class.

## Objects

* A _**reference value**_ is returned when an object is created. A _**reference value**_ uniquely denotes a particular
  object.
* A _**variable**_ denotes a location in memory where a value can be stored.
* An _**object reference**_ (or simply _**reference**_) is a variable that can store a _**reference value**_.
* Object can only be manipulated by a reference that holds its reference value. Direct access to the reference value is
  not permitted.
* `The toString() method called on a String object returns the String object itself.Point2D p1 = new Point2D(10, 20); // A point with coordinates (10,20)`
    * The `new` operator creates an instance of the Point2D class and returns the _**reference value**_ of this
      instance.
    * The purpose of the constructor call on the right-hand side of the new operator is to initialize the fields of the
      newly created object.
* objects in Java do not have names, but rather are denoted by references.

## Static Members

* A static variable belongs to the class, It will be allocated in the class and initialized in its declaration when the
  class is loaded.
* Static members can also be accessed via object references, although doing so is not encouraged.
* Static members in a class can be accessed both by the class name and via object references, but instance members can
  be accessed only by object references.

### Terminology for Class Members

|         Members         |                                                                                     Terminology                                                                                      |
|:-----------------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| **_Instance members_**  |                             The instance variables and instance methods of an object. They can be accessed or invoked only through an object reference.                              |
| **_Instance variable_** | A field that is allocated when the class is instantiated (i.e., when an object of the class is created). Also called a non-static field or just a field when the context is obvious. |
|  **_Instance method_**  |                                        A method that belongs to an instance of the class. Objects of the same class share its implementation.                                        |
|  **_Static members_**   |                  The static variables and static methods of a class. They can be accessed or invoked either by using the class name or through an object reference.                  |
|  **_Static variable_**  |      A field that is allocated when the class is loaded. It belongs to the class, and not to any specific object of the class. Also called a static field or a class variable.       |
|   **_Static method_**   |                                         A method that belongs to the class, and not to any object of the class. Also called a class method.                                          |

## Inheritance

* A subclass can **_extend_** only one superclass.
* `private` fields in the super class are accessible to subclass object indirectly through the `public` get and set
  methods.

## Aggregation

* An **_association_** defines a static relationship between objects of two classes. One such association, called *
  *_aggregation_** (also known as **_composition_**), expresses how an object uses other objects.
* Java supports aggregation of objects by reference, since objects cannot contain other objects explicitly.

## Sample Java Program

* To create a program in Java, the program must have a class that defines a method named `main`, which is invoked at
  runtime to start the execution of the program. The class with this `main()` method is known as the *
  *_entry point of the program_**.
* The classes in the Java SE Platform API are already compiled, and the JDK tools know where to find them.
* Java source files can be compiled using the Java Language Compiler, javac, which is part of the JDK.
* Each source file name has the extension .java. Each class declaration in a source file is compiled into a separate
  class file, containing its Java bytecode.
* `javac ClassName.java` to compile the source code and generate a bytecode file (.class)
* We can run the program with the `java ClassName.java` without compiling it first (compile on the fly) if all the
  source code are in one file.
* no class files are created. The source code is compiled fully in memory and executed.
* In order to run a single-file source-code program, the following conditions must be met:
    * The single source file must contain _**all**_ source code for the program.
    * the first class declaration in the source file must provide the main() method; that is, it must be the entry point
      of the program.
    * There must not exist class files corresponding to the class declarations in the single source file that are
      accessible by the java command.
    * An invalid class name can be used (name of the file can be different from the public class).
    * allows several public classes in the single source file.

## Program Output

* A Java program can send its output to the terminal window using an object called standard out which can be accessed
  using the `public static final` field out in the `System` class, is an object of the class `java.io.PrintStream`.
* An object is first converted to its text representation by calling its `toString()` method implicitly, if it is not
  already called explicitly on the object.
* The `toString()` method called on a String object returns the String object itself.
* Any error in the format string will result in a runtime exception.

### Format Specifier Examples

|     Parameter value      | Format spec | Example value | String printed |                                                                  Description                                                                  |
|:------------------------:|:-----------:|:-------------:|:--------------:|:---------------------------------------------------------------------------------------------------------------------------------------------:|
|    **Integer value**     |    `%d`     |     `123`     |     "123"      |                                                Occupies as many character positions as needed.                                                |
|    **Integer value**     |    `%6d`    |     `123`     |    "   123"    |           Occupies six character positions and is right-justified. The printed string is padded with leading spaces, if necessary.            |
|    **Integer value**     |   `%06d`    |     `123`     |    "000123"    |            Occupies six character positions and is right-justified. The printed string is padded with leading zeros, if necessary.            |
| **Floating-point value** |    `%f`     |    `4.567`    |   "4.567000"   |                            Occupies as many character positions as needed, but always includes six decimal places.                            |
| **Floating-point value** |   `%.2f`    |    `4.567`    |     "4.57"     |    Occupies as many character positions as needed, but includes only two decimal places. The value is rounded in the output, if necessary.    |
| **Floating-point value** |   `%6.2f`   |    `4.567`    |   "   4.57"    | Occupies six character positions, including the decimal point, and uses two decimal places. The value is rounded in the output, if necessary. |
|      **Any object**      |    `%s`     |     `Hi!`     |     "Hi!"      |                             The text representation of the object occupies as many character positions as needed.                             |
|      **Any object**      |    `%6s`    |     `Hi!`     |    "   Hi!"    |                        The text representation of the object occupies six character positions and is right-justified.                         |
|      **Any object**      |   `%-6s`    |     `Hi!`     |    "Hi!   "    |                         The text representation of the object occupies six character positions and is left-justified.                         |
