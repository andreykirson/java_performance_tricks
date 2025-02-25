# Java Performance Takeaways

## Optimize Garbage Collection

- Use `-verbosegc` to display detailed garbage collection (GC) actions.

## Object Management

### Use Object Pools
- Frequently used objects should be stored as `public static final` constants.

```java
public class IntegerManager {
    public static final Integer ZERO = new Integer(0);
    public static final Integer ONE = new Integer(1);
    public static final Integer TWO = new Integer(2);
    public static final Integer THREE = new Integer(3);
    public static final Integer FOUR = new Integer(4);
    public static final Integer FIVE = new Integer(5);
    public static final Integer SIX = new Integer(6);
    public static final Integer SEVEN = new Integer(7);
    public static final Integer EIGHT = new Integer(8);
    public static final Integer NINE = new Integer(9);
    public static final Integer TEN = new Integer(10);
}
```

### Use Off heap memory to avoid GC overhead

### Use Weak References
- Helps in automatic cleanup of objects when they are no longer referenced.

### Prefer Primitive Types
- Using primitive types instead of objects reduces memory overhead and improves performance.
- Map object properties into primitive arrays for efficiency:

```java
class MyClass {
    int x;
    boolean y;
}

class MyClassCollection {
    int[] xs;
    boolean[] ys;
    
    public int getXForElement(int i) {
        return xs[i];
    }
    
    public boolean getYForElement(int i) {
        return ys[i];
    }
}
```

## Object Flattening
- Flattening objects reduces memory consumption and improves cache locality.
- Instead of:

```java
public class Person {
    private Name name;
    private Address address;
}

class Name {
    private String firstName;
    private String lastName;
    private String[] otherNames;
}

class Address {
    private int houseNumber;
    private String houseName;
    private String streetName;
    private String town;
    private String area;
    private String greaterArea;
    private String country;
    private String postCode;
}
```

- Use a single, flattened class:

```java
public class Person {
    private String firstName;
    private String lastName;
    private String[] otherNames;
    private int houseNumber;
    private String houseName;
    private String streetName;
    private String town;
    private String area;
    private String greaterArea;
    private String country;
    private String postCode;
}
```

## Exception Handling Optimization

### Avoid Using `try-catch` Blocks in Performance-Critical Code

### Reuse Exception Objects
- Reusing exceptions avoids unnecessary object creation.

```java
public static final Exception REUSABLE_EXCEPTION = new Exception();

try {
    throw REUSABLE_EXCEPTION;
} catch (Exception e) {
    // Handle exception
}
```

> **Note:** Reusing exceptions can lead to incorrect stack traces. Use `fillInStackTrace()` if stack trace accuracy is critical, though it incurs performance overhead.

## Code Optimization Techniques

### Avoid Casting
- Casting introduces overhead; prefer primitive data types where possible.

### Optimize Loops
- Avoid method calls in loop termination conditions.

**Bad Example:**
```java
for (int i = 0; i < collection.size(); i++) {
    // Code
}
```

### Prevent False Sharing with Padding
- Align data structures properly to avoid CPU cache conflicts.

### Use Bit Masking for Index Calculations
- If the array size is a power of two, use bitwise operations for fast indexing.

## Multithreading Considerations

- **Multithreading does not always mean high performance** – single-threaded approaches can sometimes be faster (e.g., LMAX Disruptor).
- If an algorithm guarantees that only one thread modifies a resource, mutual exclusion is unnecessary.

### Use Compare-And-Swap (CAS) for Efficiency
- CAS is more efficient than locks as it avoids context switches.
- Java provides CAS operations via `java.util.concurrent.atomic` classes.

> **Ideal Scenario:** A single thread owns all writes to a resource while other threads only read.

### CPU Affinity for Thread Optimization
- Pin threads to specific CPU cores to maximize cache efficiency.

## Conclusion

Optimizing Java performance involves careful management of memory, object creation, exception handling, and multithreading strategies. By leveraging techniques such as object pooling, primitive types, loop optimizations, and CAS operations, developers can achieve significant performance gains in Java applications.

