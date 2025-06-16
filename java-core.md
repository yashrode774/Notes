# SOLID Principles

- **Single Responsibility**: Every method/class (Entity) should have single responsibility.
- **Open/Closed Principle**: Entities should be open for extension/closed for modification.
- **Liskov Substitution Principle**: Object of the parent class should be replaceable by object of the child class.
- **Interface Segregation**: Prefer many small interfaces over several bigger ones.
- **Dependency Inversion**: High-level modules should depend on interfaces, not concrete classes.

# ACID Properties of Database

- **Atomicity**: A transaction (set of instructions) should either be completed or failed (rolled back).
- **Consistency**: Database should go from one consistent state to another.
- **Isolation**: Each transaction should be isolated from others.
- **Durability**: If the transaction is committed, its changes are permanently stored even if the system goes down.

# Java Basics

## JDK, JRE, JVM

- **JDK**: Tool necessary to compile, document, and package Java programs. (JRE + Development tools)
- **JRE**: Runtime environment in which Java bytecode can be executed.
- **JVM**: Abstract machine that provides a runtime environment to execute Java bytecode.

## Singleton Class

A class with only one instance in a JVM.

```java
class SingleTon {
    private static SingleTon obj;
    private SingleTon() {}
    public static SingleTon getInstance() {
        if (obj == null) {
            obj = new SingleTon();
        }
        return obj;
    }
}
```

## JIT Compiler

- **JIT (Just-In-Time) Compiler** improves performance by converting bytecode into native machine code at runtime.
- It is enabled by default and activates when a Java method is called.

## Java Compiler (javac)

- Converts `.java` source code into `.class` bytecode files.
- If multiple classes exist in a file, each gets its own `.class` file.

## Platform Independence

- Java is platform-independent because bytecode can be executed on any OS via the JVM.
- Output is the same regardless of the OS.

# Data Types in Java

- Java is a **strongly typed** language.
- Implicit conversion between incompatible types is not allowed.

### Primitive Types in Java
- `byte`, `short`, `int`, `long`, `char`, `float`, `double`, `boolean`

### Primitive Types in JVM
- Same as Java, except `boolean` is stored using `byte` or `int`.

### Data Type Sizes

- Size is not fixed but range is defined.
- JVM uses **word size** for internal data management.
- Word size is typically large enough to hold int, char, float, etc.
- `long` and `double` require **two words**.

### Reference Types

- Hold **reference values**, not actual objects.
- Includes:
    - Class types
    - Interface types
    - Array types
    - `null` (when it doesn't reference any object)

# Objects in Java

- Object references are stored on the **stack**.
- Objects created with `new` are stored on the **heap**.
- Data members are implicitly initialized with default values.

# Classes in Java

- A class is a **blueprint** to create objects.
- It encapsulates data and behavior.
- **Packages** group related classes.
- A **default constructor** is automatically provided if none is defined.

### Variable Types

- **Static variables** (class variables): stored in method area.
- **Instance variables**: tied to object instances.

## 4. Boxing and Unboxing (Auto-Boxing and Auto-Unboxing)

```java
// Manual Boxing
Integer iref = Integer.valueOf(33); // Boxing
int yrs = iref.intValue();      // Unboxing

// Auto Boxing and Unboxing
Integer boxed = 33;               // Auto Boxing
int unboxed = boxed;      // Auto Unboxing
```

---


## 5. Taking Input in Java

### Using BufferedReader

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
String str = br.readLine();
```

- `BufferedReader br` → Returns a String.
- `InputStreamReader` → Converts byte stream to character stream.
- `System.in` → Standard input from keyboard (byte stream).

### Using Scanner

```java
Scanner sc = new Scanner(System.in);
int yrs = sc.nextInt();
```

---


## 6. Arithmetic in Java

### Example

```java
byte b1 = 1, b2 = 2, b3;
b3 = b1 + b2; // Error: result is int, not byte
```

- Most arithmetic operations result in `int` or `long` by default.
- This works as **upcasting** (small → large type).

---

### Type Conversion and Casting

- Java automatically converts (upcasts) to a **larger type**:
  ```
  byte → short → char → int → long → float → double
  ```

- Java **does not** automatically downcast (larger → smaller).
- Explicit casting is required, which can lead to:
    - **Data loss**
    - **Unpredictable results**

```java
int x = 130;
byte b = (byte) x; // Might lead to overflow
```

---

### Bitwise Operator

- `>>>` → **Unsigned right shift operator**

---

### `instanceof` Operator

- Used while **downcasting**
- Returns `true` if an object is an instance of a specific class.

```java
if (obj instanceof Dog) {
    Dog d = (Dog) obj;
}
```

- An instance of a **subclass** is also an instance of its **superclass**
- Don't check with the reference variable's declared type; check the **actual object** it points to.

## 7.labeled break and labeled continue.
### break label
Exits the labeled loop/block entirely.
```
outerLoop: for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        if (i * j == 4) {
            break outerLoop;
        }
        System.out.println(i + " " + j);
    }
}
```
Once i * j == 4, it breaks out of the outer loop, not just the inner one.
### labeled continue
Skips to the next iteration of the labeled loop.

## 8. Modifiers

### Class Modifiers
- **Public**: Any one can declare ref and can use public methods and members of that class. By default it is accessible only within its own package.
- **abstract**: Abstract class is an Incomplete class since it contains methods which may not have body and must be implemented by a sub-class. No instance can be created.
- **final**: Final classes cannot be overridden by subclass.
- **strictfp**: All floating point arithmetic defined with the class evaluated strictly.

> **Note**: Program file may have several classes but only one among can be defined as public. Class name must be same as filename.  
> So a `.java` file can have only one public class (Driving class).

---

### Field Modifiers

#### Storage and Behavior Modifiers
- **static**: Does not contribute to the size of the object. One copy created for every class.
- **transient**: Field which we don't want to serialize or de-serialize.
    - Serialization = converting object to byte or bits
    - De-serialization = reverse
- **volatile**: Used in synchronization. Ensures field is read from main memory, not thread-local cache.
- **final**: Any attempt made to change final variable gives compile-time error.
  > Cannot be both `volatile` and `final`.

#### Access Modifiers
- **default** (no modifier): Accessible within the same package
- **private**
- **protected**
- **public**

---

### Method Modifiers
- **static**: Can access only static fields and methods.
- **final**: Cannot be overridden in subclass (can be overloaded).
- **native**: Method implemented in native code (like C/C++).
  ```java
  public native void xyz();
  ```
    - Uses JNI (Java Native Interface)
- **strictfp**: Forces floating point precision consistency
- **synchronized**: Lock acquired, thread-safe. Time slicing is ignored. Executes fully before switching to another thread.
- **abstract**: No method body; must be implemented by subclass.

---

## 9. Arrays of Objects

Construction of array of objects in Java is different.  
There is no real "array of objects", but an array of references.

```java
Point[] arr;
arr = new Point[3];  // Array object containing 3 references to Point class objects.
```

---

## 10. Packages and Imports

- **Package** provides namespace to class.  
  If no package is declared, class falls into default package.
- **`package` keyword**: Defines where the class is located.
- **`import` keyword**: Used to bring in other classes or packages.
    - Can import individual class or entire package using `*`
    - Static import:
      ```java
      import static <packageName.ClassName.staticMember>;
      ```
        - Good practice to import individual static members to avoid namespace issues.

> **Note**: To run a Java program, you must define a `main` method in a **public** class.  
> The main method is static, so it runs without creating an object.

---

## 11. Java ClassLoader

Loads class into memory:

```java
className.class.getClassLoader().loadClass("Employee");
```

- **Bootstrap ClassLoader**: Loads core Java classes (`JAVA_HOME/lib`)
- **Extension ClassLoader**: Loads classes from `JAVA_HOME/lib/ext`
- **System ClassLoader**: Loads application classes from project source

---

## 12. Garbage Collection

- Reference scope depends on object instantiation.
- GC recognizes unreachable objects and reclaims memory.
- Runs in idle cycles of JVM.
- Follows **mark and sweep** algorithm.
- Memory areas: **Young generation** and **Old generation**

---

## 13. Java is Pass by Value

All assignment operations in Java are **pass-by-value**.

Example:
```java
Dog myDog = new Dog("Rover");
foo(myDog);
```

You are passing the **value** of the reference (address) to `foo()`.

Suppose Dog resides at memory address `42`.

```java
public void foo(Dog someDog) {
    someDog.setName("Max");     // AAA
    someDog = new Dog("Fifi");  // BBB
    someDog.setName("Rowlf");   // CCC
}
```

### Step-by-step:

- Line **AAA**: Modifies name of Dog at address 42 → becomes "Max"
- Line **BBB**: Creates new Dog at 74 and assigns `someDog` to it
- Line **CCC**: Changes name of Dog at 74 to "Rowlf"
- After method returns:
    - `myDog` still points to address 42
    - Its name is "Max", not "Rowlf"

### Conclusion:
Java passes **copies of values** (even if they're object references).  
So the reference value passed into the method is **copied**, not the actual object.

**Java is always pass-by-value.**

--------------------------------------------------------------------------------------

## 14. 2D Array Creation in Java

```java
int[][] marks;
marks = new int[3][];        // Array object containing 3 array-type references.
marks[0] = new int[3];       // First reference is assigned a 1D array of size 3.
```

- Array is a collection of variables of the same type.
- Items can be primitive or object-type references.
- Length is fixed when created.

### Declaring Arrays of Primitives

```java
int[] marks = new int[4];        // Initializes to 0
int[] marks = {1, 2, 3, 4, 5};
int pow[4];                      // ❌ Invalid syntax
```

### Declaring Arrays of Reference Type

```java
String[] categories = new String[3];
for (int i = 0; i < categories.length; i++) {
    categories[i] = new String(); // Optional for String; needed for custom class types.
}
```

---

## 15. Strings in Java

- `String` is an immutable class—values cannot be changed once created.
- Use `StringBuilder` or `StringBuffer` for mutable strings.
- String methods appear to modify the string but return new objects.

### Creating Strings

```java
String s = "teststring";                     // String literal, stored in string pool
String s = new String("teststring");         // String object, created in heap
```

### Common String Methods

- `+` or `concat()` — Concatenation
- `length()` — Returns string length
- `charAt(index)` — Returns char at index
- `substring(begin, end)` — Substring
- `toUpperCase()`, `toLowerCase()`, `trim()`
- `indexOf(String)`, `lastIndexOf(String)`
- `equals()`, `equalsIgnoreCase()`
- `==` compares **references**, not values

### String Literal vs Object

```java
String s1 = "Hello";
String s2 = "Hello";       // Points to same object as s1
String s3 = s1;

String s4 = new String("Hello");  // Different object
String s5 = new String("Hello");
```

---

## 16. StringBuilder and StringBuffer

- `StringBuilder` is mutable and not thread-safe.
- `StringBuffer` is thread-safe (synchronized).

### StringTokenizer

Used to split strings into tokens.

```java
StringTokenizer st = new StringTokenizer("my name is khan", " ");
while (st.hasMoreTokens()) {
    System.out.println(st.nextToken());
}
```

**Output:**
```
my
name
is
khan
```

---

## 17. Formatting Classes

Classes for formatting:
- `Date`, `Time`
- `ChoiceFormat`, `NumberFormat`
- `Locale` and `ResourceBundle`
- `Regex` (regular expressions)

---

## 18. Inheritance

- Enables reuse and extension of existing classes.
- `extends` keyword used to inherit.

### Key Concepts

- Java allows only **single inheritance**.
- All classes inherit from `Object`.
- Constructors are called **top-down** (superclass to subclass).
- `super()` is used to call parent constructors or overridden methods.
- **Private** and **final** methods cannot be overridden.

### Overriding

- Subclass redefines method of superclass with same signature.
- Use `super.methodName()` to call overridden method.
- Fields cannot be overridden—**they're hidden**.

```java
class Parent { public int x = 10; }
class Child extends Parent { public int x = 20; }
```

### Overloading

- Same method name, different parameters.
- Resolved at **compile-time**.

### Inheriting Static Methods

- Static methods are **hidden**, not overridden.

---

## 19. Upcasting and Downcasting

- **Upcasting**: Subclass object referred by superclass type.
- **Downcasting**: Superclass reference cast to subclass type to access subclass-specific methods.

```java
InventoryItem item = new Vcr();        // Upcasting
((Vcr) item).play();                   // Downcasting
```

---

## 20. Final Keyword

- `final` method: Cannot be overridden.
- `final` class: Cannot be extended.
- Used for **security**, **efficiency**, and **immutability**.

---

## 21. Polymorphism

- **Overloading** and **Overriding** are forms of polymorphism.
- Java determines method at **runtime** for overridden methods (dynamic binding).
- Member variables use **static binding**.

---

## 22. Abstract Class

- Cannot be instantiated.
- May contain both abstract and concrete methods.
- Can have constructors.
- Abstract methods must be implemented by subclass.

```java
abstract class Animal {
    abstract void sound();
}
```

---

## 23. Interfaces

- All methods are `public` and `abstract` (by default).
- All variables are `public static final`.
- Java 8+: `default` and `static` methods allowed.
- No constructors or instance fields.

```java
interface Color {
    int RED = 1, WHITE = 2, BLUE = 3;
}
```

### Marker Interfaces

- No members.
- Examples: `Serializable`, `Cloneable`

### Serialization

```java
ObjectOutputStream oos = new ObjectOutputStream(...);
oos.writeObject(obj);   // Serialize
oos.readObject();       // Deserialize
```

### Cloning

```java
public Object clone() throws CloneNotSupportedException {
    return super.clone();
}
```

- Avoid shallow copy for reference-type fields.

---

## Comparable vs Comparator

### Comparable (Single Attribute)

```java
class Movie implements Comparable<Movie> {
    public int compareTo(Movie other) { ... }
}
```

### Comparator (Multiple Criteria)

```java
class RatingCompare implements Comparator<Movie> {
    public int compare(Movie m1, Movie m2) { ... }
}
```

---

## Functional Interfaces (Java 8+)

- Only one abstract method (SAM - Single Abstract Method)
- Can be used with lambda expressions

```java
@FunctionalInterface
interface MyFunc {
    void doWork();
}
```

- Can have multiple `default` or `static` methods
-----------

# 24.Exception Handling(throws and throw):

## UnChecked exception (Runtime time exceptions)

## Checked exception (compile time):
- all the subclass of throwable, other than runtime and error are checked exception.
- Runtime Exceptions are never checked.
- A checked exception is a type of exception that must be either caught or declared in the method in which it is thrown. For example, the java.io.IOException is a checked exception. To understand what a checked.
- A checked exception caught in throws don't need to implement catch for it.


- throws keyword is in the function signature.
- throws keyword handel checked exceptions.
- It is used when the function has some statements that can lead to some exceptions.
- throw : It can throw only one exception at a time.
------

// throwing only an IOException  // throwing instance of IOException
throw new IOException();
---
- throws keyword can be used to declare multiple exceptions, separated by comma.
 Whichever exception occurs, if matched with the declared ones, is thrown automatically then
---
### throwing multiple exceptions
```

void Demo() throws excep1,excep2 (This are Checked exceptions)
{
// Statements where exceptions might occur.
}
```

-Syntax wise throws keyword is followed by exception class names.-

void main() throws IOException   //checked exception must either be caught
{
}
---- 

--IMPETUS---
Declaring Exceptions
A Java method must declare in its signature the types of checked exception it may "throw" from its
body, via the keyword "throws".
For example, suppose that methodD() is defined as follows:

public void methodD() throws XxxException, YyyException {
 // method body throw XxxException and YyyException
}

The method's signature indicates that running methodD() may encounter two checked exceptions:
XxxException and YyyException. In other words, some of the abnormal conditions inside
methodD() may trigger XxxException or YyyException.
Exceptions belonging to Error, RuntimeException and their subclasses need not be declared.
These exceptions are called unchecked exceptions because they are not checked by the compiler.

-----

## finally:
The finally-block is meant for cleanup code such as closing the file, database connection regardless of whether the try block
succeeds. The finally block is always executed (unless the catch-block prematurely terminated the current method)

### Try with resources:
- If we want to do some close-up operations, which are generally done in finally block, java provides a better way of doing that:
- Java will automatically close the br object that has been created.
```
try (BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
    int n = Integer.parseInt(br.readLine());
    System.out.println(n);
}
```

## difference between constant and final:
Java's final works only on primitive types and references, never on object instances themselves where the const keyword works on anything.

# 25. Threading:
- java supports single thread and multi-threading operations.
- Java has a built-in support for concurrent programming by running multiple threads concurrently with a single program.
- Ms word is running 1 process contain multiple subprocess like spelling checking,typing.
- Thread is the smallest unit of a process.
- when you create your java application you only use one thread that is main thread by default.

## uses:
- If you want to multiplty {1,2,3,4,   .... ,5000} by  *2 it will run on single thread consider it takes time of 8sec.
- It 4 threads are used the same task can be divided and completed in 2 Sec. by executing task on 4 threads parallely.
- main thread will create 4 different threads t1,t2,t3,t4 and assign a task to the threads and they can run in background a main thread can perform its task.
- considering sending req on Amazon app to search shoes it will assign new thread task to find all the shoes and main thread will work.
- Considering web Application example if five different users request same page 5 different threads are created and each is assign to user to perform its task.

## Creating Threads:
Thread t1= new Thread(); // thread doesn't know what you want.
you have to create a class.
```
//Creating Thread Way 1:
class MyThread extends Thread  // Every class implements run method
{
  int[]={6,5,1,7,8};
  public void run()
  {
	for(int i=0; i<5;i++)
	{
		values[i]=values[i]*2;
	}

  }
}
Thread t1= new MyThread();
t1.start()// new thread created  start  will automatically call run().
---------
methods :
sleep(takes milliseconds): you want to stop for 2 Sec or 3 sec you can call sleep
wait:
notify:

------Cannot use multiple Inheritance-------
//Creating Thread Way 2:
class MyThread extends Aclass  implements Runnable // cannot extend thread so implement Runnable.
{
  int[]={6,5,1,7,8};
  public void run()
  {
	for(int i=0; i<5;i++)
	{
		values[i]=values[i]*2;
	}

  }
}
```
- If one method is busy on one core you should execute other method on other core.
- if both the thread reaches to the scheduler simultaneously , multiple criteria on which scheduler will select one thread
i.e ThreadPriority if priority is same it will go for random order.

**Demon Thread**: (Thread with Low Priority)
Daemon thread is a low priority thread (in context of JVM) that runs in background to perform tasks such as garbage collection (gc) etc.,
they do not prevent the JVM from exiting (even if the daemon thread itself is running) when all the user threads (non-daemon threads) finish their execution.
JVM terminates itself when all user threads (non-daemon threads) finish their execution, JVM does not care whether Daemon thread is running or not, if JVM
finds running daemon thread (upon completion of user threads), it terminates the thread and after that shutdown itself.

---Hiran  
(concurrent execution of tasks).

## Life Cycle of Thread

```
new -> runnable -> running -> terminated (2-3) waiting.
1. new : creating a thread object.
2. runnable : start the thread
3. running : executing running method
4. waiting : sleep(), waiting, blocked
5. terminated : returns from run method. e.g download the image, wait : no network
```

1. If two threads with equal priorities are competing, they are time-sliced in round-robing fashion (OS dependent).

## Some thread methods:

```
1. Thread.currentThread();          // return the reference of current thread.
2. ref.getName();
3. ref.setName("pass name here");
4. ref.sleep(time in milliseconds); // might throw an InterruptedException. Suspends thread for specified time
5. isAlive();                       // to check whether thread is running or not
6. ref.join();                      // makes main thread wait until ref execution is completed
7. ref.setPriority();              // level of priority (MIN_PRIORITY || NORM_PRIORITY)
8. ref.getPriority();
```

## Controlling the main thread

```java
class CurrentThreadDemo {
    public static void main(String args[]) {
        Thread t = Thread.currentThread();
        System.out.println("Current thread: " + t);

        // change the name of the thread
        t.setName("My Thread");
        System.out.println("After name change: " + t);

        try {
            for(int n = 5; n > 0; n--) {
                System.out.print(n + "\t");
                Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            System.out.println("Main thread interrupted");
        }
    }
}
```

### Output:
```
Current thread: Thread[main,5,main]
After name change: Thread[My Thread,5,main]
5   4   3   2   1
```

---

## Thread and Process

- Thread is the segment of a process, meaning a process can have multiple threads, and these multiple threads are contained within a process.
- A thread has 3 states: running, ready, and blocked.
- Thread takes less time to terminate as compared to a process, and unlike processes, threads do not isolate.

### Process:
Process means the thread is in Execution.

### Difference Between Thread and Process

| Process | Thread |
|--------|--------|
| Process is isolated. | Threads share memory. |
| Process is called heavy weight thread. | Thread is called a light weight process. |
| Process means any program is in execution. | Thread means segment of a process. |
| Process is less efficient in terms of communication. | Thread is more efficient in terms of communication. |
| Process consumes more resources. | Thread consumes less resources. |

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Questions asked:
Technical :
1.Difference between pass by value and pass by reference.
2.Difference between Thread and process.
3.What is an immutable object in Java?
4.What is the difference between array, set, and an ArrayList in java?
5.What happens when an exception/error occurs in our program?
6.Can you have a return statement in a try or catch block?
7.Difference between LinkedList and ArrayList?
8.If You were going to write a program where you will use LinkedList or ArrayList? If there are many manipulations which one should you use?
9.In data structure have you come across Typecasting? Two types comparable a= 20 and int b=30 how do you add these two?
I didn’t know about comparable so he told let’s change it to object now how will you add it, upcast or downcast?
10.Given 10 5 5 6 how to sort using bubble sort, how will you handle duplicates?
11.Given a string find the first non-repeated character.
12.An array consists of elements 1 to 100 all are supposed to be unique but one of them is repeated find the repeated element.
13.Difference between a linked-hashmap and a hashmap in java.


Technical round questions :
i) AVL tree (code)
ii) difference between structure and array
iii) difference between structure and object
iv) inheritance
v) some basic codes to write on patterns, anagram etc
vi) dynamic loading
vii) threads and processes
viii) reverse a linked list
ix) critical section
x) implementation of some real time systems

DBMS:
1. Different normal forms
2. Database ACID properties.
3. OOP concepts like encapsulation, abstraction and their real-life examples.
4. What happens when you enter a URL in the browser? (DNS related)

HR: Type
1.Why do you want to join a finance company.
2.Are you ever in a position where you work in a team and there is a person who doesn’t do much work? What was your role in a team and what was your impact?
3.You are already placed in the company ‘X’ then why you want to join Credit Suisse?
4.How were the coding challenge and technical interview?
5.Are you okay to relocate to Bangalore, Pune or Mumbai? Company will decide the location.
6.Tell me about your family.
7.Tell me about a situation when you were a leader or worked in a project together?
8.Let’s say you are working in a team of 5 and a member of your team is a childhood friend of yours. He did a mistake which will lead to a huge loss to the company which might end up your friend losing your job. You are the 1st person to find out the mistake what will you do?
9.If your friend tells you to do a transaction on your behalf will you do?
10.You are already placed in the company then why you want to join Credit Suisse?
