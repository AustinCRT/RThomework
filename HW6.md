[https://austin-rt-dev.s3.us-east-2.amazonaws.com/hw6.mov](https://austin-rt-dev.s3.us-east-2.amazonaws.com/hw6.mov)

1\. Client \- Server Model  
"So the client-server model is basically how the internet works. The client is anything on the user's side — like a browser or mobile app. The server is the backend that processes requests. The client sends an HTTP request, the server handles the logic, hits the database if needed, and sends back a response. They're completely separate — the client doesn't know how the server works internally, it just knows the URL."

2\. Application Service  
"An application service is where the actual business logic lives. It sits between the controller and the database. So the controller receives the request, passes it to the service, and the service decides what to do — like calculating a price, filtering data, or calling the repository to query the database. It's the brain of the backend."

3\. HTTP Request / Response  
"An HTTP request has three parts — the request line, which contains the method, URL, and protocol version. Then the headers, which carry metadata like content type and user agent. And the body, which carries the actual data you're sending.  
The response is similar — it has a status line with the status code like 200 or 404, response headers, and a response body which is the actual content returned, usually JSON in modern APIs."

4\. Horizontal vs Vertical Scaling  
"Vertical scaling means making one server stronger — adding more RAM or CPU. But there's a physical limit, and you need downtime to do it.  
Horizontal scaling means adding more server instances. No downtime, theoretically unlimited. The tradeoff is your app needs to be stateless, and you need a load balancer to distribute traffic.  
Modern systems prefer horizontal scaling because it's more flexible and cost-effective."

5\. Load Balancer  
"A load balancer sits in front of your server cluster and distributes incoming requests evenly across all instances. So if you have three instances of your product service, the load balancer makes sure each one gets roughly the same amount of traffic. This minimizes latency and prevents any single instance from getting overwhelmed. It's often built into the API Gateway."

6\. Microservice & Microfrontend  
"Microservice architecture means splitting your application into small independent services based on business scope. So instead of one giant jar file, you have a product service, a payment service, a shipment service — each running independently with its own database. If one goes down, the others keep running.  
Microfrontend is the same idea but for the UI. Different teams own different parts of the frontend, built and deployed independently."

7\. SQL vs NoSQL Database  
"SQL databases store data in structured tables with fixed schemas — think rows and columns like Excel. They're great for financial data or anything needing strong consistency. Examples are MySQL and PostgreSQL.  
NoSQL databases are more flexible — data can be stored as JSON documents, key-value pairs, etc. No fixed schema. They scale horizontally really well. MongoDB and Redis are examples.  
Use SQL when data integrity is critical. Use NoSQL when you need flexibility or massive scale."

8\. API Gateway  
"The API Gateway is the single entry point for all client requests. Every request hits the gateway first, and it handles routing to the correct microservice, authentication, and load balancing. So the client never talks directly to individual services — it only talks to the gateway.  
Think of it like a hotel concierge — guests don't go directly to the kitchen or housekeeping, they go through the concierge who directs them."

9\. Message Queue  
"A message queue allows services to communicate asynchronously. Instead of Service A calling Service B directly, it drops a message into a queue, and Service B picks it up when it's ready.  
For example, after payment is processed, instead of directly calling the shipment service, we drop a message into the queue. Shipment picks it up and starts packing. This decouples the services, and if shipment is temporarily down, the message just waits — no data loss.  
Kafka and AWS SQS are common examples."

10\. Log & Monitor  
"Logging is your application writing down everything that happens — requests, errors, transactions. When something breaks, logs tell you exactly what happened and when.  
Monitoring is watching your system in real time — CPU usage, memory, error rate, latency. Tools like Datadog or CloudWatch give you dashboards and send alerts when something spikes.  
So logs tell you what happened, monitoring tells you what's happening right now."

11\. Deployment with AWS / Azure / GCP  
"These are cloud platforms so you don't need to manage physical servers. On AWS for example — EC2 gives you virtual machines to run your app, S3 is object storage for files and videos, RDS is managed database, SQS is message queue, and API Gateway manages your API traffic.  
A typical flow is — push code to GitHub, CI/CD pipeline builds a Docker image, deploys it to EC2 or EKS, and API Gateway routes traffic to it."

12\. Security: Authentication vs Authorization  
"Authentication is verifying who you are — like logging in with username and password, or a JWT token. It answers the question 'are you who you claim to be?'  
Authorization is about what you're allowed to do after you're logged in. A regular user can only see their own orders. An admin can see everyone's orders.  
Simple way to remember — authentication is the login check, authorization is the permission check. They always happen in that order."