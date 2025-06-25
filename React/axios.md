 Let’s build a **clean, scalable Axios setup** in a **React + TypeScript** app using:

* ✅ Separate interceptor file
* ✅ Base URL and keys from constants
* ✅ Error handling
* ✅ Support for custom headers
* ✅ Query parameters for GET
* ✅ Body for POST

---

### 📁 Folder Structure

```
src/
├── api/
│   ├── api.ts
│   ├── interceptor.ts
│   ├── constants.ts
│   └── httpStatusCodes.ts
├── services/
│   └── userService.ts
└── components/
    └── Users.tsx
```

---

### 📄 1. `constants.ts` – Base URL & Tokens

```ts
// src/api/constants.ts
export const API_BASE_URL = 'https://api.example.com';

export const API_KEYS = {
  defaultToken: 'token123',
};
```

---

### 📄 2. `httpStatusCodes.ts` – Optional Status Helpers

```ts
// src/api/httpStatusCodes.ts
export const HTTP_STATUS = {
  UNAUTHORIZED: 401,
  FORBIDDEN: 403,
  NOT_FOUND: 404,
  SERVER_ERROR: 500,
};
```

---

### 📄 3. `interceptor.ts` – Setup Interceptors Separately

```ts
// src/api/interceptor.ts
import { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios';
import { HTTP_STATUS } from './httpStatusCodes';

export const setupInterceptors = (api: AxiosInstance) => {
  // Request interceptor
  api.interceptors.request.use(
    (config: AxiosRequestConfig) => {
      const token = localStorage.getItem('token');
      if (token && config.headers) {
        config.headers['Authorization'] = `Bearer ${token}`;
      }
      return config;
    },
    (error) => Promise.reject(error)
  );

  // Response interceptor
  api.interceptors.response.use(
    (response: AxiosResponse) => response,
    (error) => {
      const status = error.response?.status;

      switch (status) {
        case HTTP_STATUS.UNAUTHORIZED:
          console.warn('Unauthorized. Redirect to login.');
          break;
        case HTTP_STATUS.SERVER_ERROR:
          console.error('Server error occurred.');
          break;
        default:
          console.error('Unhandled error:', error.message);
      }

      return Promise.reject(error);
    }
  );
};
```

---

### 📄 4. `api.ts` – Axios Instance with Interceptors

```ts
// src/api/api.ts
import axios, { AxiosInstance } from 'axios';
import { API_BASE_URL } from './constants';
import { setupInterceptors } from './interceptor';

const api: AxiosInstance = axios.create({
  baseURL: API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

setupInterceptors(api);

export default api;
```

---

### 📄 5. `userService.ts` – Example with Query, Body, Headers

```ts
// src/services/userService.ts
import api from '../api/api';

type User = {
  id: number;
  name: string;
  email: string;
};

export const getUsers = async (filters: { page?: number; search?: string }): Promise<User[]> => {
  const response = await api.get<User[]>('/users', {
    params: filters, // 🔍 Query Params
  });
  return response.data;
};

export const createUser = async (user: Omit<User, 'id'>, customHeader?: string): Promise<User> => {
  const response = await api.post<User>('/users', user, {
    headers: {
      'x-custom-header': customHeader ?? '',
    },
  });
  return response.data;
};
```

---

### 📄 6. `Users.tsx` – Usage Example

```tsx
import React, { useEffect, useState } from 'react';
import { getUsers, createUser } from '../services/userService';

type User = {
  id: number;
  name: string;
  email: string;
};

const Users: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    getUsers({ page: 1, search: 'john' }) // ✅ Query params
      .then(setUsers)
      .catch((err) => setError(err.message));
  }, []);

  const handleAddUser = () => {
    createUser(
      { name: 'Alice', email: 'alice@example.com' },
      'XYZ-CUSTOM-HEADER' // ✅ Custom header
    )
      .then((newUser) => setUsers((prev) => [...prev, newUser]))
      .catch((err) => setError(err.message));
  };

  return (
    <div>
      <h2>User List</h2>
      {error && <p style={{ color: 'red' }}>{error}</p>}
      {users.map((user) => (
        <div key={user.id}>{user.name} - {user.email}</div>
      ))}
      <button onClick={handleAddUser}>Add User</button>
    </div>
  );
};

export default Users;
```

---
