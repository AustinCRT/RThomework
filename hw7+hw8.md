Homework 7

a. Optimized Singleton — Double-Checked Locking
javapublic class Singleton {

    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
Line by line:

private static volatile Singleton instance — volatile prevents instruction reordering. Creating an object is actually 3 steps under the hood: allocate memory, initialize, assign reference. Without volatile the JVM can reorder step 2 and 3, so another thread could grab a half-initialized instance.
private Singleton() — blocks anyone from calling new directly, forces everything through getInstance().
First if (instance == null) — avoids grabbing the lock on every call. Once the instance exists, this check fails and we just return, no locking needed.
synchronized (Singleton.class) — only locks when instance might not exist yet.
Second if (instance == null) — two threads can both pass the first check at the same time. The one that loses the lock race would create a duplicate without this second check.
instance = new Singleton() — actually creates the object.
return instance — returns the single shared instance.


b. What is Reflection
Reflection is a Java API that lets me inspect and manipulate classes, fields, and methods at runtime — even private ones — without knowing them at compile time.
javaClass<?> clz = Class.forName("com.example.Employee");
Object obj = clz.getDeclaredConstructor().newInstance();

Field field = clz.getDeclaredField("name");
field.setAccessible(true);
field.set(obj, "Austin");

Method method = clz.getDeclaredMethod("getName");
method.invoke(obj);
This is how Spring works under the hood — @Autowired, dependency injection, annotation scanning all rely on reflection to wire things together at runtime. The downside is performance cost and it bypasses access control, so I'd keep it out of regular business logic.

c. HTTP Status Codes

200 — request succeeded, response body has the data
201 — POST succeeded, new resource created
202 — request accepted but processing hasn't finished yet, used for async operations
204 — succeeded but nothing to return, typical for DELETE
301 — resource permanently moved to a new URL
307 — temporary redirect, go to another URL just for this request, same HTTP method preserved
400 — bad request, client sent malformed or invalid data
401 — not authenticated, need to log in first
403 — authenticated but no permission to access this resource
404 — resource doesn't exist at this URL
500 — something blew up on the server, unhandled exception


d. What is HTTP
HTTP is the application-layer protocol that defines how clients and servers talk to each other. It's stateless — each request is completely independent, the server remembers nothing about previous requests. A request has a method, URL, headers, and optional body. A response has a status code, headers, and optional body. Since it's stateless, identity has to be carried explicitly in each request, which is why things like JWT tokens exist.

e. GET / POST / PUT / DELETE / PATCH
GET    /employees       - read, no body
POST   /employees       - create, has body
PUT    /employees/{id}  - full replace, has body
PATCH  /employees/{id}  - partial update, has body
DELETE /employees/{id}  - delete, no body
GET reads, POST creates, PUT fully replaces the whole resource, PATCH only updates the fields I send, DELETE removes. GET and DELETE carry no request body.

f. POST vs PATCH
POST creates a new resource — I send the full payload, server assigns the ID. Calling it twice creates two records, so not idempotent.
PATCH partially updates an existing resource — I only send the fields I want to change, everything else stays as-is.
javaPOST /employees          { "name": "Austin", "dept": "Engineering" }
PATCH /employees/1       { "dept": "Backend" }  // only dept changes

g. POST vs PUT
POST creates, server assigns the ID, not idempotent. PUT fully replaces a resource at a specific ID — if I omit a field, that field gets cleared. PUT is idempotent, calling it 10 times with the same body gives the same result every time.
javaPOST /employees          { "name": "Austin", "dept": "Engineering" }
PUT  /employees/1        { "name": "Austin", "dept": "Backend", "salary": 80000 }
// omit salary on PUT → salary becomes null
In practice I use POST to create and PATCH to update. PUT is less common because replacing an entire object is risky if I accidentally omit fields.

h. What is Idempotent, which methods are idempotent
Idempotent means calling the operation multiple times with the same input always produces the same result as calling it once. It matters for retry logic — if a network request times out and I retry, an idempotent operation is safe to repeat.
GET, PUT, DELETE are idempotent. POST and PATCH are not — POST creates a new record each time, PATCH can have side effects depending on current state.
GET    /employees/1   - always returns the same employee, safe to retry
DELETE /employees/1   - first call deletes, subsequent calls still 404, same end state
POST   /employees     - each call creates a new record, NOT safe to retry blindly

-----------------
Homework 8

a. TCP 3-Way Handshake
Before any data is sent, TCP establishes a connection through a 3-step process:
Client  →  SYN        →  Server   (I want to connect)
Client  ←  SYN-ACK    ←  Server   (OK, I'm ready)
Client  →  ACK        →  Server   (Got it, let's go)

SYN — client sends a synchronize packet to the server, saying it wants to open a connection
SYN-ACK — server acknowledges the request and signals it's ready
ACK — client confirms, connection is established, data can now flow

This exists because TCP is connection-oriented and needs both sides to agree before transmitting anything.

b. TCP vs UDP
TCP is connection-oriented — requires the 3-way handshake before sending, guarantees delivery and ordering, and retransmits lost packets. Slower because of all that overhead.
UDP just fires packets and doesn't care if they arrive. No handshake, no acknowledgment, no retransmit. Much faster and lower latency.
TCP → file transfer, HTTP, emails — correctness matters
UDP → video streaming, gaming, DNS — speed matters, some loss is fine

c. What is Tomcat
Tomcat is an open-source Java web server and servlet container maintained by Apache. It implements the Java Servlet and JSP specs, meaning it can receive HTTP requests, route them to the right servlet, and send back responses. In Spring Boot, Tomcat is embedded by default — no separate installation needed, it just starts up with the application.

d. Basic Components of Tomcat

Server — the top-level component, represents the entire Tomcat instance
Service — sits inside the server, connects a Connector to an Engine
Connector — listens on a port (default 8080) and handles incoming HTTP requests, translates them into a format the Engine understands
Engine — the actual request processing engine, takes the request from the Connector and figures out which Host and Servlet to route it to
Host — represents a virtual host, maps domain names to web applications
Context — represents a single web application deployed inside a Host

Server
 └── Service
      ├── Connector (port 8080)
      └── Engine
           └── Host
                └── Context (your app)

e. What is a Web Server
A web server is software that listens for HTTP requests and sends back responses. At its simplest it serves static files — HTML, CSS, images. When dynamic content is needed it forwards the request to an application server. Common examples are Nginx and Apache. Tomcat blurs the line a bit — it's technically a servlet container but also handles HTTP directly, so it's often referred to as a web server too.

f. What is 3-Tier Architecture
3-tier architecture splits an application into three separate layers:
Presentation Layer  →  the UI, what the user sees (browser, mobile app)
Business Logic Layer →  the backend, processes requests, applies rules (Spring Boot)
Data Layer          →  the database, stores and retrieves data (PostgreSQL, MySQL)
Each layer only talks to the layer directly next to it. The benefit is separation of concerns — I can swap out the database without touching the frontend, or redesign the UI without changing business logic. It also makes scaling easier since each tier can be scaled independently.

g. OSI Model — 7 Layers
The OSI model is a conceptual framework that breaks network communication into 7 layers. Each layer has a specific job and only interacts with the layers directly above and below it.
7  Application   → HTTP, FTP, DNS — what the user/app actually uses
6  Presentation  → encoding, encryption, compression (SSL/TLS lives here)
5  Session       → manages sessions, keeps connections open across requests
4  Transport     → TCP/UDP, end-to-end delivery, ports
3  Network       → IP addressing, routing between networks
2  Data Link     → MAC addresses, transfers data between devices on the same network
1  Physical      → the actual cable, wifi signal, raw bits
In practice the layers I deal with most as a backend developer are 4 (TCP/UDP), 3 (IP), and 7 (HTTP). The lower layers are handled by the OS and network hardware.
