# State management

State management is a crucial aspect of application development, especially in modern web and mobile apps where data flows between components and needs to stay consistent. There are **multiple ways to manage state**, depending on the complexity of the app, the tech stack, and performance needs.

## **main approaches to state management**:

---

### 🧠 **1. Local State Management**
State is stored within individual components.

- **Tools/Techniques**:
  - React: `useState`, `useReducer`
  - Angular: Component properties
  - Vue: `data` object
- **Best for**: Simple UI interactions, form inputs, toggles

---

### 🌐 **2. Global State Management**
State is shared across multiple components or pages.

- **Libraries**:
  - **Redux** (React, Angular, etc.)
  - **MobX**
  - **Zustand**
  - **Recoil**
  - **Vuex** (for Vue)
  - **NgRx** (for Angular)
- **Best for**: Apps with complex data flows, user authentication, theme settings, etc.

---

### 🧪 **6. Context API (React-specific)**
Provides a way to pass data through the component tree without props.

- **Best for**: Theme, language, user settings
- **Note**: Not ideal for frequently changing state due to performance concerns

---

### 🗄️ **7. Persistent State**
State saved across sessions.

- **Storage Options**:
  - LocalStorage / SessionStorage
  - IndexedDB
  - Cookies
- **Best for**: User preferences, login tokens, offline support

---

# Redux

Redux is a **state management library** that:
- Stores the entire application state in a **single object tree** called the **store**
- Updates state using **pure functions** called **reducers**
- Triggers state changes through **actions**, which are plain JavaScript objects describing what happened

-----

## ❓ Why Do We Need Redux?

Redux becomes essential when:
- Your app has **complex state logic** spread across many components
- You need to **share state** between distant components
- You want **predictable behavior** and easier debugging

React already has built-in state management, but Redux solves deeper problems like:
- **Props drilling** (passing data through many layers)
- **Inconsistent state updates**
- **Difficulty in tracking changes** across components

---

## 🌟 Benefits of Using Redux

Here are the key advantages:

| Benefit                        | Description                                                                 |
|-------------------------------|-----------------------------------------------------------------------------|
| 🧭 Predictable State Updates   | State changes only through actions and reducers, making logic transparent  |
| 🏛 Centralized State Store     | All state lives in one place, simplifying access and updates                |
| 🧪 Easy Debugging              | Redux DevTools allow time-travel debugging and action logging               |
| 🔄 Pure Reducers               | Reducers are pure functions, making them easy to test and reason about      |
| 🚀 Performance Optimization    | Avoids unnecessary re-renders using shallow equality checks                 |
| 🔄 Unidirectional Data Flow    | Clear flow of data from actions → reducers → store → UI                     |
| 🔧 Middleware Support          | Easily add logging, async handling, or routing logic                        |
| 🌍 Framework Agnostic          | Works with React, Angular, Vue, and vanilla JS                              |
| 👥 Strong Community Support    | Tons of tutorials, tools, and extensions available                          |

-----

# Core Pillars of Redux Architecture


| Pillar           | Role                                                                 |
|------------------|----------------------------------------------------------------------|
| 🏦 **Store**      | Holds the entire state tree of the application                      |
| 📤 **Actions**    | Plain objects that describe what happened                           |
| 🔄 **Reducers**   | Pure functions that calculate the next state based on action        |
| 🧩 **Selectors**  | Functions that extract or derive data from the state                |
| 🧪 **Middleware** | Intercepts actions for side effects, logging, async ops, etc.       |

---

## 🔗 How Everything Connects

Here’s a breakdown of the **Redux data flow** and how each component fits in:

### 1. **Store**
- Created using `createStore(reducer, applyMiddleware(...middlewares))`
- Central hub for state and dispatching actions

### 2. **Action**
- Dispatched via `store.dispatch({ type: 'ACTION_TYPE', payload })`
- Describes what should change, but not how

### 3. **Middleware**
- Intercepts actions **before they reach reducers**
- Used for:
  - Logging
  - Async operations (e.g. API calls)
  - Conditional dispatching

### 4. **Reducer**
- Receives current state and action
- Returns a new state (pure function)
- Example:
  ```js
  function counterReducer(state = { count: 0 }, action) {
    switch (action.type) {
      case 'INCREMENT':
        return { count: state.count + 1 };
      default:
        return state;
    }
  }
  ```

### 5. **Selector**
- Encapsulates logic for extracting or deriving data from state
- Improves performance via memoization (e.g. with `reselect`)
- Example:
  ```js
  const selectTodos = state => state.todos;
  const selectCompletedTodos = createSelector(
    [selectTodos],
    todos => todos.filter(todo => todo.completed)
  );
  ```
---

## 🔁 Full Redux Flow

```
  UI 
  ↓
dispatch(action)
     ↓
Middleware intercepts (optional side effects)
     ↓
Reducer updates state
     ↓
Store holds new state
     ↓
Selector extracts/derives data
     ↓
UI re-renders with updated data
```

---


# Difference between Redux and Redux Toolkit (RTK)?

The **main difference between Redux and Redux Toolkit (RTK)** is that **RTK is a higher-level abstraction over Redux**, designed to simplify setup, reduce boilerplate, and enforce best practices — while still being 100% compatible with Redux.

Here’s a clear breakdown:

---

### **1. Setup and Boilerplate**

**Classic Redux** requires a lot of manual setup:

* Write action types as strings
* Write action creators manually
* Create reducers with `switch` statements
* Configure middleware manually

**Example (Classic Redux):**

```js
// Action Types
const INCREMENT = 'INCREMENT';

// Action Creator
const increment = () => ({ type: INCREMENT });

// Reducer
function counterReducer(state = { value: 0 }, action) {
  switch (action.type) {
    case INCREMENT:
      return { value: state.value + 1 };
    default:
      return state;
  }
}

// Store
import { createStore } from 'redux';
const store = createStore(counterReducer);
```

---

**Redux Toolkit** simplifies this with `createSlice` and `configureStore`:

```js
import { createSlice, configureStore } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1; },
  },
});

const store = configureStore({
  reducer: { counter: counterSlice.reducer },
});

store.dispatch(counterSlice.actions.increment());
```

✅ **Less code, fewer bugs, easier to read.**

---

### **2. Immutability**

* **Redux:** You must manually write immutable logic.
* **Redux Toolkit:** Uses **Immer** under the hood — you can write code that *looks mutable* but is safely immutable.

**Example with RTK:**

```js
increment: (state) => {
  state.value += 1; // Immer handles immutability
}
```

---

### **3. Middleware and DevTools**

| Feature        | Redux               | Redux Toolkit                    |
| -------------- | ------------------- | -------------------------------- |
| **Middleware** | Must add manually   | Preconfigured with `redux-thunk` |
| **DevTools**   | Manual setup needed | Enabled by default               |

---

### **4. Code Splitting and Scalability**

* Both support large applications.
* **RTK** makes dynamic reducer injection and scalable folder structures **much simpler**.

---

### **5. Learning Curve**

* **Redux:** Steeper, because you need to understand multiple low-level APIs (`createStore`, `combineReducers`, `applyMiddleware`, etc.).
* **RTK:** Beginner-friendly, but still allows access to the low-level Redux APIs if needed.

---

### **6. Bundle Size**

* Both are lightweight.
* **RTK** adds a small overhead for Immer and utilities, but the difference is negligible in real-world apps.

---

### **Summary Table**

| Feature        | **Redux (Classic)**     | **Redux Toolkit (RTK)**   |
| -------------- | ----------------------- | ------------------------- |
| Boilerplate    | High                    | Low                       |
| Immutability   | Manual                  | Automatic (via Immer)     |
| DevTools       | Manual setup            | Built-in                  |
| Async logic    | Middleware setup needed | Thunk included by default |
| Code splitting | Manual                  | Easier                    |
| Learning curve | Steeper                 | Easier                    |

---

### **When to Use**

* **Small or legacy apps:** Classic Redux is fine.
* **Modern apps (recommended):** Use Redux Toolkit for cleaner, safer, and faster development.

---


# Why Redux works with **immutable state**?


Redux works with **immutable state** because immutability enables **predictable state updates, easier debugging, efficient change detection, and reliable time-travel debugging**.
Let me break it down clearly:

---

### **1. Predictability and Pure Reducers**

* Redux reducers are **pure functions**.
* A reducer should not **mutate the existing state**; instead, it should **return a new state object**.
* Immutability ensures:

  * No accidental side effects
  * State transitions remain predictable and consistent

**Example**:

```js
// ❌ Mutable
state.user.name = "John";

// ✅ Immutable
return {
  ...state,
  user: { ...state.user, name: "John" }
};
```

---

### **2. Efficient Change Detection**

Redux checks if the state has changed using **reference equality** (`===`) instead of deep comparisons.

* If you mutate the object, the reference stays the same → Redux won’t detect changes.
* If you return a **new object** when state updates, Redux can easily detect that something changed.

**Example**:

```js
const prevState = { count: 1 };
const nextState = { count: 2 };

prevState === nextState; // false -> state updated
```

If you mutate:

```js
const state = { count: 1 };
state.count = 2;
state === state; // true -> Redux thinks nothing changed (bug!)
```

---

### **3. Time-Travel Debugging**

Redux DevTools uses **snapshots of state** for debugging and replaying actions.

* Immutability ensures each previous state is preserved without unexpected changes.
* This allows features like:

  * Undo/redo
  * State history inspection
  * Time travel debugging

---

### **4. Easier Testing and Debugging**

Immutable state means:

* No unexpected mutations during tests
* Easy to compare states by reference
* Makes unit testing reducers more reliable

---

### **5. Performance Optimizations**

UI libraries like **React** rely on shallow equality checks to decide if components should re-render.
Immutable updates allow:

* **Fast re-render decisions** without deep checks
* Performance boosts in large applications

**Example with React-Redux**:

```js
function mapStateToProps(state) {
  return {
    user: state.user
  };
}

// React only re-renders if state.user reference changes
```

---

### **Real-world helpers**

Developers often use libraries like:

* **Immer** (used in Redux Toolkit) → simplifies immutable updates
* **Immutable.js** (less common today) → enforces immutability with special data structures

Example with **Immer**:

```js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { count: 0 },
  reducers: {
    increment: (state) => {
      state.count += 1; // Looks mutable but Immer makes it immutable internally
    }
  }
});
```

---

# How bundle size impacts when we have lot of reducers?


Yes, having **200 reducers in a single Redux store** **can** impact bundle size and performance, but how much depends on **how you organize, load, and use them**.

Let’s break it down:

---

### **1. Impact on Bundle Size**

* Each reducer is just a function — small by itself — but **200 reducers together** can make your **bundle heavier**, especially if:

  * Each reducer imports heavy utility libraries.
  * Reducers are tightly coupled with large feature modules.
  * Everything is bundled together upfront (no code splitting).

**Example**:
If each reducer averages \~10 KB, then `10 KB × 200 = ~2 MB` just for reducer logic in the main bundle.

---

### **2. Impact on Performance**

#### **a) State Updates**

* Redux processes **every dispatched action** through **all reducers**.
* Even if 190 reducers don’t care about the action, they are still called.
* With 200 reducers, the overhead **adds up**, but usually this is still **very fast** unless reducers are doing heavy computations.

#### **b) DevTools Debugging**

* Large state trees (from many reducers) can make Redux DevTools slower when inspecting snapshots or time-travel debugging.

---

### **3. Best Practices to Handle Many Reducers**

#### **a) Use Feature-Based Slices**

Group related logic into **feature slices** instead of creating one reducer per micro-feature:

```js
// Instead of 5 reducers for todos
todosList, todosFilter, todosUI, todosMeta, todosStats
// Use one slice:
todosSlice
```

---

#### **b) Code Splitting (Lazy Loading Reducers)**

For large apps, dynamically load reducers when a feature is needed using `redux-dynamic-modules` or manual injection.

Example:

```js
import store from './store';
import userReducer from './features/userSlice';

// Inject dynamically when feature mounts
store.injectReducer('user', userReducer);
```

---

#### **c) Tree Shaking**

If your build setup supports tree-shaking (Webpack, Vite), unused reducers won’t be included in the final bundle.

---

#### **d) Keep Reducers Lightweight**

* Avoid heavy computations inside reducers.
* Move expensive logic to **selectors** or **middleware**.

---

### **4. Real-World Scenarios**

| App Size             | # of Reducers | Approach                                                  |
| -------------------- | ------------- | --------------------------------------------------------- |
| Small app            | 5–10          | Single store, static reducers                             |
| Medium app           | 20–50         | Feature slices, static store                              |
| Large enterprise app | 100+          | Feature slices + dynamic reducer injection + lazy loading |

---

### **Summary**

| **Aspect**      | **Impact with 200 reducers**                             | **Mitigation**                            |
| --------------- | -------------------------------------------------------- | ----------------------------------------- |
| **Bundle size** | Increases significantly if everything is bundled upfront | Use lazy loading, dynamic injection       |
| **Performance** | Slight slowdown during dispatch                          | Keep reducers simple, optimize selectors  |
| **DevTools**    | Can be slower with huge state trees                      | Limit history or disable DevTools in prod |

---
