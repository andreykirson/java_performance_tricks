Java Performance takeaway

-`verbosegc` - use VM key to verbosely show actions of GC
- use pool of object
  - use public static final for often used objects
    
    `public class IntegerManager
    {
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
    }`
- use weak references
- use primitive type
  - use array to map objects
    `class MyClass
    {
        int x;
        boolean y;
    }`

    `class MyClassCollection
    {
        int[] xs;
        boolean[] ys;
        public int getXForElement(int i) {return xs[i];}
        public boolean getYForElement(int i) {return ys[i];}
        //If possible avoid having to declare element access like the
        //following method:
        //public MyClass getElement(int i) {return new MyClass(xs[i], ys[i]);}
    }`

- use flatten objects

`public class Person {
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
}`

These three classes collapse into one class:

`public class Person {
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
}`  

Avoid use try catch block
Reuse exception object
public static Exception REUSABLE_EXCEPTION = new Exception();
...
//Much faster reusing an existing exception
    `try {
        throw REUSABLE_EXCEPTION;
    } catch (Exception e) {...}`


The sole disadvantage of reusing an exception instance is that the instance does not have the correct
stack trace, i.e., the stack trace held by the exception object is the one generated when the exception
object was created.[2] However, this disadvantage can be important for some situations when the
trace is important, so be careful. This technique can easily lead to maintenance problems.
[2] To get the exception object to hold the stack trace that is current when it is thrown, rather than created, you must use the fillInStackTrace(
) method. Of course, this is what causes the large overhead that you are trying to avoid.

Avoid use Casting

The result is that temporary variables using primitive (nonobject) data types are better for
performance.

Avoid using a method call in the loop termination test. The overhead is significant. 
bad example:
`for(int i = 0; i < collection.size( ); i++) //or collection.length( )`

Use padding to prevent false sharing to improve cache locality

Use bit masked for calculation idx if size of array is powerr of 2


Multithreading does not mean high performance, single thread approach could be much faster for example LMAX Disruptor

If your algorithm can guarantee that any given resource is modified by only one thread, then mutual exclusion is unnecessary.

This CAS approach is significantly more efficient than locks because it does not require a context switch to the kernel for arbitration. However CAS operations are not free of cost. The processor must lock its instruction pipeline to ensure atomicity and employ a memory barrier to make the changes visible to other threads. CAS operations are available in Java by using the java.util.concurrent.Atomic* classes.
The ideal algorithm would be one with only a single thread owning all writes to a single resource with other threads reading the results.

Use CPU Affinity to pin thread to the specific core
