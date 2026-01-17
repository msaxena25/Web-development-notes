# 🧩 Micro-Frontend Architecture

## 1️⃣ What Is Micro-Frontend?

> Micro-frontend is an architectural style where a large frontend application is **split into multiple independently developed, deployed, and owned frontend applications**, which are composed together into a single UI.

👉 Exactly like **microservices**, but for **frontend**.

---

## 2️⃣ Why Micro-Frontend Is Needed

### Problems with Monolithic Frontend

* Single large codebase
* Slow builds
* Risky deployments
* Multiple teams blocking each other
* Hard to scale teams

---

### What Micro-Frontend Solves

✔ Independent team ownership
✔ Independent deployment
✔ Technology flexibility
✔ Faster development

---

Each block is a **separate frontend app**.

---

## 4️⃣ Core Components of Micro-Frontend Architecture

### 1️⃣ Shell / Container App ⭐⭐⭐

* Hosts all micro-frontends
* Handles routing
* Shared layout (header/footer)

```
Shell App
 ├── MF-Home
 ├── MF-Cart
 ├── MF-Profile
```

---

### 2️⃣ Micro-Frontend Apps

* Independently built & deployed
* Own codebase & pipeline

---

### 3️⃣ Shared Libraries

* UI components
* Auth utilities
* Design system

---


## 6️⃣ Popular Micro-Frontend Patterns

### 1️⃣ Module Federation (Most Used) ⭐⭐⭐

* Host loads remote apps dynamically
* Shared dependencies

Pros:
✔ No iframe overhead
✔ Shared React/Angular

Cons:
❌ Version conflicts

---

### 2️⃣ iFrames

Pros:
✔ Strong isolation

Cons:
❌ Poor performance
❌ Hard communication

---

### 3️⃣ Web Components

Pros:
✔ Framework agnostic

Cons:
❌ Complex state sharing

---

## 7️⃣ Routing in Micro-Frontend

### Options:

* Shell manages routing
* Each MF manages its own sub-routes


## 8️⃣ State Management (Hardest Part)

### Approaches:

* Local state per MF
* Shared global store (Redux)
* Event-based communication

✔ Prefer **minimal shared state**

---

## 9️⃣ Authentication & Authorization

Handled by:

* Shell app
* Token shared with MFs

---

## 🔟 Deployment Strategy

* Each MF deployed independently
* CDN hosting
* Versioning support

---

## 1️⃣1️⃣ Performance Challenges

Problems:

* Increased JS size
* Multiple bundles
* Runtime loading delay

Solutions:

* Lazy loading
* CDN
* Shared dependencies
* Preloading critical MF

---

# ⚙️ Module Federation – Internal Working

**Module Federation** is the *heart* of modern Micro-Frontend architecture.

> Module Federation is a Webpack feature that allows **one JavaScript application to dynamically load code from another application at runtime**, while sharing dependencies.

---

## 2️⃣ Key Roles in Module Federation

### 🔹 Host (Shell App)

* The main application
* Loads remote modules

### 🔹 Remote (Micro-Frontend App)

* Exposes modules/components
* Built & deployed independently

---

## 3️⃣ The Magic File: `remoteEntry.js`

Every **remote app** builds a special file:

```
remoteEntry.js
```

This file contains:

* Metadata about exposed modules
* How to load them
* Shared dependency rules
---

## 5️⃣ Runtime Flow (Step-by-Step Internals)

### Step 1️⃣ Browser loads Host (Shell)

* Host JS bundle loads
* App starts running

---

### Step 3️⃣ When user navigate to Cart, Host downloads `remoteEntry.js`

At runtime, browser fetches:

```
https://cdn.cart-app.com/remoteEntry.js
```

This is a **dynamic network call**.

---

## 6️⃣ How Sharing Actually Works (Under the Hood)

Webpack creates a **shared scope**:

```
__webpack_share_scopes__.default
```

This acts like:

* A global dependency registry
* Avoids duplicate libraries

--- 

## 8️⃣ Lazy Loading + Code Splitting

Module Federation works with:

* Lazy loading
* Chunk splitting

Only needed MF is loaded:
✔ Faster initial load

---

## 9️⃣ Failure Scenarios (Real World)

### If remoteEntry.js fails:

* Host catches error
* Show fallback UI
* Log metrics

App does NOT crash completely.

---