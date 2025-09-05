# Use of key in View and flat list ?

# Use of comparator function with useMemo ?

# setState(count + 1) vs setState(pre => pre + 1); ?

This behavior is tied directly to **React's batching mechanism**.

### 🔁 What Happens with `setState(count + 1)` Called 5 Times

If you do something like:

```tsx
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
```

React sees all five updates as **identical** because `count` hasn’t changed yet — it’s still the same value during that synchronous execution. So instead of incrementing 5 times, it only applies the **last one**, which is still `count + 1`.

### ✅ Why `setCount(prev => prev + 1)` Works

```tsx
setCount(prev => prev + 1);
setCount(prev => prev + 1);
setCount(prev => prev + 1);
setCount(prev => prev + 1);
setCount(prev => prev + 1);
```

Here, each update uses the **latest value of state**, even within the same render cycle. React queues them and applies them one after another, resulting in a proper increment of +5.

---

So when you're updating state based on its previous value — especially in loops, effects, or rapid interactions — **always use the functional form**.

--------


# File name js vs jsx difference ?
---

### 🧠 `.js` vs `.jsx`: What’s the Difference?

| Extension | Meaning | Typical Usage | JSX Allowed? |
|-----------|--------|----------------|--------------|
| `.js`     | JavaScript | Plain logic, utility functions, config files | ✅ Yes, but not explicitly |
| `.jsx`    | JavaScript + XML | React components that include JSX syntax | ✅ Yes, explicitly intended |

---

### 🔍 Why Use `.jsx`?

- Signals that the file **contains JSX syntax** (e.g., `<View>`, `<Text>`, etc.)
- Improves readability and tooling support in some editors
- Useful for distinguishing between logic-only files and UI components

---

### ⚙️ In Practice

React projects often use both:
- `App.jsx` → Main component with JSX
- `utils.js` → Helper functions, no JSX
- `config.js` → Constants or settings

But if you're using **TypeScript**, the equivalent extensions are:
- `.ts` → TypeScript without JSX
- `.tsx` → TypeScript with JSX

So in a React Native + TypeScript project, you'd typically use `.tsx` for components and `.ts` for logic.

---

### 🧩 Final Tip

You **can** write JSX in `.js` files, and many projects do. But using `.jsx` (or `.tsx` in TypeScript) makes your intent clearer and helps with tooling, especially in large codebases.

----------

# Why jsx must have a parene element?

In **JSX**, every return statement must have **one parent element** because of **how JSX is compiled under the hood**.

Here’s the reasoning:

---

### **1️⃣ How JSX Works**

JSX isn’t HTML — it’s **syntactic sugar** for `React.createElement`.

Example:

```jsx
return (
  <h1>Hello</h1>
  <p>World</p>
);
```

Internally, React tries to compile this into:

```js
return (
  React.createElement("h1", null, "Hello")
  React.createElement("p", null, "World")
);
```

🚨 **Error:** JavaScript functions can **only return one value**, not multiple sibling elements.

---

### **2️⃣ The Fix → Wrap with One Parent**

You need **one root wrapper**, like:

```jsx
return (
  <div>
    <h1>Hello</h1>
    <p>World</p>
  </div>
);
```

Now React compiles this into one tree:

```js
return React.createElement("div", null,
  React.createElement("h1", null, "Hello"),
  React.createElement("p", null, "World")
);
```
---

# How unmounting works with navigation stack ? unmount remove component from tree or not?

# Scrollable View vs FlatList?

# How to view console.log with emulator?

Run command > npx react-native log-android

# Why Use a Custom Comparison Function in React memo?

By default, `React.memo` does a **shallow comparison** of props. This works fine for primitive values (`string`, `number`, `boolean`) but can fail for:

- 🧩 **Objects**
- 📦 **Arrays**
- 🧵 **Functions**

These are compared by reference, not by value — so even if the contents are the same, a new reference causes a re-render.

---

## 🔍 When You Need a Custom Comparison Function

Here are the most common cases:

### 1. **Props Are Objects or Arrays**

```jsx
const Child = memo(({ user }) => {
  return <Text>{user.name}</Text>;
}, (prevProps, nextProps) => {
  return prevProps.user.name === nextProps.user.name;
});
```

Even if `user` has the same data, a new object reference will trigger a re-render unless you compare values manually.

---

### 2. **Props Include Functions**

```jsx
const Child = memo(({ onClick }) => {
  return <Button title="Click" onPress={onClick} />;
}, (prevProps, nextProps) => {
  return prevProps.onClick === nextProps.onClick;
});
```

If `onClick` is re-created on every render (e.g., inline arrow function), it will cause re-renders. You can either memoize the function with `useCallback`, or use a custom comparison.

---

### 3. **Selective Prop Comparison**

If your component receives many props but only depends on a few:

```jsx
const Child = memo(({ a, b, c }) => {
  return <Text>{a}</Text>;
}, (prev, next) => prev.a === next.a);
```

This avoids unnecessary re-renders when `b` or `c` change but `a` stays the same.

---

### 4. **Performance Optimization in Large Trees**

In complex UIs, even small re-renders can cascade. Custom comparison functions help you **fine-tune** rendering behavior.

---

# Why can't we use forEach to iterate and render list? I understand map returns a new array and forEach doesn't?

You're right — the **main reason** is that `forEach` **doesn’t return anything**, while `map` **returns a new array**, which React can render as a list of elements.

Let’s break it down:

---

### **1️⃣ React needs an array of elements**

In React, when you want to render a list like:

```tsx
{items.map(item => (
  <li key={item.id}>{item.name}</li>
))}
```

`map` returns a **new array of JSX elements**, which React can take and render directly.

---

### **2️⃣ Why `forEach` doesn't work**

Example:

```tsx
const items = ['Apple', 'Banana', 'Cherry'];

const list = items.forEach(item => <li>{item}</li>);

console.log(list); // undefined
```

`forEach` doesn’t return anything (it always returns `undefined`), so there’s **nothing for React to render**.

---

### **3️⃣ How to still use `forEach` (not recommended)**

You could manually push elements into an array, but it’s extra work:

```tsx
const elements: JSX.Element[] = [];
items.forEach(item => {
  elements.push(<li key={item}>{item}</li>);
});

return <ul>{elements}</ul>;
```

This works, but is **less clean** than just using `map`.

Yes! While `.map()` is the **cleanest and most common** way to render lists in React, you can use other approaches too, depending on your coding style or specific needs.

Here are some **alternatives to `map`**:

---

### **2️⃣ Using `reduce`**

You can accumulate JSX elements into an array:

```tsx
const items = ['Apple', 'Banana', 'Cherry'];

function FruitList() {
  const elements = items.reduce<JSX.Element[]>((acc, item) => {
    acc.push(<li key={item}>{item}</li>);
    return acc;
  }, []);

  return <ul>{elements}</ul>;
}
```

✔ **Good for**: When you need to **transform** data while building the JSX list.

---

### **3️⃣ Using `Array.from`**

`Array.from` can create an array of JSX elements:

```tsx
const items = ['Apple', 'Banana', 'Cherry'];

function FruitList() {
  return (
    <ul>
      {Array.from(items, item => <li key={item}>{item}</li>)}
    </ul>
  );
}
```

✔ **Good for**: Cleaner syntax but less common than `.map`.

---

### **4️⃣ Using a `while` loop**

Rarely used, but possible:

```tsx
const items = ['Apple', 'Banana', 'Cherry'];

function FruitList() {
  const elements: JSX.Element[] = [];
  let i = 0;
  while (i < items.length) {
    elements.push(<li key={items[i]}>{items[i]}</li>);
    i++;
  }
  return <ul>{elements}</ul>;
}
```

✔ **Good for**: When iterating dynamically with complex conditions.



---

### **Why `map` is still preferred**

* Cleaner and concise
* Functional programming style
* Easy to combine with `.filter()` or `.sort()`
* Works naturally with React rendering patterns

-----------

# Why Terniary opeartor (? : ) does not re render same component two times correctly ?

Great question — and it's one that trips up many developers! The ternary operator itself **doesn't prevent re-rendering**, but the way it's used **can lead to unexpected rendering behavior** if you're not careful.

Let’s break it down:

---

### ❓ **What Is the Ternary Operator Doing?**
In JSX, the ternary operator is often used for conditional rendering:

```jsx
{condition ? <ComponentA /> : <ComponentB />}
```

This works perfectly **as long as the condition changes**, and React can tell that the output should be different.

---

### ⚠️ **Why It Might Not Re-render Properly**

#### 1. **Same Component, Different Props — But React Reuses It**
If you’re rendering the **same component** with different props:

```jsx
{isTrue ? <MyComponent value="A" /> : <MyComponent value="B" />}
```

React sees `<MyComponent />` in both branches and may **reuse the component instance**, only updating props — not remounting or resetting state.

> This can lead to stale state or unexpected behavior if `MyComponent` uses `useState` or `useEffect` that depends on props.

---

#### 2. **Missing `key` Prop**
React uses the `key` prop to determine whether to **remount** a component. Without it, React may reuse the component even if the ternary condition changes.

✅ **Fix**:

```jsx
{isTrue ? <MyComponent key="A" value="A" /> : <MyComponent key="B" value="B" />}
```

> This forces React to treat them as **distinct components**, triggering a full re-render.

---

#### 3. **State Not Synced with Props**
If your component uses `useState` initialized from props:

```jsx
const MyComponent = ({ value }) => {
  const [state, setState] = useState(value);
  return <div>{state}</div>;
};
```

Changing `value` via the ternary won’t reset `state` unless you manually sync it with `useEffect`.

✅ **Fix**:

```jsx
useEffect(() => {
  setState(value);
}, [value]);
```

---

### ✅ **Best Practices for Ternary Rendering**

| Situation | Recommendation |
|----------|----------------|
| Same component, different props | Use `key` prop or lift state up |
| Different components | Ternary works fine |
| Complex logic | Consider using `if` statements or helper functions for clarity |

---

# Multiple use state or useReducer to work on single state value ?
---

## ⚖️ **Comparison: `useState` vs `useReducer`**

| Feature | `useState` | `useReducer` |
|--------|------------|--------------|
| 🔧 **Simplicity** | Great for simple, isolated state values | Better for complex state logic |
| 🧩 **Multiple State Values** | Use multiple `useState` calls | Combine all state into one object |
| 🔁 **State Transitions** | Manual updates per state | Centralized logic via reducer function |
| 🧠 **Readability** | Easy to read for small components | Easier to manage in large components |
| 🧪 **Debugging** | Can be harder with scattered state | Easier with predictable actions |
| 🧵 **Performance** | Fine for most cases | Slightly better batching in some cases |

---

# Can we update context from any Child component that is working inside context provider?

Yes, absolutely! In React, if a component is wrapped inside a **Context Provider**, any of its **descendant (child) components** can **read from and update** the context — as long as the context provides a way to do so. 🔄

---

## 🧠 How Context Updates Work

React's Context API allows you to share state across components without prop drilling. To **update context from a child**, you typically:

1. **Create a context** with both state and an updater function.
2. **Provide** that context at a higher level.
3. **Consume** the context in child components and call the updater.

---

### 🛠️ Example: Theme Context

```jsx
// 1. Create context
const ThemeContext = React.createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = React.useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}
```

```jsx
// 2. Child component updates context
function ThemeToggle() {
  const { theme, setTheme } = React.useContext(ThemeContext);

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Toggle Theme
    </button>
  );
}
```
----

# If context have 10 values inside object and only one value we are updating, so will all child component render or not ?

---

## 🧩 Scenario Recap

You have a context like this:

```jsx
const AppContext = React.createContext();

function AppProvider({ children }) {
  const [state, setState] = React.useState({
    user: null,
    theme: 'light',
    language: 'en',
    notifications: [],
    // ...6 more values
  });

  return (
    <AppContext.Provider value={{ state, setState }}>
      {children}
    </AppContext.Provider>
  );
}
```

Now, if you update **just one field** (say `theme`), like this:

```jsx
setState(prev => ({ ...prev, theme: 'dark' }));
```

---

## 🔥 Will All Child Components Re-render?

Yes — **all components that consume the context will re-render**, even if they only use a part of the context that **didn't change**.

Why? Because React compares the **entire context value object** (`{ state, setState }`), and if it's a new reference, it triggers re-renders in all consumers.

---

## 🛠️ How to Optimize This

### ✅ 1. **Split Contexts**
Instead of one big context, create **multiple smaller contexts** for logically grouped values:

```jsx
const ThemeContext = React.createContext();
const UserContext = React.createContext();
```

This way, updating `theme` only affects components using `ThemeContext`.

---

### ✅ 2. **Memoize Context Value**
Use `useMemo` to prevent unnecessary re-renders:

```jsx
const value = React.useMemo(() => ({ state, setState }), [state]);
```

But this only helps if you're **not updating the entire state object**. If you do, the memoized value still changes.

---

Ah, now we’re getting into the **subtleties of React rendering behavior** — and the answer is:

> **No**, a child component that does **not consume** the context will **not re-render** just because the context value changed.

---

## ✅ When a Child **Does Not Use Context**

If a component is inside a `<Context.Provider>` but **doesn't call `useContext()`** or access the context via `Context.Consumer`, it’s **not subscribed** to context updates.

So even if the context value changes, React **won’t re-render** that component — unless:

- Its **own props or state** change
- Its **parent re-renders and passes new props** to it

---

### 🧪 Example

```jsx
function App() {
  return (
    <AppProvider>
      <ChildA /> {/* Uses context */}
      <ChildB /> {/* Does NOT use context */}
    </AppProvider>
  );
}

function ChildA() {
  const { state } = React.useContext(AppContext);
  return <div>{state.theme}</div>;
}

function ChildB() {
  console.log('ChildB rendered');
  return <div>I don’t care about context</div>;
}
```

> If you update `state.theme`, only `ChildA` re-renders. `ChildB` stays untouched — unless `App` itself re-renders and causes a ripple.

---

# If redux store data is 5 mb. then how it manage performance on mobile app ?

Excellent question — and it touches on a real-world concern for mobile apps: **performance vs. payload size**. If your Redux store holds **5 MB of data**, that’s quite large, but whether it’s a problem depends on **how you structure, access, and update** that data.

Let’s break it down:

---

## 📦 Is 5 MB Too Much?

- 🔍 **5 MB in memory** isn’t inherently bad — modern mobile devices can handle it.
- 🚫 But if you're **frequently updating or re-rendering** large chunks of that store, you could run into performance issues like:
  - Slow UI updates
  - Janky animations
  - Increased battery usage
  - Lag on lower-end devices

---

## 🧠 Key Performance Factors

### ✅ 1. **State Shape Matters**
- Flat, normalized state is easier to manage.
- Avoid deeply nested objects — they’re harder to update efficiently.

### ✅ 2. **Selective Access**
Use **selectors** to access only the data a component needs:

```js
const user = useSelector(state => state.users.byId[userId]);
```

> This prevents unnecessary re-renders when unrelated parts of the store change.

### ✅ 3. **Memoization**
Use `React.memo`, `useMemo`, and `reselect` to avoid recalculating or re-rendering unnecessarily.

---

### ✅ 4. **Pagination & Lazy Loading**
Don’t load all 5 MB at once. For example:
- Load only visible items in a list
- Fetch additional data on scroll or interaction

---

### ✅ 5. **Avoid Storing Non-Essential Data**
Don’t store large blobs like:
- Images
- Videos
- Raw HTML
- Logs or analytics

Instead, store references or IDs and fetch the heavy stuff when needed.

---

## 📱 Mobile-Specific Tips

- Use **React Native’s FlatList** with `initialNumToRender`, `windowSize`, and `getItemLayout` for large lists.
- Avoid triggering global store updates from frequently changing UI elements (like text inputs).
- Profile with tools like **Flipper**, **React DevTools**, and **Redux DevTools** to spot bottlenecks.

---
