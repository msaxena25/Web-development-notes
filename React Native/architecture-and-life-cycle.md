# 🏛️ React Native Old Architecture: Detailed Breakdown

The old architecture of React Native (pre-Fabric) is built around a **three-thread model** and a **bridge**. Here's how it works step by step:

---

### 🧵 1. **Three Main Threads**

| Thread                    | Purpose                                                                         |
| ------------------------- | ------------------------------------------------------------------------------- |
| **JavaScript Thread**     | Runs the React app logic, written in JS                                         |
| **Native Modules Thread** | Executes native functions (like accessing the camera, file system)              |
| **Main/UI Thread**        | Handles layout and rendering of UI (e.g., UIKit on iOS, View system on Android) |

---

### 🧠 2. **JavaScript Thread**

* Runs in a separate JS engine (`JavaScriptCore` on iOS, JSC or Hermes on Android).
* Executes your React code: JSX rendering, state changes, timers, business logic.
* Doesn’t directly interact with the native UI.

---

### 🔗 3. **The Bridge (The Heart of Old Architecture)**

> **The Bridge is an asynchronous, batched, and serialized communication channel between JS and native.**

It handles **message passing between the JS and native worlds**:

* JS → Native: sends commands to create views, handle layout, trigger native modules.
* Native → JS: sends back events like user gestures, lifecycle events.

---

#### How the Bridge Works Internally:

1. **JavaScript calls native code**:

   * React reconciler decides to create/update/remove views.
   * Converts this into a JSON message:

     ```json
     {
       "module": "UIManager",
       "method": "createView",
       "args": ["view123", "Text", { "text": "Hello" }]
     }
     ```

2. **Message goes through the bridge**:

   * Serialized into JSON.
   * Added to a message queue.

3. **Native code reads the queue**:

   * De-serializes JSON.
   * Calls actual native APIs (e.g., `UIView`, `ViewGroup`, camera, maps, etc.)

4. **User interacts**:

   * Touch/tap gesture is detected by the native UI thread.
   * Event info is serialized and sent **back through the bridge** to JS.
   * JS may update state → re-renders → sends another message back.

---

### 🔁 Full Communication Cycle Example

Let's say you render:

```jsx
<Button title="Click Me" onPress={() => alert("Hello")} />
```

#### Step-by-Step:

1. **JS Thread** (Reconciliation):

   * React decides it needs to create a `Button`.
   * Prepares a command like:

     ```json
     { "module": "UIManager", "method": "createView", "args": [...] }
     ```

2. **Bridge**:

   * Serializes and queues the message.

3. **Native Thread**:

   * Deserializes message.
   * Instantiates a `UIButton` or `Button` depending on platform.
   * Adds it to native UI tree.

4. **User Taps Button**:

   * Native code fires `onPress` event.
   * Serialized and passed to JS.

5. **JS Thread**:

   * Handles event: `alert("Hello")`.
   * No UI change, so no new commands go back.

---

## 📉 Major Limitations of the Old Architecture

| Issue                            | Explanation                                                                                    |
| -------------------------------- | ---------------------------------------------------------------------------------------------- |
| ❌ **Bridge bottleneck**          | Messages are serialized (JSON) and batched; large or frequent messages slow things down.       |
| ❌ **Asynchronous-only**          | Everything over the bridge is async → not suitable for real-time interactions like animations. |
| ❌ **No shared memory**           | JS and Native cannot share memory or objects. Always copy and serialize data.                  |
| ❌ **Slow native module access**  | Each native module call has overhead due to bridge crossing.                                   |
| ❌ **Poor animation performance** | Requires constant JS-native back-and-forth, leading to frame drops.                            |

-------

# **Bridge**

The **bridge** in React Native's old architecture is **not a third-party library or tool** — it's a **built-in internal mechanism** provided by React Native itself.

---

## 🧱 So, What *Exactly* Is the Bridge?

### ✅ The Bridge is:

> A **core part of React Native’s runtime**, responsible for **communication between the JavaScript side and the native side** (iOS/Android).

---

### 📌 It Is:

* **A design pattern / mechanism**, not an external library.
* **Implemented natively** in C++, Java (Android), and Objective-C/Swift (iOS).
* **Auto-loaded** when your React Native app boots.
* **Invisible to developers** most of the time — used behind the scenes unless you’re building native modules.
-----

## 🔁 Communication Example

Here’s how the bridge acts as a **messenger** between JS and Native:

```jsx
<Button onPress={() => console.log('Pressed')} />
```

**Flow:**

1. JS thread says: “Create a Button”.
2. Serializes this into a JSON-like message.
3. The bridge queues and batches this message.
4. Native side receives the message:

   * Android: `ReactTextViewManager` creates a native `TextView`.
   * iOS: `RCTTextManager` creates a `UILabel`.
5. Native touches/events go back to JS the same way.

----
## ✅ Is the Bridge Written in C++ for Android and Objective-C for iOS?

### 🤝 **Bridge is a cross-platform mechanism** with **multiple language layers**, like this:

| Layer                           | Purpose                                  | Android                  | iOS               |
| ------------------------------- | ---------------------------------------- | ------------------------ | ----------------- |
| **Core Logic (Cross-Platform)** | Shared logic to connect JS & native      | ✅ C++ (`ReactCommon/`)   | ✅ C++             |
| **Platform Integration**        | Glue code to integrate with the platform | ✅ Java/Kotlin            | ✅ Objective-C     |
| **JS Runtime**                  | Executes JS and sends messages to bridge | ✅ JS engine (Hermes/JSC) | ✅ JS engine (JSC) |

---

## 🧠 So, the Bridge Is:

| Part                | Language        | Purpose                                                           |
| ------------------- | --------------- | ----------------------------------------------------------------- |
| **Bridge Core**     | **C++**         | Cross-platform message queue and routing                          |
| **Android Side**    | **Java**        | Converts bridge messages into Android APIs (Views, Modules, etc.) |
| **iOS Side**        | **Objective-C** | Converts bridge messages into UIKit, native modules, etc.         |
| **JavaScript Side** | **JavaScript**  | Sends/receives messages to/from native                            |
---

## 📌 Summary

* ✅ The **core bridge logic is in C++** (shared between platforms).
* ✅ The **platform-specific bridge code is in Java (Android)** and **Objective-C (iOS)**.
* ❌ It is **not written only in C++ for Android** or only in Objective-C for iOS — it’s a combination.
* ✅ It’s an **internal mechanism**, not a public-facing library.

---

## 🧠 Why Use **C++** in React Native’s Bridge (Old & New)?

### 🔑 Main Reasons:

---

### 1. **Cross-Platform Compatibility (Write Once, Use on Android & iOS)**

* C++ can compile and run on **both Android (via NDK)** and **iOS (via Objective-C++ and Xcode)**.
* This allows React Native to share a single codebase (JSI, Fabric logic, core abstractions) between platforms without rewriting everything twice.

🧩 Example:

* Core logic like layout diffing, shadow tree updates, and module calling can be shared via C++.

---

### 2. **High Performance / Low-Level Access**

* C++ is a **compiled, low-level language**, so it offers:

  * Fine-grained memory control (no garbage collector pauses)
  * Fast execution
  * Synchronous processing without blocking the main thread

🏎️ This is **crucial** for real-time UI updates, animations, and low-latency function calls between JS and native.

---

### 3. **Avoid Overhead of Java/Objective-C Abstractions**

* Old architecture used Java (Android) and Objective-C (iOS), which are:

  * **Heavier** due to bridging and message-passing
  * Slower (because of serialization/deserialization between JS and native)
* C++ lets React Native:

  * Talk directly to the JS engine (like Hermes or JSC)
  * Skip the "bridge bottleneck"

🧠 With **JSI**, C++ directly exposes native functions to JavaScript.

---

### 4. **Hermes Engine Integration (Also Written in C++)**

* Hermes, the optimized JS engine for React Native (especially on Android), is written in C++.
* Writing the bridge/JSI in C++ allows **tighter integration with the JS runtime**, eliminating glue code and overhead.

---

### 5. **Interfacing with Both Java and Objective-C++**

* C++ can interoperate with both:

  * **Java** (via JNI on Android)
  * **Objective-C++** (a variant of Objective-C that allows mixing with C++ on iOS)

🔄 This makes it the perfect middle layer between JS and platform-specific code.

---

## 🧩 Summary Table

| Reason                     | Explanation                                                         |
| -------------------------- | ------------------------------------------------------------------- |
| ✅ Cross-platform           | Write once, run on Android (JNI) & iOS (Objective-C++)              |
| ✅ Performance              | Faster execution and memory control                                 |
| ✅ Direct JS engine access  | JSI and Hermes are written in C++                                   |
| ✅ Avoids double code       | One C++ module instead of Java + Obj-C                              |
| ✅ Native bridge efficiency | Enables sync/async native function calls without JSON serialization |

---

## ❓ Can React Native Use C Instead of C++?

### Technically:

✅ **Yes**, React Native *could* use C — but…

### Practically:

❌ **C is not ideal** for React Native's needs, and here's why.

---

## 🔍 Why C++ is Preferred Over C in React Native

| Capability                    | C                             | C++                                           | Why It Matters                                                                                         |
| ----------------------------- | ----------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| ✅ Cross-platform              | ✅ Yes                         | ✅ Yes                                         | Both support iOS and Android (via NDK and Objective-C++)                                               |
| ❌ Object-Oriented             | ❌ No                          | ✅ Yes                                         | C++ allows defining objects and classes — critical for complex UI systems like Fabric and TurboModules |
| ❌ Abstraction & Reusability   | ❌ Low                         | ✅ High                                        | C++ supports inheritance, polymorphism, and templates                                                  |
| ❌ Standard Library            | ❌ Minimal (`stdio`, `stdlib`) | ✅ Rich (STL, smart pointers, containers)      | Speeds up development and safety                                                                       |
| ❌ Exception Handling          | ❌ No                          | ✅ Yes                                         | Makes native code more robust and maintainable                                                         |
| ❌ Interfacing with JS Engines | ❌ Limited                     | ✅ Easy with JSI (JavaScript Interface is C++) | JSI is written in C++ — using C would require a C++ wrapper anyway                                     |

---

### 🧠 Real Example:

**JSI (JavaScript Interface)** — the core layer for calling native functions from JS — is written entirely in **modern C++** (with classes, templates, smart pointers, etc.).

Trying to use C here would force you to:

* Rewrite or wrap JSI with procedural C APIs
* Lose performance optimizations and maintainability
* Add unnecessary complexity

--------------

# **React Native New Architecture**

## Overview

React Native’s **new architecture** is designed to solve the performance and flexibility issues of the old bridge-based architecture. It introduces:

1. **JSI (JavaScript Interface)**
2. **Fabric (New Renderer)**
3. **TurboModules (Improved Native Modules)**
4. **Codegen (Automatic Bindings Generator)**

These work together to deliver better performance, modularity, and easier native integration.


# 🧠 What is JSI in React Native?

> **JSI (JavaScript Interface)** is a **C++ API layer** that provides a direct bridge between **JavaScript and native C++/platform code**, without relying on the traditional "Bridge" used in the old React Native architecture.

---

### 🔧 Why JSI was Introduced?

The **old bridge**:

* Was **asynchronous**, **serializes data to JSON**, and required **marshaling** data between JavaScript and native code.
* It caused **performance bottlenecks**, especially for high-frequency interactions (like animations, gestures, etc.)

The **new architecture**:

* Introduces **JSI**, **Fabric**, **TurboModules**, and **Codegen**.
* Allows **synchronous, direct access** from JS to native code using **C++ function pointers**, with **no JSON serialization**.
---

### 🧩 How JSI Fits into the New Architecture

```
          ┌────────────────┐
          │ JavaScript App │
          └──────┬─────────┘
                 │ uses
                 ▼
          ┌──────────────┐
          │     JSI      │  ← C++ API Layer (core of new bridge)
          └──────┬───────┘
                 │ calls
      ┌──────────▼────────────┐
      │ TurboModules & Fabric │ ← Modern Native Modules & UI renderer
      └───────────────────────┘
                 │
          ┌──────▼─────┐
          │ Native Code│ (Android/iOS)
          └────────────┘
```

---

### 🔄 Example Use Case

Let’s say you want to call a **native module** for accessing device storage:

**Old architecture**:

* JS → Bridge → JSON → Native queue → Deserialize → Native method call

**New (with JSI)**:

* JS → Direct C++ function call → Native method (no JSON, no queues)

---

### ✅ Benefits of JSI

* 🔄 **Faster communication** between JS and native.
* 📉 **Lower memory usage** and fewer threads.
* 🚀 Enables real-time, smooth animations and gestures (used with **Reanimated 2**, **Skia**, etc.).
* 📦 Enables smaller app bundles due to efficient codegen.

---

### 📅 Introduced In

JSI was introduced as part of the **React Native New Architecture**, formally starting around:

* **React Native 0.69** (JSI, TurboModules opt-in)
* **React Native 0.71+** (improved stability, more built-in modules migrated)

------

# 🧱 What is Fabric in React Native?

> **Fabric** is the **new rendering system** introduced in React Native to **replace the old UI Manager**, making rendering more efficient, concurrent, and better integrated with React’s **Fiber architecture**.

It’s built to work **in sync with React’s core rendering engine**, and unlocks features like:

* Concurrent rendering
* Layout improvements
* Better error handling
* Improved native performance

---

## 🧠 Why Fabric?

In the **old architecture**:

* The UI update pipeline was **asynchronous and multi-staged**, often causing frame drops.
* The JavaScript thread would update the virtual DOM, and send UI updates to the native thread **through a bridge**, serialized as JSON.
* Native views were updated via a centralized, complex **UI Manager**.

With **Fabric**, the rendering is:

* **Synchronous when needed**
* **Thread-agnostic**
* **Direct and optimized**, working with **JSI** and **C++**

---

Here's a quick 2-line summary for each:

---

### ✅ **Concurrent Rendering**

Allows React Native to interrupt rendering work and prioritize urgent tasks.
Improves responsiveness by avoiding UI freezes during heavy computation.

### ✅ **Layout Improvements**

Fabric uses the Yoga engine more efficiently for layout calculations.
Layouts are more accurate and consistent across Android and iOS.

### ✅ **Better Error Handling**

Fabric provides detailed native-to-JS stack traces for UI errors.
Easier debugging with clearer links between JS code and native failures.

### ✅ **Improved Native Performance**

Direct rendering via Fabric and JSI removes bottlenecks from the old bridge.
Reduces overhead, making animations and interactions smoother and faster.

----------
---

# 🚀 What are TurboModules?

> **TurboModules** are the **next-generation native modules** system in React Native that allows **lazy loading**, **direct access via JSI**, and **faster communication** between JavaScript and native code.

They replace the older **NativeModules** mechanism that relied on the asynchronous, JSON-based **bridge**.

---

### 🧠 Why TurboModules Were Introduced?

The old system had issues:

* Native modules were **eagerly loaded**, even if not used.
* Communication was **serialized** (JSON), making it slow.
* Native code was not well typed or auto-generated.

**TurboModules** solve these problems with:

* Lazy loading
* JSI-based direct binding
* Strong typing with **Codegen**

---

### 🔧 How TurboModules Work (Step-by-Step)

1. JS code requests a native module.
2. If it's a TurboModule, it's **loaded on-demand (lazy loaded)**.
3. JSI provides a **direct C++ pointer** to the native function — no bridge, no serialization.
4. You call the native function like a regular JS function — fast and seamless.

---

# ⚙️ What is CodeGen in React Native?

> **CodeGen** is a tool in React Native’s new architecture that **automatically generates native code (bindings)** for **TurboModules** and **Fabric components** based on **JavaScript/TypeScript interface definitions**.

It ensures that the communication between **JavaScript and native code is type-safe, fast, and boilerplate-free**.

---

## 🧠 Why CodeGen Was Introduced?

Before CodeGen:

* You had to manually write bindings in Java/Kotlin (Android) or Obj-C/Swift (iOS).
* Any typo or mismatch between JS and native could cause **runtime crashes**.
* Lots of **duplicate code** across platforms.

With CodeGen:

* You define the interface **once in TypeScript or Flow**, and it **auto-generates native code** for both Android and iOS.
* Safer, faster, and much easier to maintain.

--

## 🧱 CodeGen Adds To:

* 🔥 **Performance** — minimal runtime overhead
* 🧠 **Reliability** — build-time validation of APIs
* 🚫 **Less Boilerplate** — no need to manually write glue code


---

# 🚄 What Is **Metro**?

**Metro** is the **JavaScript bundler** used by **React Native**.
It takes all your JavaScript code (and dependencies), processes it, and **outputs a single optimized JavaScript bundle** that can run on your mobile device.

Think of it as the **Webpack of React Native** — but optimized specifically for mobile apps.

---

## ⚙️ What Does Metro Do?

| Task                                     | Description                                                                      |
| ---------------------------------------- | -------------------------------------------------------------------------------- |
| 🧩 **Dependency Resolution**             | Figures out which files need to be included (JS, JSON, images, etc.)             |
| 📦 **Bundling**                          | Combines all JS files into one file (or multiple chunks for RAM bundles)         |
| ⚡ **Transforms**                         | Compiles ES6+, JSX, and Flow/TypeScript into standard JavaScript                 |
| 🔥 **Hot Reloading**                     | Supports Fast Refresh and Live Reload                                            |
| 🪄 **Minification**                      | Compresses and optimizes JS for production                                       |
| 🗂️ **Asset Handling**                   | Handles static assets like images, fonts, etc., in the bundle                    |
| 🧠 **Caching**                           | Uses intelligent file caching for faster builds                                  |
| 📤 **Sends the JS Bundle to Mobile App** | Over a local server during development, or embeds it into APK/IPA for production |

---

## 📁 How It Works (High-Level)

1. You run:

   ```bash
   npx react-native start
   ```
2. Metro starts a **Node.js server** on `http://localhost:8081`.
3. The React Native app loads the JavaScript bundle from that server.
4. Metro:

   * Watches your file changes
   * Re-bundles quickly
   * Sends updated code via Fast Refresh

---

## 📦 Output Example

When you run the app:

* Metro sends `/index.bundle?platform=android&dev=true` to the app.
* The bundle includes:

  * React
  * Your app code
  * Dependencies
  * Any native module wrappers (via TurboModules or old Bridge)

---

## 🚀 Metro vs Webpack

| Feature                 | Metro                | Webpack          |
| ----------------------- | -------------------- | ---------------- |
| Built for React Native? | ✅ Yes                | ❌ No             |
| Mobile optimized?       | ✅ Yes                | ❌ No             |
| ES Modules support      | ✅ (with Babel)       | ✅                |
| Handles native assets?  | ✅ Yes                | ❌ (needs config) |
| Dev server & HMR        | ✅ Yes (Fast Refresh) | ✅ Yes            |

---

## 🛠️ Metro Config Customization

Metro is customizable via a `metro.config.js` file.

Example:

```js
const { getDefaultConfig } = require("metro-config");

module.exports = (async () => {
  const defaultConfig = await getDefaultConfig();
  return {
    ...defaultConfig,
    resolver: {
      assetExts: [...defaultConfig.resolver.assetExts, 'bin'],
    },
  };
})();
```
---

## 🕰️ When Was Metro Introduced?

**Metro** was officially introduced as the bundler for React Native in **2017**, but it **started evolving internally at Facebook around 2015–2016** under the name **“Packager”**.

| Year                   | Milestone                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------- |
| **2015–2016**          | React Native used an internal "packager" at Facebook                                  |
| **2017**               | Renamed and rebranded as **Metro Bundler** (publicly)                                 |
| **React Native 0.43+** | Metro became the default bundler                                                      |
| **2018–Now**           | Metro becomes a standalone project on GitHub and continues evolving with React Native |

---

## 🧰 Is Metro an Internal or External System?

### ✅ It is an **internal tool for React Native**, but developed as a **standalone open-source project**.

| Aspect                               | Answer                                                                   |
| ------------------------------------ | ------------------------------------------------------------------------ |
| Built by                             | Facebook / Meta                                                          |
| Used by                              | React Native (official bundler)                                          |
| Part of React Native CLI?            | ✅ Yes (tightly integrated)                                               |
| Can it be used outside React Native? | ⚠️ Technically yes, but not common — it’s tailored for React Native apps |
| Is it open source?                   | ✅ Yes ([GitHub – facebook/metro](https://github.com/facebook/metro))     |

---

## Is Metro Part of React Native’s Architecture?

**No, Metro is not part of the React Native architecture itself.**

* The **React Native architecture** refers to how JavaScript and native layers interact at runtime (JSI, Fabric, TurboModules, Bridge, etc.).
* **Metro** is a **JavaScript bundler and development server** that runs during **build time and development**, **not at runtime inside the app**.

---

## What Is Metro Then?

* Metro **bundles** your JS code and assets into a single (or multi) bundle(s).
* It **serves** the JS bundle to the React Native app during development (via a local dev server).
* It supports **fast refresh**, source maps, asset resolution, and code transformation.
* Metro runs **outside the app process**, on your development machine (or CI), and **does not run inside the native app**.

---

## Is Metro Part of React Native Lifecycle?

* Metro is involved in the **development/build lifecycle**, but **not the app runtime lifecycle**.
* The **app lifecycle** (JS and native threads running on the device) uses React Native’s architecture to interpret and execute the bundle Metro produced.
* When the app starts, it **loads the Metro-generated JS bundle** from disk (or Metro server in dev), then React Native runtime + native modules + JSI/Fabric/etc run on the device.

-----------------------


# ⚡ What is Fast Refresh? **how Metro enables Fast Refresh (hot reloading)**

> **Fast Refresh** is the modern version of hot reloading in React Native.
> It re-runs only the **updated modules** without restarting the whole app or losing state (unless the update affects stateful components).

---

## 🧠 Full Story: What Happens When You Change One Line of Code

Let's say you modify a line in `MyComponent.js`. Here's what happens from edit to seeing the change on your phone:

---

### ✅ 1. **File Change Detection (File Watcher)**

* Metro runs a **Node.js development server**.
* It uses a **file watcher** (based on tools like `watchman` or Node's `fs.watch`) to detect changes in the filesystem.
* As soon as you save `MyComponent.js`, the change is **detected instantly**.

---

### ⚙️ 2. **Module Dependency Graph Update**

* Metro maintains a **live dependency graph** of your entire app.
* When `MyComponent.js` changes:

  * Metro updates the graph for that file.
  * It checks what other files depend on it (if any).
  * It determines if the change is **local** or affects other modules.

---

### 🔧 3. **Partial Rebuild (Incremental Bundling)**

* Metro does **not rebuild the entire app**.
* It **compiles only the changed file and its dependencies** using Babel (with React Native presets).
* It generates a new JavaScript bundle **just for that module**.

---

### 🌐 4. **WebSocket Communication with the App**

* When Metro starts, the React Native app (running on your phone/emulator) **opens a WebSocket connection** to the Metro server.
* After bundling the updated module, Metro **sends it over this WebSocket** to the running app.

---

### 🔁 5. **Fast Refresh Runtime Applies the Update**

* The app has a runtime (inside the React Native framework) that:

  * Receives the updated module.
  * Replaces the old version in memory.
  * Re-runs the updated module.
* If the updated module is a **component**, React Fast Refresh system will **re-render it without losing its state** (if the state structure didn't change).

---

### 🧪 6. **Error Recovery & State Preservation**

* If there’s an error, the Fast Refresh system shows a red screen (error overlay) with stack trace.
* If you fix the error and save again, the updated module reloads **without resetting the whole app**.

---

## 🎯 What Makes It Fast?

| Feature                                 | How It Helps                                     |
| --------------------------------------- | ------------------------------------------------ |
| 🔄 **Incremental updates**              | Only changed files are rebuilt and sent          |
| 📡 **WebSocket channel**                | Real-time communication with the app             |
| 🧠 **Smart state retention**            | Keeps component state if possible                |
| 🧰 **React Refresh (via Babel plugin)** | Fine-grained control over module hot-replacement |

---

## 🔬 Under the Hood: Babel + React Refresh

When bundling, Metro uses:

* **Babel with React Refresh plugin**
* This wraps your component definitions with special hot-swap logic
* It tells React which components can be hot-reloaded and which ones need full re-rendering

---

# **HMR (Hot Module Replacement API)**.

Let me now **clarify, correct, and expand** the full picture, including what role **HMR** plays, and whether anything important was missing.

---

## ✅ First, is HMR used in React Native via Metro?

**Yes — Metro does use HMR under the hood.**

But there's a key distinction:

### 🧠 React Native used to rely on **raw HMR**, but now uses **Fast Refresh**, which is built *on top of HMR*.

---

## 📌 So what’s the difference between HMR and Fast Refresh?

| Feature            | **HMR (Old Style)**        | **Fast Refresh (Modern)**       |
| ------------------ | -------------------------- | ------------------------------- |
| Reload granularity | Module-level               | Component-level                 |
| State preservation | Often lost                 | Preserved (if safe)             |
| Error handling     | Crashes app or loses state | Recovers gracefully             |
| Used in RN?        | ✅ Previously               | ✅ Now (wrapped by Fast Refresh) |

So yes — Metro uses **HMR as a lower-level mechanism**, but what React Native developers interact with is **Fast Refresh**, which **wraps HMR and adds intelligence**.

---

## **How HMR (Hot Module Replacement) worked in older versions of React Native**

 Let’s walk through **how HMR (Hot Module Replacement) worked in older versions of React Native** — **before Fast Refresh was introduced** (i.e., React Native < 0.61).

----

## 🚧 Context: Old HMR (Before React Native 0.61)

React Native used **basic Webpack-style HMR**, adapted for Metro. It allowed updating **JavaScript modules** on the fly, but **not intelligently**, and had several limitations:

- ❌ It didn’t reliably preserve state.
- ❌ Could easily crash the app or cause inconsistent UI.
- ❌ Developers had to **opt-in manually** (`module.hot.accept()`).
- ❌ Complex component trees broke the updates.
- ❌ Sometimes caused memory leaks.

---

## 🧩 Old React Native HMR: Step-by-Step Flow

Let’s say you change a single line in `MyComponent.js`.

---

### 🟩 1. **File Change Detected**

- Metro’s file watcher notices the file change.
- It marks `MyComponent.js` as **dirty** and schedules a rebuild.

---

### 🟨 2. **Re-Bundle Affected Module**

- Metro recompiles **only** the changed module (`MyComponent.js`).
- It injects **HMR code**, which looks like:

```js
if (module.hot) {
  module.hot.accept(() => {
    // Replace the module at runtime
  });
}
```

- This code must be manually or automatically added.

---

### 🟧 3. **WebSocket Sends HMR Update to the App**

- The updated module is sent over **WebSocket** from Metro to the app (like now).
- The app’s HMR runtime receives this update.

---

### 🟥 4. **Runtime Replaces the Module in Memory**

- The HMR runtime swaps out the old `MyComponent` module with the new one.
- **No deep inspection** is done.
- The component might **re-render**, but often loses state or crashes.

---

### ⚠️ 5. **Developer Handles Side Effects**

- If `MyComponent` has internal state, it’s **lost** unless the developer manually re-injects it.
- Hooks often **break silently**.
- Updates in component files **could invalidate unrelated modules**.

---

## 🧠 Why Fast Refresh Replaced It

Fast Refresh fixed HMR’s problems by:

- Being **React-aware** (component-level granularity)
- Automatically preserving state (when safe)
- **Auto-registering** components (via Babel plugin)
- Resetting only affected components instead of the whole tree
- Providing much more consistent developer experience

---

# **Webpack vs Metro bundler**

## Webpack

* **Primary Use Case:** Primarily used for **web applications**. It bundles JavaScript, CSS, images, and other assets for deployment on browsers.
* **Highly Configurable:** Webpack is known for its extensive configuration options. It uses a `webpack.config.js` file where you define entry points, output paths, loaders (to process different file types like JSX, TypeScript, CSS), and plugins (to perform broader tasks like optimization, asset management, etc.). This flexibility makes it powerful for complex, large-scale web projects.
* **Ecosystem:** Has a vast and mature plugin and loader ecosystem, allowing for nearly unlimited customization and integration with various tools and workflows.
* **Development Server (webpack-dev-server):** Includes a built-in development server with Hot Module Replacement (HMR) for instant updates without full page reloads.
* **Optimizations:** Offers features like code splitting (breaking down code into smaller chunks for faster loading), tree shaking (removing unused code), and various other optimizations for production builds.

## Metro Bundler

* **Primary Use Case:** The official JavaScript bundler for **React Native** applications (for iOS and Android). While it has growing web support, its core optimization is for mobile environments.
* **Optimized for React Native:** Metro is specifically designed to handle the unique requirements of React Native, including features like fast refresh, native module integration, and efficient bundling for mobile devices.
* **Less Configurable (by default):** Metro generally aims for "zero configuration" or minimal configuration out-of-the-box, trading some configurability for performance and ease of use in React Native projects. While you can customize it with a `metro.config.js` file, it's typically less involved than a Webpack setup.
* **Performance:** Metro is designed for speed, aiming for sub-second reload cycles and fast startup times in development, particularly for React Native. It utilizes aggressive caching and on-demand processing.
* **Multi-dimensional:** Unlike traditional bundlers that might create separate instances for server and client code, Metro is optimized for multiplatform development, maximizing resource reuse across platforms and environments.

## Is React Web use Webpack?

Historically, and still very commonly, **yes, React web applications frequently use Webpack.**

* **Create React App (CRA):** For a long time, if you started a React project with `create-react-app`, it came pre-configured with Webpack and Babel under the hood. You didn't need to manually configure Webpack, but it was there, handling the bundling, transpilation, and asset management.
* **Custom Setups:** Many larger or more complex React projects build their Webpack configurations from scratch to gain fine-grained control over the build process, performance optimizations, and integration with specific tools.
* **Alternatives:** While Webpack has been the dominant bundler for React web for years, alternatives like **Vite** have gained significant popularity. Vite offers a faster development experience, especially for larger projects, by leveraging native ES modules and tools like esbuild for pre-bundling.

---------

# **Journey of a React Native app on device**

Let's trace the journey of a React Native app from the moment you tap its icon on your mobile device. This process involves a fascinating interplay between native code (Java/Kotlin for Android, Objective-C/Swift for iOS) and JavaScript.

Here's a step-by-step guide:

### On Android

1.  **User Taps App Icon:**
    * When the user taps the app icon on the home screen, the Android operating system receives an intent to launch your app.
    * This triggers the execution of your app's main activity, typically defined in `AndroidManifest.xml`. This is the native entry point of your application.

2.  **`MainActivity.java` or `MainActivity.kt` Starts:**
    * The `MainActivity` (or a similar class you've defined) is the first Java/Kotlin code that runs.
    * In a React Native app, this activity's `onCreate()` method will typically:
        * Instantiate a `ReactRootView`. This is a native UI component provided by the React Native framework that will host your JavaScript-driven UI.
        * Load the JavaScript bundle (your entire React Native application code).

3.  **Loading the JavaScript Bundle:**
    * **Development Build:**
        * In development, the native code connects to the **Metro Bundler** running on your development machine (via Wi-Fi or USB debugging).
        * Metro then serves the JavaScript bundle over HTTP. This means the JavaScript code is not packaged inside the `.apk` file but is fetched remotely. This allows for fast refresh and hot module replacement.
    * **Production Build:**
        * In a production build, the JavaScript bundle (a single `.js` file, often named `index.android.bundle` or similar) is **pre-bundled by Metro** and packaged directly into the `.apk` file (in the `assets` folder).
        * The native code simply reads this local file to load the JavaScript.

4.  **JavaScript Environment (JS Engine) Initialization:**
    * Once the JavaScript bundle is loaded, React Native needs an environment to execute it.
    * On Android, this is typically **JavaScriptCore** (a JavaScript engine developed by Apple, also used in Safari), which is bundled with the React Native framework.
    * A JavaScript Virtual Machine (JS VM) is created within the native app's process.

5.  **Bridging or JSI, Fabric Renderer (modern) and Communication Setup:**
    * This is the core of React Native. A "bridge" is established between the native environment (Java/Kotlin UI thread) and the JavaScript environment (JS thread).
    * This bridge allows for asynchronous, batched communication between the two realms:
        * JavaScript code can call native modules (e.g., to access camera, GPS, file system).
        * Native events (e.g., touch events, network responses) can be sent to JavaScript.

6.  **React Native App Mounts (JavaScript Execution):**
    * The JavaScript code (your `App.js` and all its components) starts executing within the JS VM.
    * React's reconciliation process begins. It constructs a virtual DOM representing your UI.
    * When React needs to render something on the screen, it sends instructions across the bridge to the native side. These instructions describe what native UI components (e.g., `TextView`, `ImageView`, `Button`) need to be created or updated.

7.  **Native UI Rendering:**
    * The native UI thread receives these instructions from JavaScript.
    * It then creates and renders actual, platform-specific native UI components on the screen.
    * When the user interacts with these native components (e.g., taps a button), the native event is sent back across the bridge to the JavaScript side, where your React component's event handlers are triggered.

### On iOS

1.  **User Taps App Icon:**
    * Similar to Android, tapping the app icon starts the application process.
    * The `AppDelegate.m` or `AppDelegate.swift` file is the native entry point for an iOS app.

2.  **`AppDelegate.m` or `AppDelegate.swift` Starts:**
    * The `application:didFinishLaunchingWithOptions:` method in `AppDelegate` is called.
    * Here, a `RCTRootView` (the iOS equivalent of `ReactRootView`) is instantiated.
    * The `RCTRootView` is configured to load your JavaScript bundle.

3.  **Loading the JavaScript Bundle:**
    * **Development Build:**
        * The native code connects to the **Metro Bundler** running on your development machine.
        * Metro serves the JavaScript bundle over HTTP.
    * **Production Build:**
        * The JavaScript bundle (e.g., `main.jsbundle`) is pre-bundled by Metro and included in the app's `.ipa` archive.
        * The native code reads this local file.

4.  **JavaScript Environment (JS Engine) Initialization:**
    * On iOS, **JavaScriptCore** is always used as the JavaScript engine. It's built into the iOS operating system.
    * A JS VM is created within the app's process.

5.  **Bridging and Communication Setup:**
    * A similar native-to-JavaScript bridge is established on iOS, facilitating asynchronous and batched communication.

6.  **React Native App Mounts (JavaScript Execution):**
    * The JavaScript code executes within the JS VM.
    * React's reconciliation process creates the virtual DOM.
    * Instructions for native UI components are sent across the bridge to the native side.

7.  **Native UI Rendering:**
    * The native UI thread (main thread on iOS) receives these instructions.
    * It creates and renders actual platform-specific native UI views (e.g., `UIView`, `UILabel`, `UIButton`).
    * User interactions on these native views trigger events that are sent back to the JavaScript side.

-----------

# **The lifecycle of a React Native application**

The lifecycle of a React Native application can be understood from 3 main perspectives:

1.  **Application Lifecycle (Native Side):** How the mobile operating system manages your app's overall state.
2.  **Component Lifecycle (JavaScript Side):** How individual React components manage their existence, rendering, and updates.
3.  **Compile and Build Lifecycle (Development/Production)**

Let's break them down.

---

### 1. Application Lifecycle (Native Side)

This refers to the states your app goes through as managed by the operating system (iOS or Android). React Native provides the `AppState` API to detect these changes in your JavaScript code.

* **`active`**: The app is running in the foreground. This is the initial state when the app is launched and what users primarily interact with.
* **`background`**: The app is running but in the background. This happens when:
    * The user presses the home button.
    * The user switches to another app.
    * On iOS, the user pulls down the notification center or control center.
    * On Android, an incoming call or certain system pop-ups appear.
    * The app might still be executing code (e.g., background tasks) but is not visible to the user.
* **`inactive` (iOS only)**: A transitional state. On iOS, when the app is about to go into the background (e.g., due to an incoming call or swiping up to the app switcher), it first enters `inactive` before moving to `background`. It can also return to `active` from `inactive`.
* **`unknown`**: Initial state, or when the state cannot be determined.

**How to detect in React Native (JavaScript):**

```javascript
import { AppState } from 'react-native';
import React, { useEffect, useRef } from 'react';

function AppStateExample() {
  const appState = useRef(AppState.currentState);

  useEffect(() => {
    const subscription = AppState.addEventListener('change', nextAppState => {
      if (appState.current.match(/inactive|background/) && nextAppState === 'active') {
        console.log('App has come to the foreground!');
      }
      appState.current = nextAppState;
      console.log('AppState', appState.current);
    });

    return () => {
      subscription.remove();
    };
  }, []);

  return (
    // Your app UI
  );
}
```

---

### 2. Component Lifecycle (JavaScript Side - React Lifecycle)

This is about how individual React components (functional or class components) are created, updated, and destroyed.

#### For Functional Components (using Hooks - the modern approach):

Functional components don't have traditional lifecycle *methods* but achieve similar effects using **React Hooks**, primarily `useEffect` and `useState`.

* **Mounting (Component Creation):**
    * **`useState()` and `useRef()`:** Initialize state and refs.
    * **`useEffect(callback, [])` (empty dependency array):** Runs *after* the initial render. This is equivalent to `componentDidMount`. Perfect for data fetching, setting up subscriptions, or DOM manipulations that need the component to be rendered.
    * **Component Function Body:** Runs on every render (initial and updates).

* **Updating (Component Re-render):**
    * **`useState()` state updates:** Trigger re-renders.
    * **Props changes:** If parent component passes new props.
    * **`useEffect(callback, [dependencies])` (with dependencies):** Runs *after* every render where one of its dependencies has changed. This can be seen as a combination of `componentDidMount` and `componentDidUpdate`.
    * **Component Function Body:** Runs on every re-render.

* **Unmounting (Component Destruction):**
    * **`useEffect(() => { return () => { /* cleanup */ } }, [])`:** The function returned from `useEffect` (the cleanup function) runs right before the component is unmounted. This is equivalent to `componentWillUnmount`. Use this for unsubscribing, clearing timers, or cancelling network requests.

**Example for Functional Component Lifecycle (Hooks):**

```javascript
import React, { useState, useEffect } from 'react';
import { View, Text } from 'react-native';

function MyFunctionalComponent(props) {
  const [count, setCount] = useState(0);

  // Runs ONCE after initial render (Mounting)
  useEffect(() => {
    console.log('Functional Component Mounted!');
    // Example: Fetch data
    return () => {
      // Runs ONCE before component is unmounted (Unmounting)
      console.log('Functional Component Unmounted!');
      // Example: Cleanup subscriptions, clear timers
    };
  }, []); // Empty dependency array means it runs once on mount and cleanup on unmount

  // Runs after initial render AND whenever 'count' or 'props.name' changes (Updating)
  useEffect(() => {
    console.log('Count or Name changed:', count, props.name);
  }, [count, props.name]); // Dependencies: runs only when these change

  console.log('Functional Component Rendered/Re-rendered'); // Runs on every render

  return (
    <View>
      <Text>Count: {count}</Text>
      <Text>Name: {props.name}</Text>
      <Text onPress={() => setCount(prevCount => prevCount + 1)}>
        Increment Count
      </Text>
    </View>
  );
}
```

---

#### For Class Components (Legacy - less common for new code):

Class components have specific lifecycle methods. While still supported, they are generally less favored for new development due to the simplicity and power of hooks.

* **Mounting (Component Creation):**
    * **`constructor(props)`:** Called first. Used for initializing state and binding methods. **Do not cause side effects here.**
    * **`static getDerivedStateFromProps(props, state)`:** Called before every render. Seldom used, for very specific cases where state needs to be derived from props.
    * **`render()`:** The only required method. Returns the JSX elements to be rendered.
    * **`componentDidMount()`:** Called immediately after the component is mounted (inserted into the tree). Good for data fetching, setting up subscriptions, and direct DOM manipulation.

* **Updating (Component Re-render):**
    * **`static getDerivedStateFromProps(props, state)`:** (Same as mounting).
    * **`shouldComponentUpdate(nextProps, nextState)`:** Rarely used. Allows you to optimize performance by returning `false` if you know the component doesn't need to re-render.
    * **`render()`:** Renders the updated UI.
    * **`getSnapshotBeforeUpdate(prevProps, prevState)`:** Rarely used. Called right before the changes from `render()` are committed to the DOM. Useful for capturing scroll position, etc.
    * **`componentDidUpdate(prevProps, prevState, snapshot)`:** Called immediately after updating occurs. Good for network requests based on prop changes, or reacting to state changes.

* **Unmounting (Component Destruction):**
    * **`componentWillUnmount()`:** Called immediately before a component is unmounted and destroyed. Use this for cleanup (e.g., invalidating timers, cancelling network requests, removing event listeners).

* **Error Handling:**
    * **`static getDerivedStateFromError(error)`:** Renders fallback UI after an error.
    * **`componentDidCatch(error, info)`:** Catches JavaScript errors in child components.

---

You're right to add that! While the "Application Lifecycle" and "Component Lifecycle" describe what happens at runtime on the device, the **Compile and Build Lifecycle** is crucial for preparing your React Native app *before* it even gets to the device.

Let's add that third perspective:

---

### 3. Compile and Build Lifecycle (Development/Production)

This lifecycle describes the process of transforming your React Native source code into a runnable application package (APK for Android, IPA for iOS) that can be installed on a device or distributed through app stores. This process is handled by tools like Metro Bundler, Babel, native build systems (Gradle for Android, Xcode for iOS), and often third-party tools like Fastlane for automation.

This lifecycle primarily occurs on your development machine or a Continuous Integration (CI) server.

* **1. Source Code (Your React Native Project):**
    * This is where you write your JavaScript/TypeScript code (React components, Redux logic, etc.), native module code (Java/Kotlin, Objective-C/Swift), and define your app's assets (images, fonts).

* **2. Dependency Resolution:**
    * **JavaScript Dependencies:** Tools like npm or Yarn read your `package.json` to download and install all your JavaScript dependencies (e.g., `react`, `react-native`, `redux`, `react-navigation`, third-party UI libraries).
    * **Native Dependencies:** For native modules, package managers might also trigger native dependency resolution (e.g., CocoaPods for iOS, Gradle for Android) to download native libraries.

* **3. JavaScript Transpilation & Bundling (Metro Bundler):**
    * **Transpilation (Babel):** Your modern JavaScript/TypeScript code (e.g., JSX, ES6+ features) is transformed into JavaScript compatible with older environments if needed, and JSX is converted into `React.createElement` calls. This is typically handled by Babel, which Metro integrates.
    * **Bundling:** The **Metro Bundler** (or an alternative bundler like Haul, although Metro is standard) traverses your entire JavaScript project, following `import` and `require` statements. It consolidates all your JavaScript modules and their dependencies into a single (or multiple, if code-splitting) optimized JavaScript file, known as the "bundle" (e.g., `index.android.bundle`, `main.jsbundle`).
    * **Asset Management:** Images, fonts, and other static assets referenced in your JavaScript are also processed and included or referenced correctly within the bundle or the native project.

* **4. Native Code Compilation:**
    * **Android (Gradle):** Your Java/Kotlin native code (from `android/` folder, and any native modules) is compiled into Dalvik bytecode. Resources (XML layouts, drawables) are processed.
    * **iOS (Xcode/Clang):** Your Objective-C/Swift native code (from `ios/` folder, and any native modules) is compiled into machine code. Storyboards, assets, and other resources are processed.

* **5. Linking and Packaging:**
    * **Android (APK Generation):** The compiled native code, processed resources, and the JavaScript bundle are all packaged together into an **Android Application Package (APK)** file. This is a single archive that can be installed on an Android device.
    * **iOS (IPA Generation):** The compiled native code, processed resources, and the JavaScript bundle are assembled into an **iOS App Store Package (IPA)** file. This is the archive for distribution on iOS devices.

* **6. Signing:**
    * The generated APK/IPA file is digitally signed with your developer certificate. This is a security measure that ensures the app's integrity and verifies its origin. Without proper signing, the app cannot be installed or distributed.

* **7. Distribution (Optional):**
    * The signed APK/IPA is now ready for distribution. This might involve:
        * Direct installation on a device for testing.
        * Uploading to a beta testing platform (e.g., App Center, TestFlight).
        * Submitting to app stores (Google Play Store, Apple App Store).

**Key Tools Involved in the Compile and Build Lifecycle:**

* **Node.js & npm/Yarn:** For JavaScript environment and package management.
* **Metro Bundler:** The primary JavaScript bundler for React Native.
* **Babel:** JavaScript compiler/transpiler.
* **Gradle (Android):** The build automation system for Android.
* **Xcode & Command Line Tools (iOS):** The IDE and build tools for iOS.
* **CocoaPods (iOS):** Dependency manager for iOS native libraries.
* **Fastlane:** Automation tool for building, signing, and releasing apps.

This compile and build lifecycle is what transforms your readable source code into the binary format that a mobile device can understand and execute. It's the critical bridge between your development environment and the actual runtime experience on the phone.

---

When you run `npm start` (or `yarn start` or `react-native start`), you are primarily initiating processes related to the **Compile and Build Lifecycle** in a **development-specific mode**.

Here's how it fits:

`npm start` kicks off the **Metro Bundler**.

* **During `npm start` (Development Mode):**
    * **Metro Bundler (JavaScript Transpilation & Bundling):** Metro starts listening for connections. It doesn't immediately create a full bundled file on your disk. Instead, it performs on-the-fly transpilation of your JavaScript/TypeScript code (using Babel) and serves it over a local HTTP server.
    * **Hot Module Replacement (HMR)/Fast Refresh:** Metro is configured to watch your code for changes. When you save a file, it quickly re-transpiles only the changed modules and pushes them to the running app on the device without a full reload, dramatically speeding up development.
    * **Asset Serving:** It also serves your assets (images, fonts, etc.).

When you then run `npm run android` or `npm run ios` (or directly open in Android Studio/Xcode and run), *that* action triggers the **Native Code Compilation** and **Linking and Packaging** steps of the **Compile and Build Lifecycle**. The native app then connects to the Metro server (started by `npm start`) to fetch the JavaScript bundle.

So, while `npm start` doesn't produce a final `.apk` or `.ipa` file, it's an essential prerequisite for the **development phase** of the **Compile and Build Lifecycle**, enabling the native app to dynamically load the JavaScript bundle from your machine.

-----

---