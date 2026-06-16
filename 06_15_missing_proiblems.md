https://austin-rt-dev.s3.us-east-2.amazonaws.com/06_15_missing_problems.mov

How to increase young generation size in heap
I use JVM flags at startup. -Xms and -Xmx set the total heap size. -Xmn sets the young generation size directly, for example -Xmn512m. I can also use -XX:NewRatio to control the ratio between young and old generation. I'd increase young gen size when I have a lot of short-lived objects and minor GC is running too frequently.

How does Spring MVC work
A request comes in and hits the DispatcherServlet first. It checks the HandlerMapping to find which controller method handles this URL. Then it calls that method. The controller calls the service layer, gets the result, and returns it. If it's a REST controller, Spring serializes the return value to JSON and writes it to the response. That's the whole flow.

How do you handle exceptions in Java
I use try-catch-finally. Risky code goes in try, I catch specific exceptions in catch, finally runs no matter what for cleanup. Java has two categories. Checked exceptions like IOException — the compiler forces me to handle them. Unchecked like NullPointerException — those usually mean bugs in my code. For resource cleanup I prefer try-with-resources so connections close automatically. In Spring Boot I use @RestControllerAdvice as a global handler so every unhandled exception returns a proper error response instead of a raw 500.

REST API vs Message Queue
REST API is synchronous. The client sends a request and waits for a response. Good when I need an immediate result. Message queue is asynchronous. The sender puts a message on the queue and moves on. The consumer picks it up later independently. I use a message queue when the operation is slow and I don't need an immediate response, or when I want to decouple two services. Common tools are RabbitMQ and Kafka.

What annotations do we use to configure customized Actuator
I put @Endpoint on the class to register it with Actuator. Then inside the class I use @ReadOperation for GET, @WriteOperation for POST, and @DeleteOperation for DELETE. The ID I give to @Endpoint becomes the path under /actuator/. If I only want it exposed over HTTP I use @WebEndpoint instead.

Can abstract class have no abstract method
Yes. An abstract class can have zero abstract methods. The only rule is it cannot be instantiated directly. I'd do this when I want to prevent direct instantiation but still provide some shared implementation for subclasses to inherit.

How can you use Optional
Optional is a container that either holds a value or holds nothing. It forces me to handle the absent case instead of returning null. I use orElse to provide a default, orElseThrow to throw a meaningful exception when empty, ifPresent to run logic only when the value exists, and map to transform the value if it's there. I typically use it as a return type when a result might not exist.

What is a functional interface
It's an interface with exactly one abstract method. That's what makes lambdas possible in Java 8. Because there's only one method, the compiler knows exactly what the lambda is implementing. I can add @FunctionalInterface to enforce this at compile time. Built-in examples are Predicate, Supplier, Consumer, and Function.

Why do you use POST instead of PUT
POST is for creating a new resource where the server assigns the ID. PUT is for fully replacing an existing resource at a known ID. The other difference is idempotency. PUT is idempotent — calling it multiple times with the same body gives the same result. POST is not — calling it twice creates two records. So when I'm creating something new I use POST.

What is WebFlux, have you used it in your project
WebFlux is Spring's reactive web framework. Instead of one thread per request, it uses a non-blocking event loop. A small number of threads handle many concurrent requests because they never sit idle waiting for I/O. It uses Mono for a single value and Flux for a stream. I haven't used it in my current project. I'm using standard Spring MVC which fits our use case. WebFlux makes more sense for very high concurrency with lots of I/O-bound operations.

What is HashMap
HashMap stores key-value pairs. Internally it uses an array of buckets. When I call put(), it computes a bucket index from the key's hashCode(). If the bucket is empty it stores the entry. If occupied it uses equals() to check — same key overwrites the value, different key means collision, stored as a linked list. In Java 8 if a bucket has more than 8 entries it converts to a Red-Black Tree. Default capacity is 16, load factor 0.75, so it resizes when 75% full. Average O(1) for get and put. Not thread safe. Allows one null key.

What is @EnableAutoConfiguration
It's one of the three annotations inside @SpringBootApplication. It tells Spring Boot to automatically configure beans based on what's on the classpath. If Spring MVC is there it sets up a DispatcherServlet. If JPA is there it sets up an EntityManager. It works by reading META-INF/spring.factories files in the starter jars. I can exclude specific configurations if I need to override the default. The whole point is to get a working setup without manual configuration.
