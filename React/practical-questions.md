#  `useEffect` is said to handle “outside React things”

### 🌐 What counts as “outside” React?
React’s main job is to render UI based on state and props. But real-world apps need to do more than just render:
- Fetch data from APIs
- Set up timers or intervals
- Listen to browser events (like scroll or resize)
- Interact with local storage or cookies
- Subscribe to external services (like WebSockets)

These are **imperative tasks** that don’t belong in React’s declarative rendering flow. That’s where `useEffect` steps in.

---

# React - 🧠 Why `console.log` after `setState` (or `setE`) doesn't show the updated value

React state updates via `useState` are **asynchronous**. That means when you call `setState(newValue)`, React schedules the update but doesn't apply it immediately. So if you do:

```jsx
setState("new value");
console.log(e); // Still logs the old value
```

### ✅ How to properly observe the updated value

Here are a few ways to handle this:

#### 1. **Use `useEffect` to watch for changes**
```jsx
useEffect(() => {
  console.log("Updated value of e:", e);
}, [e]);
```
This will log the new value **after** React has re-rendered and applied the state update.

#### 2. **Use a callback pattern if needed**
If you're doing something immediately after setting state, consider restructuring your logic to happen inside `useEffect` or use a ref to track intermediate values.


## can we log that in timeout , will it work ?

⏱️ Logging inside a `setTimeout` **can** show the updated value — but only if React has completed the re-render **before** the timeout executes.

Here’s what’s going on:

### ✅ Example:
```jsx
const [state, setState] = useState("old");

const handleClick = () => {
  setState("new");
  setTimeout(() => {
    console.log("Inside timeout:", e);
  }, 0);
};
```

### ❗What you'll see:
Even with `setTimeout(..., 0)`, `e` might still log as `"old"` — because `e` is **closed over** from the current render. React hasn’t re-rendered yet, and the timeout is using the stale value.

### 🔍 Why?
- `setTimeout` doesn’t wait for React to re-render.
- The closure inside `setTimeout` captures the value of `e` **at the time `handleClick` runs**, not after the state updates.

----

# How CSS inherit or imports CSS works in react?

If I import css file in one component, so will its children auto read that all css without importing css in child components?

Great question — and the answer depends on **how** you're importing and structuring your CSS in React.

### 👕 Standard CSS (global styles)
If you're importing a regular CSS file like this:

```jsx
import './styles.css';
```

Then yes — all child components **will inherit** those styles **as long as the CSS selectors apply to them**. CSS is global by default, so once it's loaded into the DOM, any element that matches the selectors will be styled accordingly.

### 🧩 CSS Modules
If you're using **CSS Modules** (e.g., `styles.module.css`), then styles are **scoped locally** to the component:

```jsx
import styles from './MyComponent.module.css';

<div className={styles.container}>...</div>
```

In this case:
- Child components **won’t automatically inherit** styles unless you **pass down the class names** explicitly.
- This helps avoid style conflicts and is great for large apps.

### 🎨 Styled Components / CSS-in-JS
If you're using libraries like `styled-components` or `emotion`, styles are scoped to components and not globally inherited unless you design them to be.

---

# Why react-native-encrypted-storage is more secure then async storage to keep tokens or sensitive data?

Exactly — **tokens stored in `AsyncStorage` are *not secure*.**

Here’s why:

---

### **🔓 Why AsyncStorage is NOT secure**

* **Plaintext storage**
  Data is stored in plain text on the device file system. Anyone with **physical access** or using a malicious app can read it.

  * **Android:** Located in `data/data/<your_app>/files/`
  * **iOS:** In `Documents` or `Library` folders, easily accessible if the phone is jailbroken.

* **No encryption**
  AsyncStorage does not encrypt data. Anyone with access to device backups or a rooted device can see your tokens.

* **Risk of token theft**
  If your **JWT refresh token** or API keys are exposed, attackers can log in as the user or hijack sessions.

---

### **🔐 Why Use Encrypted Storage**

Using libraries like `react-native-encrypted-storage` or `expo-secure-store` ensures:

* Data is **encrypted** using device-native keystores.
* Access is restricted to your app only.
* On Android, uses the **Android Keystore** API.
* On iOS, uses the **Keychain**, which is very secure and optionally protected by biometrics.

---

### **Comparison**

| Feature                                      | AsyncStorage | EncryptedStorage |
| -------------------------------------------- | ------------ | ---------------- |
| Encryption                                   | ❌ No         | ✅ Yes            |
| Secure against device theft                  | ❌ No         | ✅ Yes            |
| Ideal for sensitive data (tokens, passwords) | ❌ No         | ✅ Yes            |
| Use for temporary, non-critical data         | ✅ Yes        | ⚠️ Overkill      |

---

### **Best Practice**

* Use **AsyncStorage** for non-sensitive data (e.g., app theme, UI preferences).
* Use **EncryptedStorage** or **SecureStore** for sensitive data like:

  * Auth tokens (JWT, OAuth)
  * PIN codes or credentials
  * Encrypted user settings

---

# Why use 'use' in Custom hook ?


----

# useActionState hook?


# Controlled vs Uncontrolled Component & Is there any performance related to these components?


# some packages are in react-dom and some are in react? any idea on this?
- Example useactionstate in react and useFormStatus is in react-dom.


# can we set some transformed value to input using useref ?

Absolutely — you can use useRef to directly manipulate an input element and set a transformed value. This is especially handy when you want to bypass React’s re-render cycle for quick DOM updates.

```js
 const original = inputRef.current.value;
    const transformed = original.toUpperCase(); // Example transformation
    inputRef.current.value = transformed;
```

✅ What useRef is good for - 
- Accessing DOM elements directly (e.g., focusing an input, scrolling to a section)
- Storing mutable values that don’t trigger re-renders (e.g., timers, previous values)
- Avoiding unnecessary state updates when you just need to hold data temporarily


----


# Why Redux store are immutable ?


---



