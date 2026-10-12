# Module 8: Serverless Computing & Event-Driven Architecture (EDA)

> **Part of the [Ultimate Cloud Computing Handbook](../README.md)** by [CloudKnowledge.in](https://cloudknowledge.in)
> Author: **Shivam Tiwari** (Senior IAM & Cloud Architect)

---

## Module 8: Serverless Computing & Event-Driven Architecture (EDA)

        

          Serverless computing liberates engineering teams from server provisioning, patch management, capacity planning, and scaling policies. Under the serverless contract, the consumer pays exclusively for executed compute time measured in milliseconds.
        

        
### 8.1 Function as a Service (FaaS) Mechanics: Cold Starts & MicroVMs

        

          When an event triggers an AWS Lambda function, the provider must allocate a sandboxed execution environment. If no idle instance exists, a Cold Start occurs:
        

        
          - The cloud control plane downloads the customer function code archive or container image layer.

          - A microVM (such as an AWS Firecracker jail) is provisioned and booted.

          - The language runtime (Node.js, Python, JVM, Go) is initialized.

          - Global static code outside the function handler is executed (establishing database connections, reading configuration).

        
        

          While interpreted runtimes (Node.js, Python, Go) experience cold starts of 100ms–250ms, heavy JVM or .NET applications can suffer multi-second cold delays. Mitigations: Utilize Provisioned Concurrency, write lean handlers, adopt compile-time GraalVM native binaries, or migrate to Google Cloud Run which pools warm micro-containers dynamically.
        

        
### 8.2 Event-Driven Architecture (EDA) Comparison

        
          
| Technology | Primary Architecture Paradigm | Throughput & Latency | Ordering Guarantees | Consumer Model |
| --- | --- | --- | --- | --- |
| Amazon SQS | Distributed Message Queue | Virtually unlimited; ~10ms | Standard: Best-effort; FIFO: Strict | Pull / Polling (Worker fleet) |
| Amazon SNS | Publish/Subscribe Topic Fanout | High throughput; sub-second | No guaranteed global ordering | Push (HTTP, SQS, Email, Lambda) |
| AWS EventBridge | Serverless Event Bus (SaaS routing) | Moderate; Schema Registry built-in | No strict ordering guarantees | Rule-based routing to 20+ targets |
| Apache Kafka / MSK | Distributed Append-Only Partitioned Log | Millions of events/sec; <5ms | Strictly ordered per partition | Consumer groups reading offset index |

        

        
### 8.3 The Saga Pattern for Distributed Transactions

        

          In a microservices architecture, a single business transaction (e.g., "Place E-Commerce Order") spans multiple independent databases: the Order Service, Inventory Service, and Payment Gateway. You cannot execute a standard two-phase commit (2PC) ACID transaction across distributed network boundaries without introducing severe latency and locking bottlenecks.
        

        

          Instead, architects implement the Saga Pattern: a sequence of local transactions where each service updates its local database and publishes an event. If an intermediate step fails (e.g., credit card declined), the Saga executes a sequence of Compensating Transactions that undo previous steps in reverse order (e.g., releasing reserved inventory).
        

        

          Sagas are implemented via two paradigms:
        

        
          - Choreography: Decentralized. Each service listens for events and decides what to do next without a central coordinator. Best for simple workflows with 2-4 services.

          - Orchestration: Centralized. A state machine orchestrator (such as AWS Step Functions) explicitly tells each service when to execute and manages error branches. Essential for complex multi-step enterprise workflows.

---

### 🔗 Essential Resources
- 🌐 **Main Platform:** [CloudKnowledge.in](https://cloudknowledge.in)
- 🖨️ **Print & Desk Cheat Sheets:** [PrintKrlo.in](https://printkrlo.in) (Doorstep delivery for diagrams & notes)
- 📺 **Video Labs & Walkthroughs:** [YouTube: @Cloud-Knowledge](https://youtube.com/@Cloud-Knowledge)
- 🔙 **[Return to Table of Contents](../README.md)**
