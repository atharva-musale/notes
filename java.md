# Java

## JDK, JVM and JRE

### JVM (Java Virtual Machine)
- An abstract machine that provides the runtime environment to execute Java bytecode
- Platform-dependent (different implementations for Windows, Linux, macOS)
- Key responsibilities:
  - Loading bytecode
  - Verifying bytecode
  - Executing bytecode
  - Providing runtime environment
- Components:
  - **Class Loader**: Loads class files
  - **Memory Area**: Heap, Stack, Method Area, PC Register, Native Method Stack
  - **Execution Engine**: Interpreter, JIT Compiler, Garbage Collector

### JRE (Java Runtime Environment)
- Provides the environment to run Java applications
- Contains:
  - JVM
  - Core libraries (Java Class Library)
  - Supporting files
- Cannot be used to develop Java applications (no compiler)
- Minimum requirement to run Java programs

### JDK (Java Development Kit)
- Complete development kit for Java
- Contains:
  - JRE (which includes JVM)
  - Development tools (javac, javadoc, jar, etc.)
  - Debugging tools
  - JavaFX (in older versions)
- Required for developing and compiling Java applications

**Relationship**: JDK ⊃ JRE ⊃ JVM

## Strings

### String Immutability
- Strings in Java are **immutable** - once created, cannot be changed
- Benefits:
  - **String Pool**: Allows string literals to be shared in memory
  - **Thread Safety**: Multiple threads can access strings without synchronization
  - **Security**: Prevents malicious code from modifying string values
  - **Hashcode Caching**: Hashcode is cached, improving performance for HashMaps/HashSets

### String Pool (String Intern Pool)
- Special memory region in the **Heap** (before Java 7, it was in PermGen)
- Stores literal string values
- When you create a string literal, JVM checks the pool first
- If the string exists, reference is returned; otherwise, new string is added

```java
String s1 = "Hello";  // Goes to String Pool
String s2 = "Hello";  // References same object from pool
String s3 = new String("Hello");  // Creates new object in heap

s1 == s2  // true (same reference)
s1 == s3  // false (different objects)
s1.equals(s3)  // true (same content)
```

### String vs StringBuilder vs StringBuffer

| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|--------------|
| Mutability | Immutable | Mutable | Mutable |
| Thread Safety | Yes | No | Yes (synchronized) |
| Performance | Slow for concatenation | Fast | Slower than StringBuilder |
| Use Case | Fixed strings | Single-threaded string manipulation | Multi-threaded string manipulation |

### Common String Methods
```java
length()                    // Returns string length
charAt(int index)           // Returns character at index
substring(int begin, int end)  // Extracts substring
concat(String str)          // Concatenates strings
indexOf(String str)         // Returns first occurrence index
replace(char old, char new) // Replaces characters
split(String regex)         // Splits string by delimiter
trim()                      // Removes leading/trailing whitespace
toLowerCase() / toUpperCase()  // Case conversion
equals() / equalsIgnoreCase()  // Content comparison
```

## Class loaders

### What is a Class Loader?
- Part of JVM responsible for loading class files into memory
- Loads classes dynamically at runtime (not all at once)
- Follows **delegation model** and **lazy loading**

### Types of Class Loaders

#### 1. Bootstrap Class Loader
- The parent of all class loaders
- Loads core Java classes from `rt.jar` (or module system in Java 9+)
- Located in `JAVA_HOME/jre/lib`
- Written in native code (C/C++)
- Loads classes like `java.lang.*`, `java.util.*`

#### 2. Extension Class Loader
- Child of Bootstrap Class Loader
- Loads classes from the extensions directory (`JAVA_HOME/jre/lib/ext`)
- Also loads classes from directories specified by `java.ext.dirs` system property
- Implemented by `sun.misc.Launcher$ExtClassLoader`

#### 3. Application/System Class Loader
- Child of Extension Class Loader
- Loads classes from the application classpath
- Loads classes from paths specified by `-classpath` or `CLASSPATH` environment variable
- Implemented by `sun.misc.Launcher$AppClassLoader`

### Class Loader Delegation Model
- When a class needs to be loaded, the request is delegated to parent class loader first
- Flow: Application → Extension → Bootstrap
- If parent cannot find the class, then child attempts to load it
- Prevents loading core classes multiple times
- Ensures core Java classes are loaded by Bootstrap Class Loader

```
Request to load a class
        ↓
Application Class Loader (checks cache)
        ↓ (delegates)
Extension Class Loader (checks cache)
        ↓ (delegates)
Bootstrap Class Loader (checks cache)
        ↓
Loads or throws ClassNotFoundException
```

### Custom Class Loaders
- You can create custom class loaders by extending `ClassLoader`
- Use cases:
  - Loading classes from non-standard sources (network, database)
  - Implementing hot deployment
  - Loading encrypted classes
  - Isolating class versions

```java
public class CustomClassLoader extends ClassLoader {
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        byte[] classData = loadClassData(name);
        return defineClass(name, classData, 0, classData.length);
    }
    
    private byte[] loadClassData(String name) {
        // Load class bytes from custom source
        return null;
    }
}
```

## Static keyword and static block

### Static Variables (Class Variables)
- Variables declared with `static` keyword belong to the class, not instances
- Shared among all instances of the class
- Allocated memory once when class is loaded
- Can be accessed without creating an object

```java
public class Counter {
    static int count = 0;  // Static variable
    int id;                // Instance variable
    
    Counter() {
        count++;           // Shared across all instances
        id = count;        // Unique to each instance
    }
}

Counter c1 = new Counter();  // count = 1
Counter c2 = new Counter();  // count = 2
System.out.println(Counter.count);  // 2 (access without object)
```

### Static Methods
- Methods declared with `static` keyword belong to the class
- Can be called without creating an instance of the class
- Can only access static variables and other static methods directly
- Cannot use `this` or `super` keywords

```java
public class MathUtils {
    static int add(int a, int b) {
        return a + b;
    }
    
    static int square(int n) {
        return n * n;
    }
}

// Call without creating object
int result = MathUtils.add(5, 3);  // 8
```

**Restrictions:**
- Cannot access non-static (instance) variables directly
- Cannot call non-static methods directly
- Cannot use `this` reference (no instance context)

### Static Blocks
- Block of code that executes when the class is loaded into memory
- Executes only once, before any constructor or main method
- Used for static initialization of complex logic
- Multiple static blocks execute in order they appear

```java
public class Database {
    static Connection connection;
    static String url;
    
    // Static block - runs once when class loads
    static {
        System.out.println("Initializing database...");
        url = "jdbc:mysql://localhost:3306/mydb";
        connection = DriverManager.getConnection(url);
    }
    
    // Another static block
    static {
        System.out.println("Database initialized");
    }
}
```

**Execution order:**
1. Static variables initialization
2. Static blocks (in order)
3. Instance variables initialization
4. Instance blocks
5. Constructor

```java
public class Demo {
    static int x = 10;           // 1. Static variable
    int y = 20;                  // 3. Instance variable
    
    static {                     // 2. Static block
        System.out.println("Static block: x = " + x);
    }
    
    {                            // 4. Instance block
        System.out.println("Instance block: y = " + y);
    }
    
    Demo() {                     // 5. Constructor
        System.out.println("Constructor");
    }
    
    public static void main(String[] args) {
        new Demo();
    }
}
// Output:
// Static block: x = 10
// Instance block: y = 20
// Constructor
```

### Static Nested Classes
- A static class defined inside another class
- Can be instantiated without an instance of outer class
- Can only access static members of outer class

```java
public class Outer {
    static int x = 10;
    int y = 20;
    
    static class StaticNested {
        void display() {
            System.out.println(x);  // OK - static member
            // System.out.println(y);  // Error - non-static member
        }
    }
}

// Usage
Outer.StaticNested nested = new Outer.StaticNested();
nested.display();
```

### Common Use Cases

**1. Utility/Helper classes:**
```java
public class StringUtils {
    private StringUtils() {}  // Prevent instantiation
    
    public static boolean isEmpty(String str) {
        return str == null || str.isEmpty();
    }
    
    public static String reverse(String str) {
        return new StringBuilder(str).reverse().toString();
    }
}
```

**2. Constants:**
```java
public class Constants {
    public static final double PI = 3.14159;
    public static final int MAX_SIZE = 100;
    public static final String APP_NAME = "MyApp";
}
```

**3. Singleton pattern:**
```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {}  // Private constructor
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

**4. Factory methods:**
```java
public class Employee {
    private String name;
    private double salary;
    
    private Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
    
    public static Employee createManager(String name) {
        return new Employee(name, 100000);
    }
    
    public static Employee createDeveloper(String name) {
        return new Employee(name, 80000);
    }
}
```

### Key Points to Remember
- Static members are loaded when class is loaded (before any object creation)
- Static members belong to class, not objects
- Static methods can't access instance members directly
- Static blocks execute once during class loading
- Use static for utility methods, constants, and shared data
- `static final` creates constants
- Avoid overusing static - can make testing difficult