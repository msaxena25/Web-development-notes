# Microservices architecture, a React library 

In a microservices architecture, a React library can be a central hub for shared components and services. To achieve this, you'll need to create a separate React project as your library, and then publish it to a package manager like npm. Here's a detailed guide with code samples:

### Step 1: Initialize the React Library Project

Create a new React project for your library. You can use Create React App or Vite for this. Vite is generally faster for library development due to its native ES module support.

**Using Vite:**

```bash
npm create vite@latest my-react-library -- --template react-ts
cd my-react-library
npm install
```

**Using Create React App (CRA):**

```bash
npx create-react-app my-react-library --template typescript
cd my-react-library
npm install
```

### Step 2: Configure for Library Output

You'll need to configure your `package.json` and build tools to produce a distributable library.

**For Vite:**

Modify `vite.config.ts`:

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import dts from 'vite-plugin-dts';
import path from 'path';

export default defineConfig({
  plugins: [
    react(),
    dts({
      insertTypesEntry: true, // This generates the 'index.d.ts' file
    }),
  ],
  build: {
    lib: {
      entry: path.resolve(__dirname, 'src/index.ts'), // Your library's entry point
      name: 'MyReactLibrary', // Global name for your library
      fileName: (format) => `my-react-library.${format}.js`, // Output file name
    },
    rollupOptions: {
      external: ['react', 'react-dom'], // Peer dependencies that consumers should provide
      output: {
        globals: {
          react: 'React',
          'react-dom': 'ReactDOM',
        },
      },
    },
  },
});
```

Modify `package.json`:

```json
// package.json
{
  "name": "my-react-library",
  "version": "1.0.0",
  "description": "A shared React library with common components and services",
  "main": "./dist/my-react-library.umd.js",
  "module": "./dist/my-react-library.es.js",
  "types": "./dist/index.d.ts", // Important for TypeScript
  "files": ["dist"],
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview",
    "prepublishOnly": "npm run build" // Ensure build before publishing
  },
  "keywords": ["react", "library", "shared", "components", "services"],
  "author": "Your Name",
  "license": "MIT",
  "peerDependencies": {
    "react": ">=18.0.0",
    "react-dom": ">=18.0.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.66",
    "@types/react-dom": "^18.2.22",
    "@typescript-eslint/eslint-plugin": "^7.2.0",
    "@typescript-eslint/parser": "^7.2.0",
    "@vitejs/plugin-react": "^4.2.1",
    "eslint": "^8.57.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.6",
    "typescript": "^5.2.2",
    "vite": "^5.2.0",
    "vite-plugin-dts": "^3.8.3"
  }
}
```

**For Create React App:**

CRA isn't designed as a library bundler out-of-the-box. You'd typically eject (not recommended for simple libraries) or use tools like `rollup` or `webpack` directly. For simplicity, if you're using CRA, you might be better off converting it to a custom Webpack/Rollup setup or using a dedicated library boilerplate. However, if you stick with CRA, you'd configure your `package.json` to build a single file, and you'd need to manually manage `types` files. This is more complex than Vite.

### Step 3: Create Shared Components and Services

Now, let's create the shared components and services within your `src` directory.

**1. `src/components/Loader/Loader.tsx`**

```tsx
import React from 'react';
import './Loader.css'; // You'll need to create this CSS file

interface LoaderProps {
  isLoading: boolean;
}

const Loader: React.FC<LoaderProps> = ({ isLoading }) => {
  if (!isLoading) return null;

  return (
    <div className="loader-overlay">
      <div className="loader-spinner"></div>
    </div>
  );
};

export default Loader;
```


**2. `src/services/api.ts`**

```typescript
// src/services/api.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse, AxiosError } from 'axios';

interface ApiResponse<T> {
  data: T;
  message: string;
  success: boolean;
  statusCode: number;
}

const createApiClient = (baseURL: string): AxiosInstance => {
  const api: AxiosInstance = axios.create({
    baseURL,
    headers: {
      'Content-Type': 'application/json',
    },
    timeout: 10000, // 10 seconds
  });

  // Request interceptor
  api.interceptors.request.use(
    (config: AxiosRequestConfig) => {
      const token = localStorage.getItem('authToken'); // Or wherever your token is stored
      if (token) {
        config.headers = {
          ...config.headers,
          Authorization: `Bearer ${token}`,
        };
      }
      return config;
    },
    (error: AxiosError) => {
      return Promise.reject(error);
    }
  );

  // Response interceptor
  api.interceptors.response.use(
    (response: AxiosResponse<ApiResponse<any>>) => {
      // You can do something with the response here, e.g., logging
      return response;
    },
    (error: AxiosError<ApiResponse<any>>) => {
      if (error.response) {
        // The request was made and the server responded with a status code
        // that falls out of the range of 2xx
        console.error('API Error Response:', error.response.data);
        if (error.response.status === 401) {
          // Handle unauthorized, e.g., redirect to login
          // Example: window.location.href = '/login';
        }
      } else if (error.request) {
        // The request was made but no response was received
        console.error('API Error Request:', error.request);
      } else {
        // Something happened in setting up the request that triggered an Error
        console.error('API Error Message:', error.message);
      }
      return Promise.reject(error);
    }
  );

  return api;
};

// Example usage:
export const myApi = createApiClient(process.env.REACT_APP_API_BASE_URL || 'http://localhost:3000/api');
```

**3. `src/constants/sharedConstants.ts`**

```typescript
// src/constants/sharedConstants.ts
export const APP_NAME = 'My Shared App';
export const API_VERSION = 'v1';
export const MAX_ITEMS_PER_PAGE = 10;

export enum UserRoles {
  Admin = 'ADMIN',
  Editor = 'EDITOR',
  Viewer = 'VIEWER',
}

// Add more constants as needed
```

**4. `src/services/notificationService.ts`**

```typescript
// src/services/notificationService.ts
import { EventEmitter } from 'events'; // Or use a dedicated pub-sub library

interface Notification {
  type: 'success' | 'error' | 'info' | 'warning';
  message: string;
  duration?: number; // in milliseconds
}

class NotificationService extends EventEmitter {
  private static instance: NotificationService;

  private constructor() {
    super();
  }

  public static getInstance(): NotificationService {
    if (!NotificationService.instance) {
      NotificationService.instance = new NotificationService();
    }
    return NotificationService.instance;
  }

  public show(notification: Notification) {
    this.emit('showNotification', notification);
  }

  public success(message: string, duration?: number) {
    this.show({ type: 'success', message, duration });
  }

  public error(message: string, duration?: number) {
    this.show({ type: 'error', message, duration });
  }

  public info(message: string, duration?: number) {
    this.show({ type: 'info', message, duration });
  }

  public warning(message: string, duration?: number) {
    this.show({ type: 'warning', message, duration });
  }
}

export const notificationService = NotificationService.getInstance();
```

**5. `src/services/authService.ts`**

```typescript
// src/services/authService.ts
import { myApi } from './api';

interface User {
  id: string;
  email: string;
  role: string;
  // ... other user details
}

interface LoginResponse {
  token: string;
  user: User;
}

class AuthService {
  private static instance: AuthService;
  private token: string | null = null;
  private currentUser: User | null = null;

  private constructor() {
    this.loadTokenFromStorage();
  }

  public static getInstance(): AuthService {
    if (!AuthService.instance) {
      AuthService.instance = new AuthService();
    }
    return AuthService.instance;
  }

  private loadTokenFromStorage() {
    this.token = localStorage.getItem('authToken');
    const userData = localStorage.getItem('currentUser');
    if (userData) {
      try {
        this.currentUser = JSON.parse(userData);
      } catch (e) {
        console.error("Failed to parse user data from local storage", e);
        this.logout(); // Clear corrupted data
      }
    }
  }

  public isAuthenticated(): boolean {
    return !!this.token;
  }

  public getToken(): string | null {
    return this.token;
  }

  public getCurrentUser(): User | null {
    return this.currentUser;
  }

  public async login(credentials: { email: string; password: string }): Promise<User> {
    try {
      const response = await myApi.post<LoginResponse>('/auth/login', credentials);
      const { token, user } = response.data;
      this.setAuthData(token, user);
      return user;
    } catch (error) {
      console.error('Login failed:', error);
      throw error;
    }
  }

  public logout() {
    this.token = null;
    this.currentUser = null;
    localStorage.removeItem('authToken');
    localStorage.removeItem('currentUser');
    // Optionally redirect to login page or dispatch a global event
    // window.location.href = '/login';
  }

  private setAuthData(token: string, user: User) {
    this.token = token;
    this.currentUser = user;
    localStorage.setItem('authToken', token);
    localStorage.setItem('currentUser', JSON.stringify(user));
  }

  // Example: Check if user has a specific role
  public hasRole(role: string): boolean {
    return this.currentUser?.role === role;
  }
}

export const authService = AuthService.getInstance();
```

### Step 4: Export Everything from `index.ts`

Your `src/index.ts` file will be the public interface of your library.

```typescript
// src/index.ts
export { default as Loader } from './components/Loader/Loader';

// Services
export { myApi } from './services/api';
export { notificationService } from './services/notificationService';
export { authService } from './services/authService';

// Constants
export * from './constants/sharedConstants';

// Re-export types if needed
// export type { Notification } from './services/notificationService';
// export type { User } from './services/authService';
```

### Step 5: Build the Library

Run the build command you defined in `package.json`:

```bash
npm run build
```

This will create a `dist` folder containing your compiled library files (e.g., `my-react-library.es.js`, `my-react-library.umd.js`, `index.d.ts`).

### Step 6: Publish to npm

Before publishing, ensure you have an npm account and are logged in (`npm login`).

```bash
npm publish
```

* **Versioning:** Remember to increment the `version` in `package.json` before each publish to avoid conflicts and allow consumers to update. Use semantic versioning (e.g., `1.0.0`, `1.0.1`, `1.1.0`, `2.0.0`).
* **Scope (Optional for Private Libraries):** If you're publishing a private library to a private npm registry (e.g., GitHub Packages, GitLab Packages, Verdaccio), you'll need to configure your `.npmrc` and potentially use a scope:
    ```json
    // package.json
    {
      "name": "@your-org/my-react-library", // Example with scope
      // ...
    }
    ```
    Then `npm publish --access public` (if scoped but you want it public) or configure your private registry.

### Step 7: Consume the Library in Multiple React Projects

Now, in your other React projects (e.g., `project-alpha`, `project-beta`):

**1. Install the Library:**

```bash
npm install my-react-library
# or, if scoped:
# npm install @your-org/my-react-library
```

**2. Use Shared Components and Services:**

**`project-alpha/src/App.tsx`**

```tsx
import React, { useState, useEffect } from 'react';
import { Loader, myApi, notificationService, authService, APP_NAME, UserRoles } from 'my-react-library'; // Import from your library

function App() {
  const [loading, setLoading] = useState(false);
  const [data, setData] = useState(null);
  const [isLoggedIn, setIsLoggedIn] = useState(authService.isAuthenticated());

  useEffect(() => {
    // Example of using api service
    const fetchData = async () => {
      setLoading(true);
      try {
        const response = await myApi.get('/posts/1'); // Use your shared API instance
        setData(response.data);
        notificationService.success('Data fetched successfully!'); // Use your shared notification service
      } catch (error) {
        notificationService.error('Failed to fetch data.');
        console.error('Error:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchData();

    // Example of listening to notifications (optional, if you want a global notification display)
    const handleNotification = (notification: any) => {
      console.log('Received notification:', notification);
      // Here you'd typically render a toast/snackbar component
    };
    notificationService.on('showNotification', handleNotification);

    return () => {
      notificationService.off('showNotification', handleNotification);
    };
  }, []);

  const handleLogin = async () => {
    try {
      setLoading(true);
      const user = await authService.login({ email: 'test@example.com', password: 'password123' });
      setIsLoggedIn(true);
      notificationService.success(`Welcome, ${user.email}!`);
    } catch (error) {
      notificationService.error('Login failed. Please check credentials.');
    } finally {
      setLoading(false);
    }
  };

  const handleLogout = () => {
    authService.logout();
    setIsLoggedIn(false);
    notificationService.info('Logged out successfully.');
  };

  return (
    <div>
      <h1>Welcome to {APP_NAME}</h1> {/* Using shared constant */}
      <p>Current User Role: {authService.getCurrentUser()?.role || 'Guest'}</p>
      {authService.hasRole(UserRoles.Admin) && <p>You are an admin!</p>}

      {isLoggedIn ? (
        <button onClick={handleLogout}>Logout</button>
      ) : (
        <button onClick={handleLogin}>Login</button>
      )}

      {data && (
        <div>
          <h2>Fetched Data:</h2>
          <pre>{JSON.stringify(data, null, 2)}</pre>
        </div>
      )}

      <Loader isLoading={loading} />
    </div>
  );
}

export default App;
```

**Important Considerations:**

* **Peer Dependencies:** In your library's `package.json`, specify `react` and `react-dom` as `peerDependencies`. This ensures that the consuming application uses its own installed version of React, preventing potential conflicts or duplicate React instances.
* **Bundling Strategy:** Vite's library mode is excellent for this. If using Webpack/Rollup directly, ensure you configure them for library output, externalizing `react` and `react-dom`.
* **CSS Handling:** For components with styles (like the `Loader`), you can either:
    * **Import CSS directly:** As shown in `Loader.tsx`, which works if the consuming app's bundler handles CSS imports.
    * **CSS-in-JS:** Use libraries like Styled Components or Emotion within your library. This bundles the styles with the component.
    * **Pre-compiled CSS:** Compile your CSS to a separate file and instruct consumers to import it.
* **Testing:** Implement unit tests for your components and services within the library project.
* **Documentation:** Provide clear documentation for your library, explaining how to use each component, service, and constant.
* **Storybook:** For UI components, consider using Storybook to develop, document, and test them in isolation.
* **Monorepo (Advanced):** For larger projects with many shared libraries, consider a monorepo setup (e.g., with Lerna or Turborepo). This simplifies dependency management, linking, and builds across multiple packages.
* **TypeScript:** Using TypeScript significantly improves maintainability and provides type safety for consumers of your library. Ensure your `d.ts` files are correctly generated and included in your published package.
* **Global State Management:** If you need shared global state, consider context API or a lightweight state management library like Zustand or Jotai within your library, or expose hooks that integrate with the consuming app's state management.
* **Version Management:** Regularly update your library and communicate changes to consuming projects.

By following these steps, you can effectively create and manage a shared React library, promoting code reusability and consistency across your multiple React applications.

------------