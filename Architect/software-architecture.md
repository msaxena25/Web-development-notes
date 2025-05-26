## **One-tier**, **Two-tier**, and **Three-tier**

In software architecture, **one-tier**, **two-tier**, and **three-tier** refer to the way different components of an application (like the user interface, business logic, and data storage) are separated and organized. These “tiers” (or layers) define how responsibilities are distributed across different parts of the system.

---

### **1. One-Tier Architecture (Monolithic Architecture)**

* **Definition**: All components (UI, business logic, data access) are in a single layer or program.
* **Example**: A Microsoft Excel spreadsheet with embedded macros, or a standalone desktop application.
* **Use case**: Small, simple applications like calculators or local tools.
* **Pros**: Simple, fast to develop.
* **Cons**: Difficult to scale or maintain; not suitable for large systems.

---

### **2. Two-Tier Architecture (Client-Server Architecture)**

* **Definition**: The application is divided into two layers:

  * **Client Tier**: User interface (UI) and some business logic.
  * **Server Tier**: Database server, and sometimes additional business logic.
* **Example**: A desktop application (like MS Access) connecting directly to a SQL Server database.
* **Use case**: Enterprise desktop apps with limited users.
* **Pros**: Easier to manage than monolithic; better data security than one-tier.
* **Cons**: Tight coupling between client and server; not scalable for large user bases.

---

### **3. Three-Tier Architecture (Presentation, Logic, Data Layers)**

* **Definition**: The application is divided into three layers:

  1. **Presentation Tier**: UI – what the user interacts with.
  2. **Business Logic Tier**: Handles processing, rules, calculations.
  3. **Data Tier**: Database or storage.
* **Example**: A web application with Angular (frontend), Node.js or .NET (backend/API), and MySQL (database).
* **Use case**: Modern web and enterprise applications.
* **Pros**: Scalable, maintainable, better security and separation of concerns.
* **Cons**: Slightly more complex to develop and manage.

---

### Summary Table:

| Tier       | Layers/Components            | Example            | Scalability            | Use Case                       |              |                           |
| ---------- | ---------------------------- | ------------------ | ---------------------- | ------------------------------ | ------------ | ------------------------- |
| One-Tier   | UI + Logic + Data in one app | Desktop calculator | Low                    | Small apps                     |              |                           |
| Two-Tier   | UI + Logic                   | Data (Server)      | MS Access + SQL Server | Medium                         | Desktop apps |                           |
| Three-Tier | UI                           | Logic (API)        | Data (DB)              | Angular + Node.js + PostgreSQL | High         | Web apps, enterprise apps |

----------------------

## **4-tier architecture**


Yes, there is a **4-tier architecture** as well, though it's less commonly discussed in basic software architecture. It typically adds another layer to the standard 3-tier model to further separate responsibilities or enhance scalability, security, or deployment flexibility.

---

### ✅ **4-Tier Architecture** (Extended Three-Tier)

It usually includes:

1. **Presentation Tier** – The user interface (e.g., web browser or mobile app).
2. **Application Tier** – Handles business logic (e.g., API services or backend server).
3. **Data Tier** – The database or persistent storage.
4. **Integration or Service Tier** – Often handles communication with:

   * Third-party services (e.g., payment gateways, external APIs),
   * Enterprise services (like messaging queues, microservices),
   * Middleware (e.g., enterprise service buses).
---

### 🧱 Example: E-commerce Web Application (4-Tier)

| Tier                 | Example Technology / Function           |
| -------------------- | --------------------------------------- |
| 1. Presentation      | React.js / Angular frontend             |
| 2. Application Logic | Node.js / Java / .NET API               |
| 3. Data              | MySQL, MongoDB                          |
| 4. Integration Tier  | Stripe API, AWS S3, Kafka, Redis, Auth0 |

---

### ✅ Advantages:

* Better **modularity**, **scalability**, and **security**.
* Makes large, distributed systems easier to manage.
* Suited for **microservices** and **cloud-native** applications.

---

## **Software architecture terminologies**

Absolutely — here’s a **refined and verified list** of commonly used software architecture terminologies, each with a **one-line explanation** for quick understanding:

---

### 🏛️ **Architectural Layers & Tiers**

* **Tier**: A **physical separation** of software components (e.g., client, server).
* **Layer**: A **logical separation** of responsibilities within the code (e.g., presentation, business logic).
* **Monolithic**: A **single unified codebase** where all features and logic are tightly coupled.
* **Microservices**: A design that breaks applications into **small, independent services** communicating via APIs.
* **Service-Oriented Architecture (SOA)**: Uses **loosely coupled, reusable services** that communicate over a network.
* **Client-Server**: A model where a **client sends requests** and a **server processes and responds**.

---

### 🧱 **Core Components**

* **Frontend**: The **user interface** of an application, usually built with HTML/CSS/JS.
* **Backend**: The **server-side** part handling logic, data processing, and storage.
* **Middleware**: Software that **connects different parts** of an application, often managing communication or data flow.
* **Database**: A structured system to **store, retrieve, and manage data**.
* **API (Application Programming Interface)**: A **set of rules** that allows communication between different software components.

---

### 🧩 **Design Patterns**

* **MVC (Model-View-Controller)**: Separates an app into **model (data), view (UI), and controller (logic)**.
* **MVVM (Model-View-ViewModel)**: Similar to MVC but the **ViewModel binds the UI and data**, popular in frontend frameworks.
* **Repository Pattern**: **Centralizes data access logic** to improve separation of concerns.
* **Factory Pattern**: Provides an interface for **creating objects without exposing instantiation logic**.
* **Singleton Pattern**: Ensures that a **class has only one instance** throughout the application.

---

### ☁️ **Architecture Styles**

* **RESTful Architecture**: Uses HTTP verbs to **access resources** in a stateless manner.
* **Event-Driven Architecture**: System components **react to events**, often asynchronously.
* **Layered Architecture**: Organizes code in **layers like presentation, logic, and data**, allowing separation of concerns.
* **Hexagonal Architecture (Ports and Adapters)**: Decouples the core logic from external systems via **ports (interfaces) and adapters (implementations)**.
* **Clean Architecture**: Focuses on keeping the **business logic independent** from UI, database, or frameworks.

---

### 📦 **Deployment & Scaling**

* **Load Balancer**: Distributes incoming requests **across multiple servers** for high availability.
* **Horizontal Scaling**: Adds **more machines or nodes** to handle increased load.
* **Vertical Scaling**: Increases the **resources of a single server** (CPU, RAM) to improve capacity.
* **Containerization**: Packages an app and its dependencies into a **container (e.g., Docker)** for consistent deployment.
* **CI/CD (Continuous Integration/Deployment)**: Automates **building, testing, and deploying code** frequently and reliably.

---

### 🔐 **Security & Communication**

* **Authentication**: The process of **verifying a user's identity** (e.g., login).
* **Authorization**: Determines **what resources a user can access** once authenticated.
* **SSL/TLS**: Protocols that **secure communication** over networks using encryption.
* **Firewall**: Filters **incoming and outgoing network traffic** to block threats.
* **OAuth/JWT**: **Standards for token-based authentication and authorization**, often used in APIs.

---

### 📚 **Documentation & Modeling**

* **UML (Unified Modeling Language)**: A **visual language** for designing and modeling software systems.
* **ERD (Entity-Relationship Diagram)**: Graphically shows **entities and relationships** in a database.
* **Data Flow Diagram (DFD)**: Describes **how data moves** through a system.
* **Architecture Decision Records (ADR)**: A **documented log of architectural choices** and their reasoning.

---

### 🧠 **Quality Attributes**

* **Scalability**: The ability to **handle growing workload** efficiently.
* **Maintainability**: The ease with which a system can be **modified or extended**.
* **Reliability**: The system's ability to **function correctly under expected conditions**.
* **Availability**: The **readiness of a system** to be used when needed.
* **Performance**: How quickly and efficiently the system **responds to inputs** or handles operations.
* **Security**: Protects the system and data from **unauthorized access or attacks**.

---

## 🧩 What is **Sharding**?

**Sharding** is a **database architecture pattern** in which **data is split into smaller, more manageable pieces** called **shards**, and each shard is stored on a **separate database server or node**. It is used to **improve performance, scalability, and manageability** of large datasets.

---

### 🧠 Why Use Sharding?

* To **distribute the load** across multiple servers.
* To **speed up queries** by reducing the amount of data scanned.
* To **scale horizontally** (adding more machines instead of upgrading one).

---

### 📦 Example:

If a user database has 10 million users, sharding might divide it like:

* **Shard 1**: Users with IDs 1–3 million
* **Shard 2**: Users with IDs 3–6 million
* **Shard 3**: Users with IDs 6–10 million

Each shard could live on a separate server.

---

### 🧭 Types of Sharding

| Sharding Type                | Description                                                                                 | Example                                                  |
| ---------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Horizontal Sharding**      | Rows (records) are divided across different databases or tables. Most common type.          | Users with ID 1–1000 on Server A, 1001–2000 on Server B. |
| **Vertical Sharding**        | Columns (fields) are split across different databases.                                      | User profile data on one server, login data on another.  |
| **Directory-Based Sharding** | A lookup service determines which shard holds which data.                                   | A central service routes requests to correct shard.      |
| **Hash-Based Sharding**      | A hash function is used on a key (like user ID) to decide which shard to store the data in. | Hash(user\_id) % number\_of\_shards                      |
| **Range-Based Sharding**     | Data is divided by ranges of values (e.g., dates, user IDs).                                | Dates Jan–Jun on Shard 1, Jul–Dec on Shard 2.            |
| **Geo-Sharding**             | Data is partitioned based on geographic location.                                           | Users from Asia on one shard, Europe on another.         |

---

### ✅ Advantages

* **Improves performance** by reducing data per server.
* **Enables horizontal scaling**.
* **Reduces single-point bottlenecks** in databases.

---

### ⚠️ Challenges

* **Complex queries** across shards are hard to manage.
* **Rebalancing shards** when data grows unevenly.
* **Increased complexity** in application logic and failover.

---

### 🔁 Real-World Examples

* **MongoDB** supports built-in sharding.
* **MySQL** can use manual sharding techniques.
* **Cassandra** uses partitioning (similar to sharding) with a hash-based mechanism.
* **Twitter** uses sharding to split tweets and user data for performance.

---
