# Higher-Order Components (HOCs)

Higher-Order Components (HOCs) are an advanced technique in React for **reusing component logic**. A HOC is essentially a function that takes a component as an argument and returns a new (enhanced) component.

**Concept:**

Think of it like a decorator or a wrapper function. Instead of directly extending a component's class or manipulating its internal state, a HOC wraps it, providing extra props, state, or behavior.

**Signature:**

`const EnhancedComponent = higherOrderComponent(WrappedComponent);`

**Why use HOCs?**

* **Code Reusability:** Share common logic (e.g., data fetching, authentication checks, form handling) across multiple components without duplicating code.
* **Separation of Concerns:** Keep reusable logic separate from the presentational component, making both cleaner and easier to maintain.
* **Cross-Cutting Concerns:** Address concerns that affect multiple parts of your application (like logging, authentication).

**What HOCs are NOT:**

* They are **not** components themselves. They are functions that *return* components.
* They don't modify the wrapped component. They return a new component that wraps the original.

---

## Code Example: `withAuth` HOC

Let's create a HOC called `withAuth` that checks if a user is authenticated. If they are, it renders the wrapped component; otherwise, it redirects them to a login page.

**1. A simple authentication service (for demonstration):**

```jsx
// src/services/authService.js
const authService = {
  isAuthenticated: () => {
    // In a real app, check localStorage for a token or a user session
    return localStorage.getItem('userToken') === 'some_secret_token';
  },
  login: () => {
    localStorage.setItem('userToken', 'some_secret_token');
    console.log("User logged in.");
  },
  logout: () => {
    localStorage.removeItem('userToken');
    console.log("User logged out.");
  }
};

export default authService;
```

**2. The Higher-Order Component (`withAuth.js`):**

```jsx
// src/hocs/withAuth.js
import React from 'react';
import { useNavigate } from 'react-router-dom';
import authService from '../services/authService'; // Your authentication service

// withAuth is a function that takes a Component...
const withAuth = (WrappedComponent) => {
  // ...and returns a new component (the HOC)
  const AuthWrapper = (props) => {
    const navigate = useNavigate();
    const isAuthenticated = authService.isAuthenticated();

    // Check authentication status
    if (!isAuthenticated) {
      // If not authenticated, redirect to login
      React.useEffect(() => {
        navigate('/login', { replace: true });
      }, [navigate]); // Dependency array ensures effect runs only if navigate changes
      return null; // Don't render the wrapped component yet
    }

    // If authenticated, render the WrappedComponent with all its props
    return <WrappedComponent {...props} />;
  };

  // Optional: Give the new component a display name for better debugging
  AuthWrapper.displayName = `withAuth(${getDisplayName(WrappedComponent)})`;

  return AuthWrapper;
};

// Helper function to get component's display name for debugging
function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}

export default withAuth;
```

**3. Components to be wrapped (secured):**

```jsx
// src/pages/DashboardPage.js
import React from 'react';
import withAuth from '../hocs/withAuth'; // Import the HOC

function DashboardPage() {
  return (
    <div>
      <h1>Welcome to the Dashboard!</h1>
      <p>This content is only visible to authenticated users.</p>
      {/* ... dashboard specific content */}
    </div>
  );
}

// Wrap the DashboardPage with the withAuth HOC
export default withAuth(DashboardPage);
```

```jsx
// src/pages/ProfilePage.js
import React from 'react';
import withAuth from '../hocs/withAuth';

function ProfilePage() {
  return (
    <div>
      <h2>Your Profile</h2>
      <p>Manage your personal information here.</p>
    </div>
  );
}

export default withAuth(ProfilePage);
```

**4. A public Login Page (for navigation):**

```jsx
// src/pages/LoginPage.js
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import authService from '../services/authService';

function LoginPage() {
  const [password, setPassword] = useState('');
  const navigate = useNavigate();

  const handleLogin = () => {
    // Simple mock login logic: Any password works
    if (password === 'password') {
      authService.login();
      navigate('/dashboard', { replace: true }); // Redirect to dashboard on success
    } else {
      alert('Invalid password!');
    }
  };

  return (
    <div>
      <h2>Login</h2>
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Enter password"
      />
      <button onClick={handleLogin}>Login</button>
      <p>Use "password" to log in.</p>
    </div>
  );
}

export default LoginPage;
```

While HOCs are a powerful pattern, with the advent of React Hooks, many use cases for HOCs can now be more simply and cleanly achieved using **custom hooks**. However, HOCs still have their place, especially for specific cross-cutting concerns or when dealing with class components.

-----------

## Example: `withDataFetching` HOC


Okay, let's create another HOC example where we not only reuse logic but also **inject additional props** into the wrapped component.

This is a very common use case for HOCs: to abstract away data fetching or context consumption and provide derived data as props to the presentational component.

### Example: `withDataFetching` HOC

We'll create a HOC that fetches data from an API and passes that data (along with `loading` and `error` states) as props to the wrapped component.

**1. A simple data service (mock API):**

```jsx
// src/services/dataService.js
const dataService = {
  fetchProducts: async () => {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve([
          { id: 1, name: 'Laptop', price: 1200 },
          { id: 2, name: 'Mouse', price: 25 },
          { id: 3, name: 'Keyboard', price: 75 },
        ]);
      }, 1000); // Simulate network delay
    });
  },

  fetchUsers: async () => {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve([
          { id: 101, name: 'Alice' },
          { id: 102, name: 'Bob' },
        ]);
      }, 800); // Simulate network delay
    });
  }
};

export default dataService;
```

**2. The Higher-Order Component (`withDataFetching.js`):**

This HOC will take two arguments: the `WrappedComponent` and a `fetchFunction` (which determines *what* data to fetch).

```jsx
// src/hocs/withDataFetching.js
import React, { useState, useEffect } from 'react';

// withDataFetching is a function that takes a fetchFunction and returns another function
const withDataFetching = (fetchFunction) => (WrappedComponent) => {
  // This inner function returns the HOC component
  const DataFetcher = (props) => {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
      const fetchData = async () => {
        try {
          setLoading(true);
          setError(null);
          const result = await fetchFunction(); // Execute the provided fetch function
          setData(result);
        } catch (err) {
          console.error("Error fetching data:", err);
          setError("Failed to load data.");
        } finally {
          setLoading(false);
        }
      };

      fetchData();
    }, [fetchFunction]); // Re-run if the fetch function itself changes (rare, but good practice)

    // Now, render the WrappedComponent and pass the fetched data, loading, and error as new props
    return (
      <WrappedComponent
        data={data}
        loading={loading}
        error={error}
        {...props} // Pass through any original props received by DataFetcher
      />
    );
  };

  // Optional: For better debugging in React DevTools
  DataFetcher.displayName = `withDataFetching(${getDisplayName(WrappedComponent)})`;

  return DataFetcher;
};

// Helper function (same as previous example)
function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}

export default withDataFetching;
```

**3. Components that consume the injected props:**

These components don't need to know *how* the data is fetched; they just expect `data`, `loading`, and `error` as props.

```jsx
// src/components/ProductList.js
import React from 'react';
import withDataFetching from '../hocs/withDataFetching';
import dataService from '../services/dataService'; // Our data service

function ProductList({ data: products, loading, error }) {
  if (loading) return <div>Loading products...</div>;
  if (error) return <div style={{ color: 'red' }}>Error: {error}</div>;
  if (!products || products.length === 0) return <div>No products available.</div>;

  return (
    <div>
      <h2>Our Products</h2>
      <ul>
        {products.map(product => (
          <li key={product.id}>
            {product.name} - ${product.price.toFixed(2)}
          </li>
        ))}
      </ul>
    </div>
  );
}

// Wrap ProductList with withDataFetching, providing the specific fetch function
// Now ProductList receives 'data', 'loading', 'error' props automatically
export default withDataFetching(dataService.fetchProducts)(ProductList);
```

```jsx
// src/components/UserList.js
import React from 'react';
import withDataFetching from '../hocs/withDataFetching';
import dataService from '../services/dataService';

function UserList({ data: users, loading, error }) {
  if (loading) return <div>Loading users...</div>;
  if (error) return <div style={{ color: 'red' }}>Error: {error}</div>;
  if (!users || users.length === 0) return <div>No users found.</div>;

  return (
    <div>
      <h2>Registered Users</h2>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

// Wrap UserList with withDataFetching, providing the specific fetch function
export default withDataFetching(dataService.fetchUsers)(UserList);
```

---------------


# ✅ What are **Error Boundaries** in React?

**Error Boundaries** are React components that catch **JavaScript errors in their child component tree**, log those errors, and display a fallback UI instead of crashing the whole app.

---

### ⚠️ Why use Error Boundaries?

* Prevent entire UI from breaking if one part crashes
* Improve user experience with fallback UIs
* Useful in production apps for logging and recovery

---

### 📌 Key Rules:

* Only catch errors during **rendering**, **lifecycle methods**, and **constructors** of child components.
* **Do not catch**:

  * Event handlers
  * Asynchronous code (like `setTimeout`)
  * Server-side rendering
  * Errors thrown in Error Boundaries themselves

---

### 🧠 How to implement

React provides error lifecycle methods for **class components only**:

* `static getDerivedStateFromError(error)`
* `componentDidCatch(error, info)`

---

### 🧩 Example: Error Boundary Component

#### 📄 `ErrorBoundary.js`

```jsx
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state to show fallback UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // You can log the error to an error reporting service
    console.error('Error caught by ErrorBoundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong.</h2>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

---

### 🧪 Example: Component That Throws Error

#### 📄 `BuggyComponent.js`

```jsx
import React from 'react';

const BuggyComponent = () => {
  throw new Error('Simulated crash!');
  return <div>This will not render</div>;
};

export default BuggyComponent;
```

---

### 🧵 App with Error Boundary

#### 📄 `App.js`

```jsx
import React from 'react';
import ErrorBoundary from './ErrorBoundary';
import BuggyComponent from './BuggyComponent';

function App() {
  return (
    <div>
      <h1>React Error Boundary Demo</h1>
      <ErrorBoundary>
        <BuggyComponent />
      </ErrorBoundary>
    </div>
  );
}

export default App;
```

---

### 🔍 Output:

* Instead of crashing the app, it shows:
  👉 **"Something went wrong."**

---

### 🛠 Optional: Custom Fallback UI

You can make the fallback UI smarter:

```jsx
if (this.state.hasError) {
  return (
    <div>
      <h2>Oops! Something broke.</h2>
      <button onClick={() => window.location.reload()}>Reload</button>
    </div>
  );
}
```

---

React **does not support Error Boundaries using functional components directly** because error boundaries require lifecycle methods like `componentDidCatch` and `getDerivedStateFromError`, which **only exist in class components**.

However, you can still **use error boundaries in a functional component-based app** by **wrapping** your components with a class-based error boundary component.

1. **Create the Error Boundary using a class component** (because React only supports `componentDidCatch` and `getDerivedStateFromError` in class components).
2. **Use (wrap) that Error Boundary around any component — including functional components** — in your app.
---

### 🧩 Functional Component That Crashes

```jsx
// BuggyComponent.js
import React from 'react';

const BuggyComponent = () => {
  throw new Error("Oops, I crashed!");
  // return <div>This will never render</div>;
};

export default BuggyComponent;
```

---

### 🧵 3. Use ErrorBoundary in Functional Component App

```jsx
// App.js
import React from 'react';
import ErrorBoundary from './ErrorBoundary';
import BuggyComponent from './BuggyComponent';

const App = () => {
  return (
    <div>
      <h1>Functional App with Error Boundary</h1>

      {/* Use ErrorBoundary to wrap buggy part */}
      <ErrorBoundary>
        <BuggyComponent />
      </ErrorBoundary>
    </div>
  );
};

export default App;
```

---

### 🚫 Why You Can’t Use Hooks for This?

Hooks like `useEffect`, `useErrorHandler`, or `try/catch` can **only catch runtime logic errors**, **not render-time errors**.

You **must use a class component** for actual React Error Boundaries — **React has no plan to change this** as of now (React 18+).

---

### ✅ If you still want a Hook-style wrapper...

You can use libraries like **`react-error-boundary`** by Kent C. Dodds that allow similar behavior using functional components.

---
