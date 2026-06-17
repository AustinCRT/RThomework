https://austin-rt-dev.s3.us-east-2.amazonaws.com/06_16_mocking_problems.mov

Q1: SQL Coding - emp and dept table
"I'd create two tables. First the dept table with dept_id as primary key and dept_name. Then the emp table with emp_id as primary key, emp_name, salary, and dept_id as a foreign key referencing the dept table. The foreign key ensures referential integrity — you can't insert an employee with a dept_id that doesn't exist in the dept table."

Q2: If we override hashCode but not equals
"If you override hashCode but not equals, it breaks hash-based structures like HashMap and HashSet. When two objects land in the same bucket, Java uses equals to decide — is this a hash collision or a value update? If equals isn't overridden, it only checks reference. So it always treats them as a collision and adds a duplicate instead of updating. Always override both together."

Q3: How you write REST API in Spring Boot
"I use @RestController on the class — that's @Controller plus @ResponseBody combined. Then @RequestMapping with the version in the URL, like /v1/employees. No verbs in the URL, noun-based only.
For each endpoint, @GetMapping, @PostMapping, @PutMapping, @DeleteMapping.
For input, four options — @RequestParam for query params, @PathVariable for URL path values, @RequestHeader for headers, @RequestBody for JSON payload. I also add @Valid on the request body, and on the DTO fields I use @NotNull, @Min, @Max, @Email.
For the response, I use ResponseEntity to control both the payload and the HTTP status code. I also apply AOP with @Aspect and @Around for logging, and @RestControllerAdvice with @ExceptionHandler for global exception handling."

Q4: What is thread state
"A thread in Java has six states.
New — thread is created but start() hasn't been called yet.
Runnable — once you call start(), the thread is ready to run, waiting for CPU time.
Running — thread gets CPU and is actively executing.
Blocked — thread is trying to enter a synchronized block but another thread is holding the lock.
Waiting — thread called wait() or join(), waiting indefinitely for notify() or notifyAll().
Timed Waiting — same as Waiting but with a timeout, like sleep(1000).
Terminated — thread finished, run() method returned."

Q5: What is ThreadLocal
"ThreadLocal gives each thread its own isolated copy of a variable. Other threads can't see or modify it. It's useful for storing per-thread data like user sessions, database connections, or transaction context — things you don't want to share across threads. You use set() to store a value and get() to retrieve it. Always call remove() when done to prevent memory leaks, especially in thread pools where threads get reused."

Q6: What is CORS
"CORS stands for Cross Origin Resource Sharing. By default, browsers block requests from a different origin — different domain, port, or protocol. So if Angular runs on port 4200 and Spring Boot runs on port 8080, the browser rejects the request — different port means different origin.
To fix this, I use @CrossOrigin on the controller and whitelist the frontend origin like localhost:4200. Any request from an origin not on the whitelist gets blocked. This protects the backend from malicious cross-origin attacks."

Q7: Map vs filter
"Both are intermediate operations in the Stream API from Java 8.
filter takes a Predicate — one input, returns Boolean. Keeps only elements that match the condition. Output size is smaller or equal. For example, filter employees older than 30.
map takes a Function — one input, one output of a different type. Transforms each element. Output size stays the same. For example, convert Employee entity to DTO to hide sensitive fields like SSN before sending to the frontend.
We pass the logic using lambda expressions."

Q8: Thread lifecycle
"The thread lifecycle goes: New → call start() → Runnable → gets CPU → Running → if it hits a synchronized block with a locked monitor it goes to Blocked → if it calls wait() it goes to Waiting → if it calls sleep(1000) it goes to Timed Waiting → when done it goes to Terminated."

Q9: By name vs by type
"When Spring injects a bean, it first matches by type — finds a bean that matches the declared type. If there are multiple beans of the same type, it falls back to by name — tries to match the field name or parameter name to a bean name. If it still can't decide, you get NoUniqueBeanDefinitionException. Fix it with @Qualifier to specify by name, or @Primary to mark one as the default."

Q10: How to send request from Angular to backend
"In Angular, I use HttpClient to send requests. Import HttpClientModule in the app module, inject HttpClient into the service, then call http.get(), http.post(), http.put(), or http.delete() with the backend URL.
By default it's synchronous-style. To make it async, use async/await with firstValueFrom() to convert the Observable to a Promise.
On the backend, make sure @CrossOrigin is configured with the frontend origin whitelisted — otherwise the browser blocks the request."

Q11: What is pattern matching
"Pattern matching was introduced in Java 16 for instanceof. Before, you had to cast manually — if (obj instanceof String) { String s = (String) obj; }. With pattern matching, you write if (obj instanceof String s) — the cast happens automatically.
Java 21 extended this to switch — you can match on type directly in the switch case. Like case Integer i, case String s, case Long l — each case handles a different type and binds it to a variable automatically. Much cleaner than chained if-else with casting."

Q12: Spring Boot Actuator
"Spring Boot Actuator gives you production-ready monitoring endpoints. Just add spring-boot-starter-actuator dependency. It exposes endpoints like /actuator/health, /actuator/metrics, /actuator/info, and /actuator/env.
By default only /health and /info are exposed. You can expose more with management.endpoints.web.exposure.include=* in application.properties.
For production monitoring, I use Micrometer as the data collector — it bridges Actuator and Prometheus. Prometheus is the time series database that stores the metrics. Grafana connects to Prometheus and visualizes everything on a dashboard based on time range. I also secure the actuator endpoints with Spring Security."
