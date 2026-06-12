https://austin-rt-dev.s3.us-east-2.amazonaws.com/06_11_mocking_problems.mov
Q1: What Spring Boot version did you use?
"I used Spring Boot 3.0. It requires Java 17 or above. The main difference from 2.x is that it migrated from javax to jakarta namespace, so all the imports changed. It also has better native compilation support via GraalVM."

Q2: What annotations did you use in Spring?
"I used annotations across a few different areas. For IoC, the main one is @SpringBootApplication. It actually combines three annotations — @EnableAutoConfiguration to auto-configure beans based on the classpath, @SpringBootConfiguration to mark it as a config class, and @ComponentScan to scan and register all beans.
For bean registration, I used @RestController, @Service, @Repository for the three layers. For anything outside those three, like a thread pool or DB connection pool, I used @Component. And for third-party libraries where I don't control the source code, I used @Bean with @Configuration.
For dependency injection, I used @Autowired. It injects by type by default. If there's a type conflict, I use @Qualifier to pick by name, or @Primary to set a default.
For AOP, I used @RestControllerAdvice for global exception handling. For the aspect part, I used @Aspect and @Pointcut to define which methods to intercept. Then @Before, @After, @Around, @AfterThrowing, and @AfterReturning to control when the logic runs.
And for bean scopes, I used @Scope. The default is singleton. But you also have prototype, session, request, and application."

Q3: How does Spring IOC work? (all annotations + injection + bean types)
"Spring IoC means the Spring container is responsible for creating and managing your objects. You just annotate the classes and Spring handles the rest.
For annotations, @SpringBootApplication is the entry point. It includes @EnableAutoConfiguration, @SpringBootConfiguration, and @ComponentScan. For bean registration, @RestController, @Service, @Repository cover the three layers. @Component for anything else. @Bean plus @Configuration for third-party code. For AOP, @RestControllerAdvice for exception handling. @Aspect and @Pointcut to define where. Then @Before, @After, @Around, @AfterThrowing, @AfterReturning to define when.
For injection, I use @Autowired. It supports field, constructor, and setter injection. I prefer constructor injection because it makes sure all dependencies are ready at startup, which prevents NullPointerException at runtime. If there's a conflict, I use @Qualifier or @Primary.
For bean scopes — singleton is the default, one shared instance across the whole app. Prototype gives you a new instance every time. Request gives one per HTTP request. Session gives one per HTTP session. Application gives one per application context."

Q4: How to write REST API in Spring Boot?
"In Spring Boot, you start with @RestController on the class — that's actually @Controller plus @ResponseBody combined. Then @RequestMapping to define the base URL. For each endpoint, you use @GetMapping, @PostMapping, @PutMapping, or @DeleteMapping. The URL should be noun-based, no verbs.
For request input, you use @RequestParam for query params, @PathVariable for URL path values, @RequestHeader for headers, and @RequestBody for the JSON payload. The response comes back with a response body plus an HTTP status code — 2xx for success, 3xx for redirect, 4xx for client errors, 5xx for server errors. Jackson handles the JSON serialization automatically."

Q5: How to stop auto-configuration in Spring Boot?
"You use the exclude attribute on @SpringBootApplication. For example, @SpringBootApplication(exclude = {DataSourceAutoConfiguration.class}). That tells Spring Boot to skip that specific auto-configuration. You can also do it in application.properties with spring.autoconfigure.exclude. This is useful when Spring Boot tries to configure something you don't need and throws an error on startup."

Q6: How can you use Profile?
"There are three ways to activate a profile. First, in application.properties or application.yml, you set spring.profiles.active=dev. Spring will then load application-dev.properties on top of the base config. Second, via command line — java -jar springboot.jar --spring.profiles.active=qa. Third, on a remote Linux server, you set an environment variable — export SPRING_PROFILES_ACTIVE=QA. Spring Boot scans for that variable at startup. If nothing is set, it just falls back to the default application.properties."

Q7: What is @EnableAutoConfiguration?
"@EnableAutoConfiguration tells Spring Boot to automatically configure beans based on what's on the classpath. So if you have a datasource dependency in your pom.xml, Spring Boot automatically sets up a DataSource bean for you without any manual config. In Spring Boot 2.x it reads from META-INF/spring.factories. In 3.x it reads from AutoConfiguration.imports. That's what makes Spring Boot convention-over-configuration."

Q8: CompletableFuture / Multithreading in Spring?
"In Spring, we handle async processing with @Async and CompletableFuture. You annotate a method with @Async and enable it with @EnableAsync on the config class. Spring then runs that method in a separate thread without blocking the main thread. The return type is CompletableFuture<T>. You can chain operations on it — thenApply to transform the result, thenAccept to consume it, thenCompose to chain two async tasks, allOf to wait for all tasks, anyOf to continue when the first one finishes.
For the thread pool, the default is SimpleAsyncTaskExecutor which creates a new thread every time — not good for production. So we define a custom ThreadPoolTaskExecutor bean to control the pool behavior."

Q9: How do you customize a thread pool?
"You create a config class with @Configuration and @EnableAsync, implement AsyncConfigurer, and override getAsyncExecutor(). Inside you set up a ThreadPoolTaskExecutor with seven parameters — core pool size for always-alive threads, max pool size for the upper limit under high load, queue capacity for tasks waiting in line, keep alive seconds for idle threads above core size, thread name prefix for easier log debugging, rejected execution handler for when the queue is full, and wait for tasks to complete on shutdown for graceful shutdown.
In production we also separate primary and auxiliary thread pools. Primary handles core business like financial operations. Auxiliary handles trivial tasks like emails and report generation."

Q10: What annotation do you use to customize Actuator?
"To define a custom actuator endpoint, you use @Endpoint. Then @ReadOperation for GET, @WriteOperation for POST, and @DeleteOperation for DELETE. To control which endpoints are exposed, you configure management.endpoints.web.exposure.include or exclude in application.properties. In practice though, custom actuator endpoints are rarely written manually — monitoring data is usually sent to Prometheus and visualized in Grafana."

Q11: How to customize annotation?
"You use @interface to define a custom annotation. Then you put meta-annotations on it — @Target to define where it can be used, like method or class level, and @Retention(RetentionPolicy.RUNTIME) so it's available at runtime via reflection. Then you create an @Aspect class, define a @Pointcut that targets your custom annotation, and write the advice with @Around or @Before to add the logic. A common example is a custom @LogExecutionTime annotation that logs how long a method takes to run."

Q12: What are meta-annotations?
"Meta-annotations are annotations you put on other annotations to configure how they behave. The two most important ones are @Target and @Retention. @Target defines where the annotation can be applied — method, field, or class level. @Retention defines how long it lives — SOURCE gets discarded after compilation, CLASS stays in bytecode but isn't available at runtime, RUNTIME means you can access it via reflection, which is what you need for Spring AOP. Other ones include @Documented for Javadoc, @Inherited so subclasses inherit it, and @Repeatable so you can apply the same annotation multiple times."

Q13: Can AOP apply to private methods?
"No it can't. Spring AOP works through proxies. When you inject a bean, Spring wraps it in a proxy and intercepts calls through that proxy. Private methods aren't accessible through the proxy so they get skipped. Same thing if you call a method internally within the same class — that call bypasses the proxy too so AOP won't trigger. If you really need to intercept private methods you'd need AspectJ with compile-time bytecode weaving. But in practice we just avoid putting logic that needs interception on private methods."

Q14: When do we need to use WebSocket?
"WebSocket is for real-time bidirectional communication. Unlike HTTP which is stateless and request-response based, WebSocket keeps a persistent connection open so both sides can push data any time without the client having to poll. Typical use cases are live chat, real-time notifications, stock price feeds, or collaborative tools like Google Docs. In Spring you implement it with @EnableWebSocket and a WebSocketHandler, or use STOMP on top of WebSocket with @EnableWebSocketMessageBroker for pub-sub messaging."

Q15: How did you debug?
"First I check the logs — look at the HTTP status code and the error stack trace to understand what went wrong. Then I identify the exact request that triggered the issue. Then I go to the dev environment and reproduce it using Postman with the same request payload. Then I open IntelliJ, set breakpoints at the suspicious area, and step through line by line until I find the root cause. Fix it and verify."

Q16: Diff recursion and iteration?
"Recursion is when a method calls itself. It's clean and readable, especially for tree or graph problems. But every call adds a stack frame, so if it goes too deep you get a StackOverflowError. Iteration uses loops. It's more memory efficient, no call stack overhead, better for performance in production. In Java there's no tail-call optimization, so for large inputs iteration is the safer choice."

Q17: LinkedHashMap vs HashMap?
"Both implement the Map interface and store key-value pairs with unique keys. The main difference is ordering. HashMap has no guaranteed iteration order. LinkedHashMap maintains insertion order using a doubly-linked list internally. Performance-wise HashMap is slightly faster since there's no linked list overhead, but both are O(1) for get and put. Neither is thread-safe — for thread safety use ConcurrentHashMap. LinkedHashMap is commonly used for LRU cache by overriding removeEldestEntry."

Q18: Design a locking schema so that thread1 calling method1() waits until thread2 calls method2()?
"I'd use a CountDownLatch initialized to 1. Thread 1 calls method1() then calls latch.await(), which blocks it. Thread 2 calls method2() then calls latch.countDown(), which releases Thread 1. This is cleaner than wait/notify because CountDownLatch is built exactly for this kind of one-time signaling. Another option is a Semaphore initialized to 0 — Thread 1 calls acquire() to block, Thread 2 calls release() after method2() to unblock it. I'd go with CountDownLatch since the signal only needs to happen once."
