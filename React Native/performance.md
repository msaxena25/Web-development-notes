# 🚀 Why React Native and React (Web) are *Faster or Perceived Faster?*

### ✅ 1. **Virtual DOM (React Web) and Shadow Tree (React Native)**

* React uses a **Virtual DOM** (Web) or **Shadow Tree** (Native) to **minimize real DOM/native UI updates**.
* Instead of re-rendering the whole UI, React calculates a **diff** and updates only what's necessary.

📈 This results in **fewer expensive UI operations** compared to traditional frameworks that manipulate the DOM directly (like jQuery or even AngularJS 1.x).

---

### ✅ 2. **Declarative Programming Model**

* Both React and React Native use **declarative UI**:

  ```jsx
  <Button title="Click me" onPress={handleClick} />
  ```

* You describe what the UI should look like, and React figures out **how to update it** efficiently.

⚡ This reduces manual state tracking, reflows, and complexity.

---

### ✅ 3. **Efficient Reconciliation**

* React’s **reconciliation algorithm** (Fiber) prioritizes and batches updates intelligently.
* It breaks rendering work into chunks and spreads them over multiple frames, keeping the app responsive.

🧠 React Fiber (introduced in React 16) allows:

* **Interruptible rendering**
* **Prioritization of updates**
* **Smoother animations**

---

### ✅ 4. **Native Rendering (React Native)**

* React Native maps components directly to **native UI widgets** (`<View>` → `UIView` or `ViewGroup`) — not WebViews.

🏃 This means performance is close to native apps, unlike hybrid solutions like Cordova or Ionic (which run inside a browser shell).

---

### ✅ 5. **Component Reusability & Rehydration**

* Components in React are:

  * **Reusable**
  * **Composable**
  * **Easily cached or lazy-loaded**

⏱️ This reduces startup time and makes incremental updates faster.

---

### ✅ 6. **Optimized Development Workflow**

* Tools like **Fast Refresh**, **Hot Reloading**, **DevTools**, etc., allow for rapid iteration and debugging.
* Developers write less code to achieve the same behavior → fewer bugs → fewer runtime issues.

---

### ✅ 7. **Static Bundling & Code Splitting (Web)**

* React Web (via Webpack, Vite, etc.) supports **tree shaking**, **lazy loading**, and **code splitting**.
* Only the required JavaScript is sent to the browser, improving initial load time.


### ✅ 8.  **One-Way Data Flow:**
* Data flows from parent to child components, making data changes predictable and easier to debug, which indirectly contributes to maintaining performance.

### ✅ 9.  **Optimizations (Manual & Automatic):**
`React.memo` (for functional components) / `PureComponent` (for class components):** These help prevent unnecessary re-renders of components if their props or state haven't actually changed.
* **Concurrent Mode (New Architecture):** While still evolving, React's new architecture aims to make UI updates interruptible and render multiple states simultaneously, improving responsiveness, especially in data-heavy applications.
* **Code Splitting & Lazy Loading:** Modern bundlers (like Webpack or Vite) easily integrate with React to split your code into smaller chunks, loading them only when needed. This reduces the initial load time of your application.
* **Server-Side Rendering (SSR) & Static Site Generation (SSG):** Frameworks like Next.js built on React can pre-render content on the server, sending fully formed HTML to the browser. This results in faster initial page loads and better SEO, as the browser doesn't have to wait for JavaScript to render the content.

### ✅ 10.  **Native UI Components:**
* **No WebViews:** Unlike older "hybrid" frameworks (like Cordova/PhoneGap) that render web content inside a WebView, React Native translates your React components into actual, platform-specific **native UI components**. A `View` in React Native becomes a `UIView` on iOS and an `android.view.View` on Android.
* **Native Performance:** Because it renders native components, React Native apps look, feel, and perform much closer to truly native apps built with Swift/Kotlin/Java. They leverage the device's GPU and native rendering capabilities directly, which is inherently faster and smoother than rendering web technologies in a WebView.


---

## 🔬 Performance Is Also About Use Case

React and React Native **are fast**, but not always **the fastest** for:

| Use Case                   | React / RN Performance    |
| -------------------------- | ------------------------- |
| Native 3D gaming (Unity)   | ❌ Not suitable            |
| Super-light apps (Vanilla) | 🟡 May be overkill        |
| Complex animations         | 🟢 With Reanimated/Fabric |
| Web dashboards             | ✅ Excellent               |
| Offline mobile apps        | ✅ Excellent               |

---

## 🆚 Compared to Other Frameworks

| Framework         | Runtime Speed | UI Efficiency      | Notes                              |
| ----------------- | ------------- | ------------------ | ---------------------------------- |
| **React**         | ✅ Fast        | ✅ Smart UI updates | Balanced performance and DX        |
| **Angular**       | 🟡 Moderate   | 🟡 Heavier         | More boilerplate, zone.js overhead |
| **Vue.js**        | ✅ Very fast   | ✅ Efficient        | Similar to React, slightly smaller |
| **Flutter**       | ✅ Very fast   | 🟢 Canvas UI       | Draws UI with Skia; very smooth    |
| **Cordova/Ionic** | ❌ Slow        | ❌ WebView-based    | Poor for high-performance apps     |

---

## 📘 What is Virtual DOM?

Instead of directly manipulating the browser's Document Object Model (DOM) after every change, React creates a lightweight in-memory representation of the DOM called the Virtual DOM.

It’s a **plain JavaScript object tree** that mirrors the structure of your UI.

---

## 🧠 Complete Working of Virtual DOM (Step-by-Step)

### 1️⃣ Initial Render (Mount Phase)

1. React app starts → component tree is created.
2. React **renders** the entire UI as a Virtual DOM (a JS object).
3. React **translates** this Virtual DOM to actual browser DOM and inserts it into the `<div id="root">`.
4. Browser paints the UI.

✅ First render complete.

---

### 2️⃣ State or Props Change (Triggering Update)

1. Some interaction (click, API result, etc.) changes `state` or `props`.
2. React **re-invokes the affected component** (and children).
3. A **new Virtual DOM** subtree is created from the updated render.

🆕 Now there are **two Virtual DOMs**:

* Old one (before update)
* New one (after update)

---

### 3️⃣ Diffing Algorithm (Reconciliation)

1. React compares the **old and new Virtual DOMs** using a **diffing algorithm**.
2. It checks:

   * **Node type** (div vs span)
   * **Props and attributes**
   * **Children changes**
3. Only the **minimum number of changes** (called “patches”) are calculated.

🧠 Example: If a button label changes from "Login" to "Logout", React doesn't re-render the whole button — just updates the `textNode`.

---

### 4️⃣ DOM Patching (Real DOM Update)

1. Based on the diff, React:

   * Applies only the **necessary changes** to the real DOM.
   * **Avoids full re-renders**
   * Batches updates if possible
2. This real DOM manipulation is handled **efficiently and asynchronously** (sometimes with requestAnimationFrame).

---

### 5️⃣ Render Phase vs Commit Phase (React Internals)

React breaks this into two phases:

| Phase      | What Happens                         |
| ---------- | ------------------------------------ |
| **Render** | Creates new Virtual DOM, diffs trees |
| **Commit** | Applies updates to real DOM          |

---

### 6️⃣ Fiber (Advanced Scheduling in React 16+)

React Fiber adds scheduling and prioritization to the VDOM:

* Can **pause and resume** rendering (useful for large updates)
* Allows for **time-slicing**, so high-priority tasks (like user input) don't get blocked

---

### 7️⃣ Optional: Batching and Memoization

React further optimizes rendering:

* **Batch multiple setStates** into one render
* Use **memoization** (`React.memo`, `useMemo`, etc.) to skip re-rendering unchanged components

---

### 8️⃣ Final UI Update

After committing updates:

* Browser reflects the new UI
* User sees changes on screen
* React continues watching for further state/props updates


---

# 🚀 What Is React Fiber?

**React Fiber** is the **complete reimplementation of React’s core rendering engine**, built to support:

* **Incremental rendering**
* **Prioritized updates**
* **Pausing and resuming rendering**
* **Concurrency features**

> In short: **Fiber is the algorithm React uses to reconcile the Virtual DOM and update the real DOM efficiently.**

---

## 🧠 Why Was Fiber Introduced?

Before Fiber (React 15 and earlier), rendering was:

* **Synchronous**
* **Blocking**
* Not interruptible (once rendering started, React had to finish)

This became a problem for:

* Large component trees
* Slow devices
* Animations and gestures needing high responsiveness

So Fiber introduced **asynchronous, interruptible rendering**.

---

## 🧩 How React Fiber Works – Step-by-Step

Here’s the **complete working of Fiber**, broken down:

---

### 1️⃣ React creates **Fiber Nodes** instead of simple VDOM nodes

Each Fiber Node is a **JS object** that represents a component with extra metadata like:

* `type` (Component type)
* `props`
* `state`
* `effectTag` (type of update)
* `return` (parent fiber)
* `child` (first child)
* `sibling` (next sibling)
* `alternate` (previous version of the fiber for diffing)

> So, the entire component tree becomes a **linked list of Fiber nodes**, instead of just a tree.

---

### 2️⃣ React schedules updates as **units of work**

Each update (like `setState`) creates a **unit of work**.

React schedules these units and processes them **incrementally**, so it can:

* Pause work
* Resume work
* Prioritize user-visible updates (e.g., button click over animation)

This is called **Cooperative Scheduling**.

---

### 3️⃣ Work Loop & Scheduler

React runs a loop (called **Work Loop**) to perform updates.

It checks:

* Do we have time left in this frame?

  * ✅ Yes → continue rendering fiber nodes
  * ❌ No → pause rendering and resume in next frame

> This loop is powered by browser APIs like `requestIdleCallback` or `MessageChannel` (or even `Scheduler` internally).

---

### 4️⃣ Two Phases in Fiber

| Phase            | What Happens                                         |
| ---------------- | ---------------------------------------------------- |
| **Render Phase** | Fiber tree is **built and diffed**, no DOM mutations |
| **Commit Phase** | Actual **DOM updates** happen here                   |

**Render Phase is interruptible**; Commit Phase is not.

---

### 5️⃣ Priority Levels (Lanes)

Fiber introduced **priority lanes** (in React 18) to manage updates more intelligently.

Examples:

* User input → High priority
* Data fetching → Low priority
* Transitions → Medium priority

This enables **Concurrent Features** like `startTransition()`.

---

## 📅 React Version History

| Version  | Feature                     |
| -------- | --------------------------- |
| 15.x     | Stack Reconciler (sync)     |
| **16.x** | ⚡ React Fiber introduced    |
| 18.x     | Concurrent Features + Lanes |

---

## ⚙️ What is the Work Loop in React?

The **Work Loop** is the **engine that drives the Fiber update process**.

Think of it as:

> "A while loop that processes units of work (fiber nodes) until there's no more time, or work is finished."

---

### 🧱 What is a "Unit of Work"?

Each **fiber node** (component) is treated as a **unit of work**. React processes one node (unit) at a time.

Why?

* So React can **pause** after each unit
* Avoid blocking the main thread (UI stays responsive)

---

## 🧠 How the Work Loop Works (Step-by-Step)

### 🌀 1. An update is triggered (e.g. `setState`)

* React schedules a render
* It pushes the root fiber onto a **work queue**

---

### 🧱 2. React starts the Work Loop

```js
while (nextUnitOfWork && shouldYield() === false) {
  nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
}
```

React:

* Picks a fiber node
* Runs `performUnitOfWork()` on it
* Moves to its child or sibling
* Checks if **time has run out** (`shouldYield()`)

---

### ⏳ 3. If time is over → Pause work

React yields back control to the browser:

* Browser does animations, input handling, etc.
* React continues later from the **nextUnitOfWork**

✅ This is **cooperative multitasking**.

---

### ✅ 4. Once all work is done → Commit Phase

When the full fiber tree is ready:

* React applies real DOM updates
* Runs effects (like `useEffect`)
* Browser paints the changes

This phase is **not interruptible**.

---

## 🧠 What is the Scheduler?

The **Scheduler** is the part of React that:

> Manages **when** and **which** work should be performed — based on **priority** and **time slicing**.

---

### 📦 What Scheduler Does:

* Assigns **priority levels** to updates (high, normal, low)
* Uses `requestIdleCallback`, `MessageChannel`, or custom timers to:

  * Break up work into chunks
  * Yield when time runs out
  * Reschedule remaining work later
* Ensures high-priority work (like input) isn’t delayed by low-priority tasks

---

## 🪜 Priority Levels (Lanes)

React introduced **Lanes** in React 18 — a system to track update priorities.

Examples:

| Priority         | Usage Example                          |
| ---------------- | -------------------------------------- |
| `SyncLane`       | Clicks, input events                   |
| `DefaultLane`    | Normal updates                         |
| `TransitionLane` | Deferred transitions (startTransition) |
| `IdleLane`       | Background, non-urgent work            |

---

## 🔁 Interaction Between Work Loop & Scheduler

| Work Loop                          | Scheduler                           |
| ---------------------------------- | ----------------------------------- |
| Executes fiber nodes (units)       | Decides which updates to prioritize |
| Can pause between units            | Reschedules work if time is short   |
| Does the actual rendering work     | Coordinates when rendering happens  |
| Uses `shouldYield()` to check time | Uses `getCurrentTime()` and lanes   |

---

## 🧪 Visual Summary

```
[setState] → [Fiber Tree update] 
          ↓
    [Scheduler prioritizes it]
          ↓
    [Work Loop begins]
     ↳ [performUnitOfWork()]
     ↳ [shouldYield()?]
     ↳ [pause/resume]
          ↓
    [Commit Phase (DOM update)]
          ↓
      [Browser repaint]
```

---

# Virtual DOM is not in React native

Excellent question! You're right — **React Native doesn't use the browser DOM**, so the **Virtual DOM (as used in React for web)** isn't applicable in the same way. However, **React Native still uses a similar concept to manage UI updates efficiently.**

---

## ✅ So What Happens Instead of Virtual DOM in React Native?

### 🔁 1. **React Native Uses a "Virtual Tree" of UI Elements**

* Like React DOM, React Native builds an **in-memory tree of UI elements**.
* These elements are not HTML tags, but **React Native components** (e.g., `<View>`, `<Text>`, etc.).
* This tree is compared with the previous one (diffed) to calculate minimal updates.

---

## 🔧 How UI Is Updated in React Native

1. **JavaScript builds a virtual component tree** using JSX.
2. React (via **React Fiber**) calculates the diff when a state/prop change occurs.
3. Instead of applying updates to the DOM, it generates a **set of instructions (called a "shadow tree diff")**.
4. These instructions are sent over the **Bridge** (in old architecture) or via **JSI/TurboModules** (in new architecture).
5. Native modules receive the diff and update native views (`UIView` in iOS or `View` in Android).

---

## 🧱 So What Replaces the DOM?

React Native components map to:

* `UIView` (iOS)
* `View` (Android)
* Not HTML — no CSS, no document structure

Instead of the DOM, React Native talks to **native UI managers** that render real native components.

---

## ✅ What is the "Shadow Tree" in React Native?

In **React Native**, the **shadow tree** refers to:

> A lightweight, internal tree structure that mimics the layout of the native UI components and is used to **compute layout** and **manage updates** before applying them to actual native views.

---

## ❌ Not the Same as Web Shadow DOM

| Feature       | Shadow Tree (React Native)                  | Shadow DOM (Web)                                |
| ------------- | ------------------------------------------- | ----------------------------------------------- |
| Purpose       | Internal layout & rendering abstraction     | Encapsulation for web components                |
| Visibility    | Invisible to devs; used internally          | Can be accessed via browser devtools            |
| Tech          | Built using React’s fiber + Yoga            | Built into the browser (via Web APIs)           |
| Native Output | Converts to native views (`UIView`, `View`) | Still uses browser DOM elements (`<div>`, etc.) |

---

## 🔧 How the Shadow Tree Works in React Native

1. **JS constructs a component tree** (e.g., `<View>`, `<Text>`).
2. React Fiber processes updates and creates a **shadow tree**.
3. Yoga layout engine runs on shadow tree to calculate layout (`x`, `y`, `width`, `height`).
4. Changes (diffs) in the shadow tree are turned into **native UI commands**.
5. These are sent to native UI threads (via Bridge, JSI, or Fabric).

---

## ❓ Are the **Shadow Tree** and **Virtual Tree** the same in React Native?

### 👉 **Short Answer:**

**No, they are not the same.**
They serve **different purposes**, though they are closely related and part of the same update/rendering pipeline.

---

## ✅ What is a **Virtual Tree** (Virtual DOM in React Native context)?

* This is the **React component tree** you build using JSX.
* Managed by **React Fiber**.
* It holds your components (`<View>`, `<Text>`, etc.) and tracks state, props, hooks.
* It's purely in **JavaScript** — no layout or rendering yet.

> Think of this as the **"logic and structure"** of your UI.

---

## ✅ What is the **Shadow Tree**?

* This is an internal structure used by React Native for **layout calculation**.
* It is built after the Virtual Tree is processed.
* Each node in the shadow tree corresponds to a **native component** and holds **layout data** (`x`, `y`, width, height).
* Managed by the **Yoga layout engine**.
* It’s also in memory, but specifically for **computing layout and native view updates**.

> Think of this as the **"layout and sizing layer"** of your UI before drawing.

---

## 🔄 Relationship Between Virtual Tree and Shadow Tree

1. You write JSX → builds the **Virtual Tree**.
2. React Fiber walks the tree, tracks updates.
3. For components that render native views, React Native creates **shadow nodes**.
4. These shadow nodes are structured into the **Shadow Tree**.
5. The **Yoga engine** runs layout on the shadow tree.
6. Native views are updated based on layout calculations and diffs from this shadow tree.

---

## 🧠 Summary: Key Differences

| Feature      | Virtual Tree (Fiber)             | Shadow Tree (Yoga/Layout)           |
| ------------ | -------------------------------- | ----------------------------------- |
| Purpose      | Component structure & state mgmt | Layout calculation & diffing        |
| Location     | JavaScript / React runtime       | Java / Objective-C / C++ (native)   |
| Manages      | JSX, props, hooks, logic         | Position, size, layout              |
| When Created | During React render phase        | During native render phase          |
| Used By      | React Fiber                      | Yoga + React Native rendering layer |

---
