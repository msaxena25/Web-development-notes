# Style in React web

In a React application, there are **multiple ways to apply CSS styles**, each with its own use case, pros, and cons. Here's a breakdown of the most common methods:

---

### 🔹 1. **CSS Stylesheets (Global CSS)**

**How:** Write regular `.css` files and import them into your components.

```js
import './App.css';

function App() {
  return <div className="app-container">Hello</div>;
}
```

✅ Pros:

* Simple and familiar.
* Good for global styles or legacy projects.

❌ Cons:

* Styles are global and can clash (no scoping).
* Not ideal for large projects.

---

### 🔹 2. **CSS Modules**

**How:** Create `.module.css` files to scope styles locally to a component.

```js
import styles from './App.module.css';

function App() {
  return <div className={styles.appContainer}>Hello</div>;
}
```

✅ Pros:

* Scoped styles (no naming conflicts).
* Works with traditional CSS syntax.

❌ Cons:

* Slightly more boilerplate.
* No dynamic styling (you still need class-based conditionals).

---

### 🔹 3. **Inline Styles**

**How:** Use `style={{}}` attribute in JSX.

```js
function App() {
  return <div style={{ color: 'red', fontSize: '20px' }}>Hello</div>;
}
```

✅ Pros:

* Easy for dynamic or conditional styles.
* No external files needed.

❌ Cons:

* No support for pseudo-classes (`:hover`, `:focus`) or media queries.
* Not DRY for large styles.

---

### 🔹 4. **Styled Components (CSS-in-JS with `styled-components`)**

**How:** Use tagged template literals to style components.

```js
import styled from 'styled-components';

const Container = styled.div`
  color: red;
  font-size: 20px;
`;

function App() {
  return <Container>Hello</Container>;
}
```

✅ Pros:

* Fully scoped styles.
* Dynamic styling with props.
* Supports nesting, media queries, etc.

❌ Cons:

* Runtime overhead.
* Learning curve for some developers.

---

### 🔹 5. **Emotion (Another CSS-in-JS Library)**

Similar to `styled-components`, with slightly different syntax and performance benefits.

```js
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';

const style = css`
  color: blue;
`;

function App() {
  return <div css={style}>Hello</div>;
}
```

---

### 🔹 6. **Tailwind CSS (Utility-First Framework)**

**How:** Use utility classes directly in JSX.

```js
function App() {
  return <div className="text-red-500 text-xl">Hello</div>;
}
```

✅ Pros:

* Fast to build UI without writing custom CSS.
* Highly consistent.

❌ Cons:

* Long class names in JSX.
* Requires learning Tailwind syntax.

---

### 🔹 7. **SASS/SCSS**

**How:** Use `.scss` or `.sass` files with a build tool (like Webpack or Vite).

```scss
// styles.scss
$main-color: red;

.container {
  color: $main-color;
}
```

```js
import './styles.scss';
```

✅ Pros:

* Powerful features (variables, nesting, mixins).
* Familiar for those from traditional CSS background.

❌ Cons:

* Still global unless combined with CSS modules.

---

### 🔹 8. **Vanilla Extract / Linaria (Zero-runtime CSS-in-JS)**

These compile styles at build-time, so there’s no runtime cost.

✅ Pros:

* Type-safe, scoped styles.
* Zero runtime overhead.

❌ Cons:

* More setup required.
* Not as widely used as others.

---

### ✅ Summary Table

| Method               | Scoped? | Dynamic? | Supports Pseudo? | Best For                               |
| -------------------- | ------- | -------- | ---------------- | -------------------------------------- |
| Global CSS           | ❌       | ❌        | ✅                | Simple, legacy styles                  |
| CSS Modules          | ✅       | ❌        | ✅                | Medium projects, scoped styles         |
| Inline Styles        | ✅       | ✅        | ❌                | Quick dynamic styling                  |
| Styled Components    | ✅       | ✅        | ✅                | Component-driven styling               |
| Emotion              | ✅       | ✅        | ✅                | Type-safe CSS-in-JS                    |
| Tailwind CSS         | ✅       | ✅        | ✅                | Utility-based styling, rapid dev       |
| SASS/SCSS            | ❌       | ❌        | ✅                | Advanced traditional CSS with features |
| Vanilla Extract etc. | ✅       | ✅        | ✅                | Large apps, type-safe and performant   |

---

### if you create a file named **`abc.module.scss`**, **it *will be scoped*** — just like `abc.module.css`.

### ✅ What "scoped" means here:

It means the CSS classes defined in `abc.module.scss` will only apply **locally** to the component that imports and uses them — avoiding global style clashes.

---

### 🔧 Example:

**`abc.module.scss`**

```scss
.myButton {
  background-color: red;
  color: white;
}
```

**`MyComponent.jsx`**

```jsx
import styles from './abc.module.scss';

function MyComponent() {
  return <button className={styles.myButton}>Click Me</button>;
}
```

### ✅ What you get:

React + Webpack (or Vite) compiles that `.module.scss` file and gives your class a unique hashed name like:

```html
<button class="MyComponent_myButton__a1b2c">Click Me</button>
```

---

### 📦 Requirements:

Make sure:

* Your project has the appropriate **SASS loader** installed:

  ```bash
  npm install sass
  ```
* Your React setup supports CSS Modules (Create React App, Next.js, Vite, etc. do this out of the box).

---

# Style in React Native

In **React Native**, styling is quite different from React (web), as it doesn’t use traditional CSS files. Instead, it uses a JavaScript-based styling system that's optimized for mobile performance.

Here are the **main ways to style in React Native**:

---

### 🔹 1. **Inline Styles**

Use the `style` prop directly with a JavaScript object.

```jsx
<View style={{ backgroundColor: 'red', padding: 10 }}>
  <Text style={{ color: 'white' }}>Hello</Text>
</View>
```

✅ Pros:

* Simple for quick styling.
* Good for dynamic styles.

❌ Cons:

* Not reusable.
* Harder to manage in large components.

---

### 🔹 2. **`StyleSheet.create` (Recommended)**

Use `StyleSheet` from `react-native` to create a reusable, performance-optimized stylesheet.

```jsx
import { StyleSheet, View, Text } from 'react-native';

const styles = StyleSheet.create({
  container: {
    backgroundColor: 'red',
    padding: 10,
  },
  text: {
    color: 'white',
  },
});

function MyComponent() {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Hello</Text>
    </View>
  );
}
```

✅ Pros:

* Better performance (validated and optimized at runtime).
* Cleaner and organized.

❌ Cons:

* Slightly more verbose than inline.

---

### 🔹 3. **Dynamic Styles with Props/State**

You can create conditional styles dynamically.

```jsx
<View style={[styles.button, isActive && styles.activeButton]} />
```

Or pass dynamic values directly:

```jsx
<Text style={{ color: isDarkMode ? 'white' : 'black' }}>Hello</Text>
```

✅ Good for:

* Themes, toggles, states like "selected", "error", etc.

---

### 🔹 4. **External Style Libraries**

You can use libraries that make styling more powerful or familiar:

#### a. **Styled Components (for React Native)**

```bash
npm install styled-components
```

```jsx
import styled from 'styled-components/native';

const Container = styled.View`
  background-color: red;
  padding: 10px;
`;

const Label = styled.Text`
  color: white;
`;

function App() {
  return (
    <Container>
      <Label>Hello</Label>
    </Container>
  );
}
```

✅ Pros:

* Familiar syntax (especially for web developers).
* Theme support, props-based styles.

---

#### b. **Tailwind for React Native (e.g., `tailwind-rn`, `nativewind`)**

Example using NativeWind:

```bash
npm install nativewind
```

```jsx
import { Text, View } from 'react-native';

function App() {
  return (
    <View className="bg-red-500 p-4">
      <Text className="text-white">Hello</Text>
    </View>
  );
}
```

✅ Pros:

* Utility-first styling.
* Fast prototyping.

❌ Cons:

* Learning curve for Tailwind syntax.

---

### 🔹 5. **Theming with Context or Libraries**

Use libraries like:

* `react-native-paper` (Material Design)
* `react-native-elements`
* `styled-components` (with ThemeProvider)

Useful for building design systems or dark/light modes.

---

### ✅ Summary Table

| Method                        | Reusable? | Performance | Dynamic? | Notes                               |
| ----------------------------- | --------- | ----------- | -------- | ----------------------------------- |
| Inline Styles                 | ❌         | 👍 Medium   | ✅        | Simple, good for quick styles       |
| `StyleSheet.create`           | ✅         | 👍 High     | ✅        | Most common/recommended way         |
| Styled Components             | ✅         | 👍 Medium   | ✅        | Cleaner syntax, good theming        |
| Tailwind-style (`nativewind`) | ✅         | 👍 High     | ✅        | Utility-first, fast prototyping     |
| External Libraries (UI Kits)  | ✅         | 👍 High     | ✅        | Pre-built components, consistent UI |

---

#❓ Why React Native doesn't use traditional CSS?

React Native **doesn’t use traditional CSS** because it's fundamentally different from web development — it doesn't run in a browser and doesn't use the DOM. Here’s a breakdown of **why**:

### 🔧 1. **No HTML / DOM in React Native**

React Native renders to **native mobile components** (e.g., `<View>` maps to `UIView` in iOS, or `android.view.View` in Android), **not** HTML elements like `<div>`, `<span>`, etc.

➡️ Since there's no browser or DOM, the **browser’s CSS engine isn't available**.

---

### 🧱 2. **CSS is a browser technology**

Traditional CSS is deeply tied to the **Document Object Model (DOM)** and browser layout engine (e.g., WebKit, Blink).

React Native uses a layout engine called **Yoga** (built by Facebook) which interprets a **subset of CSS-like properties** (like `flexDirection`, `justifyContent`, etc.) but it’s not CSS — it’s just JavaScript style objects.

---

### 📦 3. **Performance and Consistency**

React Native uses `StyleSheet.create()` to **optimize styles** at runtime. These styles are:

* Flattened
* Interned (for performance)
* Validated at runtime

✅ This gives better performance on mobile compared to parsing and applying raw CSS strings.

---

### 🧑‍💻 4. **Uniform styling in JS**

Everything in React Native is JavaScript — including styles. This provides:

* **Dynamic styling** (styles can depend on props, state, platform, etc.)
* **Code sharing** (between styles and logic)
* Avoids switching between JS and CSS files

---

### 🎯 5. **Scoped and Predictable**

In web, CSS can "leak" globally unless you use techniques like BEM, CSS Modules, etc.
In React Native, styles are **always scoped to a component**, so no accidental conflicts.

---
