homework 1	  
Java Collections Homework \- Week 1  
Q1: List vs Set  
java// List vs Set

// duplicates: List \-\> allowed      | Set \-\> not allowed  
// ordering:   List \-\> keeps order  | Set \-\> unordered (HashSet)

List\<String\> list \= new ArrayList\<\>();  
list.add("apple");  
list.add("apple"); // size \= 2

Set\<String\> set \= new HashSet\<\>();  
set.add("apple");  
set.add("apple"); // size \= 1

**interview时口述：**

"List and Set are both under the Collection interface. Key differences: List allows duplicates and maintains insertion order, Set does not allow duplicates and is unordered by default. Implementation-wise, I'd use ArrayList for List, HashSet for Set. If I need uniqueness but also preserve order, I'd use LinkedHashSet."

Q2: LinkedList vs ArrayList  
java// LinkedList vs ArrayList

// internal structure: ArrayList \-\> dynamic array    | LinkedList \-\> doubly linked nodes  
// random access:      ArrayList \-\> O(1)             | LinkedList \-\> O(n)  
// insert/delete:      ArrayList \-\> O(n) in middle   | LinkedList \-\> O(1) at head/tail

List\<Integer\> arrayList \= new ArrayList\<\>();  
arrayList.get(0); // O(1) random access

LinkedList\<Integer\> linkedList \= new LinkedList\<\>();  
linkedList.addFirst(1); // O(1)  
linkedList.addLast(2);  // O(1)

// LinkedList also implements Deque \-\> can be used as Queue or Stack  
Queue\<String\> queue \= new LinkedList\<\>();  
queue.offer("task1");  
queue.poll(); // FIFO  
**interview时口述：**

"Both implement the List interface, but internally they're very different. ArrayList is backed by a dynamic array, so random access is O(1), but inserting or deleting in the middle is O(n) because of shifting. LinkedList uses doubly linked nodes, so inserting at head or tail is O(1), but random access is O(n). In practice I default to ArrayList since most workloads are read-heavy. I'd switch to LinkedList when I need it as a Queue or Deque."

Q3: What is Map Interface  
java// Map \-\> key-value pairs, does NOT extend Collection

// HashMap        \-\> unordered, O(1) avg, allows null key  
// LinkedHashMap  \-\> insertion order maintained  
// TreeMap        \-\> sorted by key

Map\<String, Integer\> map \= new HashMap\<\>();  
map.put("Alice", 95);  
map.get("Alice");         // 95  
map.containsKey("Alice"); // true  
map.keySet();             // all keys  
map.values();             // all values  
map.entrySet();           // iterate key-value pairs  
**interview时口述：**

"Map is an interface that stores key-value pairs. It does not extend Collection. The most common implementation is HashMap, which gives O(1) average time for get and put. If I need insertion order I'd use LinkedHashMap, if I need keys sorted I'd use TreeMap."

Q4: How does HashMap work  
java// HashMap internal mechanism:  
// 1\. key.hashCode() \-\> compute bucket index  
// 2\. bucket empty   \-\> store entry  
// 3\. bucket occupied \-\> compare with equals()  
//    equals() true  \-\> overwrite value  
//    equals() false \-\> hash collision \-\> chain (LinkedList or TreeNode)

// default: capacity \= 16, load factor \= 0.75  
// when size \> capacity \* 0.75 \-\> resize (double the array, rehash)

Map\<String, String\> map \= new HashMap\<\>(16, 0.75f);  
map.put("name", "Austin");  
map.put("name", "Austin Cui"); // same key \-\> overwrites  
map.get("name"); // "Austin Cui"  
**口述**：

"Internally HashMap uses an array of buckets. When I call put(), it computes the bucket index using the key's hashCode(). If the bucket is empty it stores the entry directly. If occupied, it uses equals() to check — same key overwrites the value, different key means collision, which is handled by chaining as a LinkedList. In Java 8+, if a bucket has more than 8 entries it converts to a Red-Black Tree for O(log n) performance. Default load factor is 0.75, meaning when the map is 75% full it resizes by doubling the array."

Q5: What is hash collision  
java// hash collision \-\> two different keys produce the same bucket index

System.out.println("Aa".hashCode()); // 2112  
System.out.println("BB".hashCode()); // 2112 \-\> same hashCode, different keys

Map\<String, Integer\> map \= new HashMap\<\>();  
map.put("Aa", 1);  
map.put("BB", 2); // collision \-\> chained in same bucket  
map.get("Aa");    // 1 \-\> resolved via equals()  
map.get("BB");    // 2 \-\> resolved via equals()

// Java 7:  LinkedList per bucket  
// Java 8+: converts to TreeNode (Red-Black Tree) when bucket size \> 8  
**口述**：

"Hash collision happens when two different keys produce the same bucket index. HashMap handles this via chaining — colliding entries are stored as a LinkedList in the same bucket. Java 8 improved this by converting the chain to a Red-Black Tree when bucket size exceeds 8, bringing worst-case lookup from O(n) down to O(log n). Collision is resolved by equals() — that's why hashCode and equals must always be overridden together."

Q6: What is Collections used for  
java// Collections \-\> utility class, all static methods

List\<Integer\> list \= new ArrayList\<\>(Arrays.asList(3, 1, 4, 1, 5));

Collections.sort(list);       // \[1, 1, 3, 4, 5\]  
Collections.reverse(list);    // \[5, 4, 3, 1, 1\]  
Collections.shuffle(list);    // random order  
Collections.max(list);        // 5  
Collections.min(list);        // 1  
Collections.frequency(list, 1); // 2

// make read-only \-\> useful for returning safe copies from APIs  
Collections.unmodifiableList(list);

// thread-safe wrapper  
Collections.synchronizedList(new ArrayList\<\>());  
**口述**：

"Collections is a utility class with all static methods for operating on Collection objects. The most common ones I use are sort, reverse, shuffle, min, max. I also use unmodifiableList when I want to return a read-only view of a list from an API, to prevent the caller from modifying internal state."

Q7: What is immutable class  
java// immutable class \-\> state cannot change after construction  
// rules:  
// 1\. class declared final         \-\> prevent subclassing  
// 2\. all fields private \+ final   \-\> no modification  
// 3\. no setters  
// 4\. deep copy mutable fields in constructor and getters

// Java built-in immutable: String, Integer, LocalDate

final class ImmutablePoint {  
    private final int x;  
    private final int y;

    public ImmutablePoint(int x, int y) {  
        this.x \= x;  
        this.y \= y;  
    }

    public int getX() { return x; }

    // return new instance instead of modifying  
    public ImmutablePoint translate(int dx, int dy) {  
        return new ImmutablePoint(x \+ dx, y \+ dy);  
    }  
}  
**口述**：

"An immutable class is one whose state cannot be changed after construction. To implement it in Java: declare the class as final to prevent subclassing, make all fields private and final, provide no setters, and deep copy any mutable fields. The engineering value is thread safety by design — immutable objects can be safely shared across threads without synchronization. String is the classic example in Java."

Q8: HashTable vs HashMap vs ConcurrentHashMap  
java// thread-safe:    Hashtable \-\> yes (whole map locked) | ConcurrentHashMap \-\> yes (bucket-level)  
//                 HashMap   \-\> no  
// null key:       HashMap \-\> allowed | Hashtable \-\> not allowed | ConcurrentHashMap \-\> not allowed  
// performance:    HashMap \-\> fastest | ConcurrentHashMap \-\> fast | Hashtable \-\> slowest (legacy)

// HashMap \-\> single-threaded  
Map\<String, Integer\> hashMap \= new HashMap\<\>();  
hashMap.put(null, 1); // allowed

// Hashtable \-\> legacy, avoid in new code  
Hashtable\<String, Integer\> hashtable \= new Hashtable\<\>();

// ConcurrentHashMap \-\> multi-threaded, preferred over Hashtable  
ConcurrentHashMap\<String, Integer\> concurrentMap \= new ConcurrentHashMap\<\>();  
concurrentMap.compute("count", (k, v) \-\> v \== null ? 1 : v \+ 1); // atomic operation  
**口述**：

"All three implement the Map interface. HashMap is not thread-safe but fastest for single-threaded use, and allows a null key. Hashtable is legacy — it synchronizes every method which makes it slow, and doesn't allow null keys. ConcurrentHashMap is the modern choice for concurrent environments — it uses bucket-level locking in Java 8 with CAS operations, so multiple threads can read and write simultaneously without blocking the whole map. I'd never use Hashtable in new code."

Q9: String vs StringBuilder vs StringBuffer  
java// immutability: String \-\> immutable | StringBuilder \-\> mutable | StringBuffer \-\> mutable  
// thread-safe:  String \-\> yes       | StringBuilder \-\> no      | StringBuffer \-\> yes  
// performance:  StringBuilder \-\> fastest | StringBuffer \-\> slower (synchronized)

// BAD \-\> creates new String object each iteration, O(n^2)  
String s \= "";  
for (int i \= 0; i \< 5; i++) s \+= i;

// GOOD \-\> single mutable buffer, O(n)  
StringBuilder sb \= new StringBuilder();  
for (int i \= 0; i \< 5; i++) sb.append(i);  
sb.toString(); // "01234"

// StringBuffer \-\> same API as StringBuilder but thread-safe  
StringBuffer sbf \= new StringBuffer();  
sbf.append("Hello").append(" World");  
**口述**：

"String is immutable — every concatenation creates a new object, so doing it in a loop is O(n²) and creates GC pressure. StringBuilder is mutable and not thread-safe — I always use it for string building in single-threaded contexts. StringBuffer has the same API as StringBuilder but is synchronized, so it's thread-safe but slower. In practice I almost always use StringBuilder, and only reach for StringBuffer in multi-threaded scenarios."

Q10: Why override hashCode and equals together  
java// Contract: if a.equals(b) \== true \-\> a.hashCode() MUST \== b.hashCode()

// only override equals \-\> same logical object lands in different buckets \-\> get() returns null  
// only override hashCode \-\> same bucket but equals() returns false \-\> treated as different keys  
// must override BOTH to work correctly with HashMap, HashSet

class Student {  
    private String name;  
    private int id;

    @Override  
    public boolean equals(Object o) {  
        if (\!(o instanceof Student)) return false;  
        Student other \= (Student) o;  
        return this.id \== other.id && this.name.equals(other.name);  
    }

    @Override  
    public int hashCode() {  
        return Objects.hash(name, id); // consistent with equals  
    }  
}

// correct behavior  
Set\<Student\> set \= new HashSet\<\>();  
set.add(new Student("Austin", 101));  
set.add(new Student("Austin", 101)); // size \= 1, not 2  
**口述**：

"The contract in Java is: if two objects are equal by equals(), they must have the same hashCode(). If I only override equals(), two logically equal objects may land in different HashMap buckets, so get() returns null even though the key exists. If I only override hashCode(), they land in the same bucket but equals() still returns false, so they're treated as different entries. Both must be overridden together and stay consistent — otherwise HashMap, HashSet, and any hash-based structure will behave incorrectly."

Q11: Common Data Structure APIs  
java// Map  
Map\<String, Integer\> map \= new HashMap\<\>();  
map.put("a", 1);  
map.putIfAbsent("b", 2);  
map.getOrDefault("z", 0);  
map.merge("a", 10, Integer::sum); // a \= 1 \+ 10 \= 11  
map.forEach((k, v) \-\> System.out.println(k \+ "=" \+ v));

// Set  
Set\<Integer\> set1 \= new HashSet\<\>(Arrays.asList(1, 2, 3));  
Set\<Integer\> set2 \= new HashSet\<\>(Arrays.asList(3, 4, 5));  
set1.retainAll(set2); // intersection \-\> \[3\]  
set1.addAll(set2);    // union

// Queue (FIFO)  
Queue\<String\> queue \= new LinkedList\<\>();  
queue.offer("task1"); // enqueue  
queue.peek();         // look without removing  
queue.poll();         // dequeue

// PriorityQueue \-\> always polls smallest  
Queue\<Integer\> pq \= new PriorityQueue\<\>();  
pq.offer(5); pq.offer(1); pq.offer(3);  
pq.poll(); // 1

// List  
List\<String\> list \= new ArrayList\<\>();  
list.add("a"); list.remove("a");  
list.sort(Comparator.naturalOrder());  
list.contains("b");  
list.subList(0, 2);

// Stack \-\> use Deque, NOT java.util.Stack  
Deque\<Integer\> stack \= new ArrayDeque\<\>();  
stack.push(1); stack.push(2);  
stack.pop(); // 2, LIFO  
**口述**：

"For Map I commonly use put, get, getOrDefault, putIfAbsent, merge, and forEach. For Set the key operations are add, contains, and for set math — retainAll for intersection, addAll for union. For Queue I use offer and poll for FIFO, or PriorityQueue when I need the smallest element first. For List I use add, remove, sort, contains, and subList. For Stack I always use ArrayDeque instead of the legacy Stack class — same push/pop behavior but better performance."

—-----------------------

# Homework 2

a. String vs StringBuilder vs StringBuffer  
String is immutable — it's stored in the constant string pool, so every "modification" actually creates a new object. Because of that it's thread safe by nature. If you do string concatenation in a loop, that's O(n²) and creates a lot of GC pressure, so I'd use StringBuilder instead. StringBuilder is mutable and not thread safe, but it's faster for single-threaded scenarios. StringBuffer is the same as StringBuilder but thread safe because its methods are synchronized.  
javaStringBuilder sb \= new StringBuilder();  
sb.append("hello").append(" world");  
b. Comparator vs Comparable, when to use which  
Both define ordering. Comparable is implemented inside the class via compareTo(), it's the natural ordering. Comparator is defined outside the class via compare(), more flexible, I can have multiple sorting strategies without touching the class. I'd use Comparable when there's one obvious default order, Comparator when I can't modify the class or need different orderings.  
java// Comparable  
public int compareTo(Employee other) { return this.id \- other.id; }  
// Comparator  
Comparator\<Employee\> byName \= (a, b) \-\> a.name.compareTo(b.name);  
c. Overriding vs Overloading  
Overloading is within the same class — same method name but different method signatures, resolved at compile time, so compile-time polymorphism. Overriding is between parent and child class — child provides its own implementation with the exact same signature, resolved at runtime, so runtime polymorphism.  
javavoid print(int a) {}  
void print(String a) {} // overloading, different signature

@Override  
void speak() { System.out.println("Woof"); } // overriding, same signature  
e. Java 8 basic data types  
There are 8 primitive types: byte, short, int, long, float, double, char, boolean. Each has a wrapper class like Integer or Double. Collections can only store objects, so when you add an int to a List, Java automatically autoboxes it into Integer.  
javaList\<Integer\> list \= new ArrayList\<\>();  
list.add(1); // autoboxing, int \-\> Integer  
f. Primitive type vs Reference type  
Primitives store the actual value on the stack, assigning copies the value so two variables are independent. Reference types store a memory address on the stack pointing to the object on the heap, assigning copies the address so both variables point to the same object.  
javaint a \= 5; int b \= a; b \= 10; // a is still 5  
int\[\] arr1 \= {1,2,3}; int\[\] arr2 \= arr1;  
arr2\[0\] \= 99; // arr1\[0\] is also 99 now

d. JRE vs JDK vs JVM  
JVM is the core — it takes the bytecode from the .class file and translates it into machine code that the current platform can execute, which is also why Java is cross-platform. JRE is JVM plus the standard libraries you need to actually run a Java program. JDK is JRE plus development tools like the javac compiler — you need this if you're writing code, not just running it. So the relationship is JDK 被包含JRE 被包含 JVM.

g. How does JVM work  
When you write Java code, javac compiles your .java file into a .class file which contains bytecode — it's not machine code yet, it's an intermediate format. Then the JVM takes over: the Class Loader loads the .class file into memory, and the Execution Engine translates the bytecode into machine code and runs it. This is why Java is platform-independent — the same bytecode runs on any OS as long as there's a JVM for that platform.  
.java \-\> javac \-\> .class (bytecode) \-\> Class Loader \-\> JVM \-\> machine code

h. JVM memory data model  
JVM memory is divided into five areas. Method Area stores class metadata, static variables, and the String Pool. VM Stack is per-thread — each method call pushes a frame onto the stack with its local variables, and pops it when the method returns. **Heap is where all objects live** — anything you new goes here, and this is what GC manages. Program Counter Register tracks which bytecode instruction the current thread is executing. Native Method Stack is for native methods that call into C/C++ libraries.

i. How does GC work  
GC automatically reclaims memory on the heap when objects are no longer reachable — meaning no references point to them anymore. You don't need to manually free memory like in C++. The basic algorithm is reachability — starting from "GC roots" like static variables and stack variables, GC traces all reachable objects and collects everything else.  
javaEmployee emp \= new Employee();  
emp \= null; // no more references \-\> eligible for GC

j. young/old/perm generation  
The heap is divided into generations because most objects die young — they get created and become unreachable very quickly. Young Generation is where new objects go first, GC runs frequently here (Minor GC). Objects that survive enough GC cycles get promoted to Old Generation, where GC runs less frequently (Major GC). Perm Generation (before Java 8\) or Metaspace (Java 8+) stores class metadata — it's not really part of the heap, Metaspace uses native memory and can grow dynamically.

k. different types of GC  
There are several GC algorithms in Java, each with different trade-offs between throughput and pause time:  
G1 GC — divides heap into regions instead of fixed young/old areas, predictable pause times. Default since Java 9\.  
ZGC / Shenandoah — ultra-low latency, pause times under 10ms regardless of heap size. Good for large heap applications.  
java// 启动时指定 GC 类型  
java \-XX:+UseG1GC \-jar main.jar  
java \-XX:+UseZGC \-jar main.jar  
**面试时候可说**: "The choice of GC depends on your application's requirements. If you need high throughput, Parallel GC. If you need low latency, G1 or ZGC. Most production systems today use G1 as the default."

# Homework 3

a. Java modifier scope  
Access modifiers control visibility — this is the core mechanism behind encapsulation. public means accessible everywhere. private means only within the same class. protected means the same class, subclasses, and same package. Default (no keyword) means same package only.  
javapublic String name;    // everywhere  
private double salary; // this class only  
protected int id;      // subclasses \+ same package  
String dept;           // same package only (default)  
In practice I always default to the minimum access level — fields are almost always private, exposed only through getters if needed.

b. What is static scope  
Static scope means the field or method belongs to the class itself, not to any instance. It lives in Method Area, shared across all instances, and exists for the entire lifecycle of the class. GC doesn't touch it — which is also why holding a large object reference in a static field can cause memory leaks.  
javastatic int count \= 0; // shared across all instances  
Employee.create();    // called on class, no instance needed

c. How does ClassLoader work  
ClassLoader loads .class files from disk into JVM memory. It works in three phases: Loading reads the .class file, Linking verifies the bytecode and allocates memory for static variables, and Initialization runs static blocks and assigns static field values.  
There are three built-in ClassLoaders in a parent delegation hierarchy — Bootstrap loads core Java classes, Extension loads from lib/ext, Application loads my own classes from the classpath. When a class needs to be loaded it always delegates to the parent first, only loading itself if the parent can't find it. This prevents someone from replacing core Java classes with malicious versions.

d. checked vs unchecked exceptions  
Checked exceptions are verified at compile time — the compiler forces me to handle them with try-catch or declare them with throws. They represent situations I can anticipate and potentially recover from, like IOException or SQLException. Unchecked exceptions extend RuntimeException and the compiler doesn't force me to handle them — they usually indicate bugs in my code like NullPointerException or ArrayIndexOutOfBoundsException.  
javapublic void readFile() throws IOException {} // checked, must declare

String s \= null;  
s.length(); // NullPointerException, unchecked, blows up at runtime

e. finally vs final vs finalize  
Three completely different things. finally is a block in try-catch that always executes regardless of whether an exception was thrown — I use it to release resources. final is a keyword: on a class it prevents subclassing, on a field it prevents reassignment, on a method it prevents overriding. finalize is a deprecated method on Object that GC used to call before reclaiming an object — it's unpredictable because I can't control when GC runs, so I'd never rely on it for cleanup. I'd use try-with-resources instead.

f. try-with-resources vs ordinary try  
With an ordinary try I have to manually close resources in the finally block — easy to forget. Try-with-resources automatically closes any resource that implements AutoCloseable when the block exits, even if an exception is thrown.  
java// ordinary  
try { ... } finally { conn.close(); }

// try-with-resources \-\> closes automatically  
try (Connection conn \= getConnection()) {  
    // use conn  
}

g. Runtime Exception with example  
RuntimeException is the parent of all unchecked exceptions. They're not checked at compile time and typically indicate bugs in my code rather than external conditions I need to handle.  
javaString s \= null;  
s.length();          // NullPointerException

int\[\] arr \= new int\[3\];  
arr\[5\] \= 10;         // ArrayIndexOutOfBoundsException

Object obj \= "hello";  
Integer i \= (Integer) obj; // ClassCastException

h. NoClassDefFoundError vs ClassNotFoundException  
Both happen when a class can't be found, but at different times. ClassNotFoundException is a checked exception thrown when I try to dynamically load a class using Class.forName() and it doesn't exist on the classpath — the class never existed. NoClassDefFoundError is an Error thrown when the class existed at compile time but is missing at runtime — usually the jar wasn't included in the deployment.  
javaClass.forName("com.example.Missing"); // ClassNotFoundException

// compiled fine, jar missing at runtime \-\> NoClassDefFoundError

i. Why clean up I/O resources in finally  
I/O resources like database connections and file handles are limited system resources — they don't get released automatically when an object goes out of scope. If an exception is thrown before I close them, they stay open and eventually I'll run out of available connections or file descriptors. The finally block guarantees cleanup happens regardless of what went wrong.

j. OutOfMemoryError  
OutOfMemoryError is a subclass of Error, not Exception — it's a JVM-level problem I generally can't recover from. It happens when the JVM can't allocate more memory on the heap, usually because too many objects are being created and GC can't reclaim space fast enough.  
javaList\<byte\[\]\> list \= new ArrayList\<\>();  
while (true) {  
    list.add(new byte\[1024 \* 1024\]); // heap fills up \-\> OOM  
}  
Common causes are memory leaks from static fields holding references, loading too much data into memory at once, or just heap size being too small. I can increase it with \-Xmx.

k. What is Generics, advantages  
Generics provide compile-time type safety. Without them I'm working with raw types and the compiler lets me put anything in a collection — type mismatches only surface at runtime as ClassCastException. With generics the compiler catches type errors immediately. It also eliminates the need for explicit casting when I get something out of a collection. The core value is fail fast at compile time rather than fail late at runtime.

l. How does Generics work, type erasure  
Generics are a compile-time feature only. At runtime all generic type information is erased — this is called type erasure. So List\<String\> and List\<Integer\> are both just List at runtime. The compiler inserts the necessary casts automatically. This is also why I can't do new T() or instanceof T inside a generic class — that type information simply doesn't exist at runtime.

m. List\<? extends T\> vs List\<? super T\>  
\<? extends T\> is an upper bound — the list holds T or any subclass. I can safely read from it as T, but I can't write to it because the compiler doesn't know the exact subtype. \<? super T\> is a lower bound — the list holds T or any superclass. I can write T and its subclasses into it, but reading gives me only Object because the exact supertype is unknown. The rule of thumb is PECS — Producer Extends, Consumer Super.

n. Optional class  
Optional is a container that may or may not hold a value — it forces me to explicitly handle the case where a value might not exist, instead of silently returning null and risking a NullPointerException downstream.  
javaOptional\<String\> name \= Optional.ofNullable(getName());  
name.orElse("default");  
name.orElseThrow(() \-\> new EmployeeNotFoundException("not found"));  
name.ifPresent(n \-\> System.out.println(n));

o. What is OOP  
OOP organizes code around objects rather than functions. Java has four pillars. Encapsulation — I use access modifiers to control what's exposed, fields are private, accessed through getters and setters. Inheritance — a child class extends a parent, reusing and extending behavior. Java only allows single class inheritance but multiple interface implementation. Polymorphism — overloading at compile time with different method signatures, overriding at runtime with the same signature. Abstraction — hiding implementation details through abstract classes and interfaces, exposing only what's necessary.

# Homework 4

Homework 4  
a. What is functional interface  
A functional interface is an interface with exactly one abstract method. This is what makes lambda expressions possible in Java 8 — because there's only one abstract method, the compiler knows exactly what the lambda is implementing. I can use @FunctionalInterface to enforce this at compile time.  
java@FunctionalInterface  
interface Validator {  
    boolean validate(String input);  
}  
Validator v \= input \-\> input.length() \> 0;

b. What is default method  
A method in an interface that has a body, introduced in Java 8\. Before this, any change to an interface would break all implementing classes. Default methods let me add new behavior to an interface without forcing every implementation to change — the class can either use the default or override it.  
javainterface Printable {  
    default void print() { System.out.println("default print"); }  
}

c. Predicate vs Supplier vs Consumer vs Function  
All four are built-in functional interfaces, each with a different purpose based on what they take and return.  
javaPredicate\<Integer\> isAdult \= age \-\> age \>= 18;       // input \-\> boolean, used for filtering  
Supplier\<Employee\> factory \= () \-\> new Employee();    // no input \-\> value, lazy generation  
Consumer\<String\> printer \= s \-\> System.out.println(s); // input \-\> void, side effects  
Function\<String, Integer\> toLength \= s \-\> s.length(); // input \-\> different type, transformation

d. Code using all four  
javaisAdult.test(20);          // true  
factory.get();             // new Employee instance  
printer.accept("Austin");  // prints Austin  
toLength.apply("hello");   // 5

e. What is method reference  
A shorthand for a lambda that just calls an existing method, using ::. It's cleaner and more readable when the lambda body is just a single method call.  
javalist.forEach(s \-\> System.out.println(s)); // lambda  
list.forEach(System.out::println);        // method reference, same thing

Function\<String, Integer\> parse \= Integer::parseInt; // static method reference

f. What is CompletableFuture  
CompletableFuture is Java's way of doing asynchronous programming. Instead of blocking the current thread waiting for a result, I can kick off a task on another thread and chain what should happen next when it completes.  
javaCompletableFuture.supplyAsync(() \-\> fetchEmployee(id))  
    .thenApply(emp \-\> emp.getName())  
    .thenAccept(System.out::println)  
    .exceptionally(e \-\> { log.error("failed"); return null; });

g. Default keyword vs Java default scope  
Two completely different things. The default keyword in an interface gives a method a body — it's an implementation. Default scope (no access modifier) is a visibility level meaning the field or method is only accessible within the same package.  
javainterface Printable { default void print() {} } // keyword, has implementation  
class Employee { String name; }                 // default scope, package-private

h. Stream coding — Student  
javaclass Student { String name; int age; int score; String gender; }

// names starting with 'A'  
list.stream().filter(s \-\> s.name.startsWith("A")).collect(Collectors.toList());

// sum of all scores  
list.stream().mapToInt(s \-\> s.score).sum();

// students with score \>= 60  
list.stream().filter(s \-\> s.score \>= 60).collect(Collectors.toList());

// all names  
list.stream().map(s \-\> s.name).collect(Collectors.toList());

// frequency of each age  
list.stream().collect(Collectors.groupingBy(s \-\> s.age, Collectors.counting()));

// count by gender  
list.stream().collect(Collectors.groupingBy(s \-\> s.gender, Collectors.counting()));

i. intermediate vs terminal operation  
Intermediate operations are lazy — they don't actually execute until a terminal operation is called, and they return a Stream so I can keep chaining. filter(), map(), sorted() are all intermediate. Terminal operations trigger the actual execution of the whole pipeline and return a result — collect(), count(), forEach(), sum().  
javalist.stream()  
    .filter(s \-\> s.score \>= 60\) // intermediate, lazy  
    .map(s \-\> s.name)           // intermediate, lazy  
    .collect(Collectors.toList()); // terminal, triggers everything

j. count frequency of each char  
javachar\[\] chars \= {'a', 'b', 'a', 'c', 'b', 'a'};  
Map\<Character, Long\> freq \= new String(chars).chars()  
    .mapToObj(c \-\> (char) c)  
    .collect(Collectors.groupingBy(c \-\> c, Collectors.counting()));  
// {a=3, b=2, c=1}

k. map() vs flatMap()  
map() is one-to-one — each element gets transformed into one output. flatMap() is one-to-many — each element gets transformed into a stream, and then all those streams get flattened into one.  
java// map \-\> one to one  
list.stream().map(s \-\> s.name).collect(Collectors.toList());

// flatMap \-\> flatten nested structure  
List.of("hello world", "foo bar").stream()  
    .flatMap(s \-\> Arrays.stream(s.split(" ")))  
    .collect(Collectors.toList()); // \["hello", "world", "foo", "bar"\]

# Homework 5

\#\# a. How to create a thread (4 ways)

There are 4 ways to create a thread in Java.

First, extends Thread — but the problem is Java only supports single inheritance, so if my class already extends something else this doesn't work.

Second, implements Runnable — this is preferred since it gets around the single inheritance limitation. run() has no return value.

Third, implements Callable — same as Runnable but call() has a return value and can throw checked exceptions. The result is retrieved via future.get() which blocks.

Fourth, Lambda — just a concise way to implement Runnable inline.

\`\`\`java  
Thread t \= new Thread(() \-\> System.out.println("running"));  
t.start();  
\`\`\`

Key point: always call start(), not run(). Calling run() directly is just a normal method call on the main thread — no new thread is created.

\---

\#\# b. Thread lifecycle

\`\`\`  
NEW → RUNNABLE → RUNNING → TERMINATED  
              ↕  
         BLOCKED/WAITING  
\`\`\`

NEW when I call new Thread(). RUNNABLE after start() is called — it's ready but waiting for CPU time. RUNNING when the CPU picks it up. BLOCKED or WAITING when it's waiting for a lock or sleep(). TERMINATED when run() finishes. Transitions are one-directional — can't restart a terminated thread.

\---

\#\# c. How does thread pool work

Thread pool pre-creates threads at startup so we don't pay the cost of creating and destroying threads for every task. When a task comes in, it grabs an idle thread from the pool, executes the task, then returns the thread to the pool.

Task assignment flow: core pool has idle thread → assign directly. All core threads busy → queue the task. Queue full → create new thread up to max size. Max reached → trigger rejection policy.

\`\`\`java  
new ThreadPoolExecutor(5, 10, 30L, TimeUnit.SECONDS,  
    new LinkedBlockingQueue\<\>(1000));  
\`\`\`

\---

\#\# d. Potential problems with newCachedThreadPool and newFixedThreadPool

newCachedThreadPool has corePoolSize of 0 and max of Integer.MAX\_VALUE — under sudden high load it can create millions of threads and cause OOM.

newFixedThreadPool uses an unbounded LinkedBlockingQueue — tasks pile up in the queue forever and cause OOM.

Both hide these risks behind a simple factory method. In production I always use ThreadPoolExecutor directly with explicit bounded parameters so I have full control.

\---

\#\# e. What is Future

Future represents the result of an async computation. I get it back when I submit a Callable to an executor. I call future.get() to retrieve the result but that blocks the calling thread until it's done. If I have multiple futures I have to block multiple times. That's the main limitation — which is why CompletableFuture is preferred in modern code.

\---

\#\# f. What is CompletableFuture

CompletableFuture is Java 8's way of doing async programming without blocking. Instead of calling get() and waiting, I chain operations that execute automatically when the previous step completes. The main thread is never blocked — results flow through the chain automatically. It's preferred in web applications where blocking threads is expensive.

\---

\#\# g. Future vs CompletableFuture

Future requires get() to block and wait for the result. If I have multiple tasks I block multiple times. CompletableFuture lets me chain everything with thenApply, thenAccept, exceptionally — no blocking, results flow automatically. CompletableFuture also supports exception handling in the chain and combining multiple async tasks with allOf and anyOf. Future has none of that.

\---

\#\# h. Lock vs synchronized

Both provide mutual exclusion but ReentrantLock gives more control. synchronized is simpler — I just wrap the critical section and the lock releases automatically. ReentrantLock requires manual unlock in a finally block, but gives me extras like tryLock() to attempt without blocking, timeout-based locking, fair lock mode where threads acquire in order, and interruptibility. I use synchronized for simple cases and ReentrantLock when I need that extra flexibility.

\---

\#\# i. wait(), notify(), notifyAll(), join()

wait() releases the lock and puts the thread in WAITING state — must be called inside a synchronized block. notify() wakes up one arbitrary waiting thread. notifyAll() wakes up all waiting threads — I prefer this over notify() to avoid missed signals. Always use a while loop around wait(), not if, to guard against spurious wakeups.

join() is different — it makes the current thread wait until another specific thread finishes.

In practice I avoid raw wait/notify and use BlockingQueue or CountDownLatch instead — safer and more readable.

\---

\#\# j. CompletableFuture common APIs

\*\*runAsync / supplyAsync\*\* — entry points. runAsync for no return value, supplyAsync when I need a result.

\*\*thenApply / thenApplyAsync\*\* — transform the result. thenApply runs on the same thread, thenApplyAsync switches to a new thread — important when the next step is I/O bound so it doesn't block the current thread.

\*\*exceptionally / handle\*\* — exceptionally only runs on exception and provides a fallback. handle runs always and gets both the result and exception, so I can handle both cases in one place.

\*\*thenCompose / thenCombine\*\* — thenCompose chains two CompletableFutures sequentially, like flatMap. thenCombine runs two independently and combines their results when both finish.

\*\*allOf / anyOf\*\* — allOf waits for all tasks to complete. anyOf completes as soon as any one of them finishes.