Okay, let's create a piece of code for `authService.js` to handle login, save the JWT token, and then (conceptually) navigate to the dashboard.

First, ensure you have Axios installed in your React project:
`npm install axios` or `yarn add axios`

Here's the code for `authService.js` and how you'd use it in a login component.

### `src/common/services/authService.js`

```javascript
import api from './api'; // Import your pre-configured Axios instance

const AUTH_TOKEN_KEY = 'jwt_token'; // Key to store the JWT token in localStorage

const authService = {
  /**
   * Logs in a user by sending credentials to the authentication API.
   * On successful login, it saves the JWT token to localStorage.
   * @param {string} username - The user's username.
   * @param {string} password - The user's password.
   * @returns {Promise<Object>} - A promise that resolves with the API response data.
   * @throws {Error} - Throws an error if the API call fails.
   */
  login: async (username, password) => {
    try {
      const response = await api.post('/auth/login', { username, password });
      const { token } = response.data; // Assuming the API returns { token: "your_jwt_token" }

      if (token) {
        localStorage.setItem(AUTH_TOKEN_KEY, token);
        console.log('JWT token saved:', token);
      } else {
        // Handle case where token is not in the response
        console.warn('Login successful, but no token received from API.');
        throw new Error('Authentication failed: No token received.');
      }

      return response.data;
    } catch (error) {
      console.error('Login failed:', error.response ? error.response.data : error.message);
      // Re-throw the error so the calling component can handle it
      throw error;
    }
  },

  /**
   * Retrieves the stored JWT token.
   * @returns {string | null} - The JWT token or null if not found.
   */
  getToken: () => {
    return localStorage.getItem(AUTH_TOKEN_KEY);
  },

  /**
   * Removes the JWT token from localStorage, effectively logging out the user.
   */
  logout: () => {
    localStorage.removeItem(AUTH_TOKEN_KEY);
    console.log('JWT token removed. User logged out.');
  },

  /**
   * Checks if a user is currently authenticated (by checking for a stored token).
   * Note: This is a basic check. For true authentication, you'd also want to
   * validate the token's expiry on the backend or regularly refresh it.
   * @returns {boolean} - True if a token exists, false otherwise.
   */
  isAuthenticated: () => {
    return !!localStorage.getItem(AUTH_TOKEN_KEY);
  },
};

export default authService;
```

### `src/common/services/api.js` (Your Axios instance configuration)

This file sets up your base Axios instance and potentially global interceptors.

```javascript
import axios from 'axios';
import authInterceptor from '../interceptors/authInterceptor'; // Assuming you have this
import errorInterceptor from '../interceptors/errorInterceptor'; // Assuming you have this

const api = axios.create({
  baseURL: 'http://localhost:5000/api', // **IMPORTANT: Replace with your actual API base URL**
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add request interceptor to attach JWT token
api.interceptors.request.use(authInterceptor, (error) => {
  return Promise.reject(error);
});

// Add response interceptor for global error handling (e.g., 401, 500)
api.interceptors.response.use((response) => response, errorInterceptor);

export default api;
```

### `src/common/interceptors/authInterceptor.js` (To add the token to requests)

This interceptor will automatically attach the JWT token to outgoing requests.

```javascript
import authService from '../services/authService';

const authInterceptor = (config) => {
  const token = authService.getToken();

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
};

export default authInterceptor;
```

### `src/common/interceptors/errorInterceptor.js` (Basic error handling)

This interceptor can handle common API errors globally.

```javascript
import { useNavigate } from 'react-router-dom'; // Assuming react-router-dom for navigation

const errorInterceptor = (error) => {
  if (error.response) {
    // The request was made and the server responded with a status code
    // that falls out of the range of 2xx
    console.error('API Error Response:', error.response.data);
    console.error('API Error Status:', error.response.status);
    console.error('API Error Headers:', error.response.headers);

    if (error.response.status === 401) {
      console.warn('Unauthorized access. Redirecting to login...');
    } else if (error.response.status === 403) {
      // Forbidden: User doesn't have necessary permissions
      console.warn('Access forbidden.');
    } else if (error.response.status === 500) {
      // Server error
      console.error('Server error. Please try again later.');
    }
  } else if (error.request) {
    // The request was made but no response was received
    console.error('No response received:', error.request);
  } else {
    // Something happened in setting up the request that triggered an Error
    console.error('Error during request setup:', error.message);
  }
  return Promise.reject(error); // Re-throw the error
};

export default errorInterceptor;
```

### `src/features/Auth/pages/LoginPage.js` (How to use `authService`)

This is a simplified `LoginPage` component that uses the `authService`.

```javascript
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import authService from '../../../common/services/authService'; // Adjust path as needed
import AuthForm from '../components/AuthForm'; // Assuming you have this form component

const LoginPage = () => {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate(); // Hook for navigation

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError(null); // Clear previous errors
    setLoading(true);

    try {
      const response = await authService.login(username, password);
      console.log('Login successful:', response);
      // Assuming successful login means a token was saved.
      // Now, redirect to the dashboard.
      navigate('/dashboard');
    } catch (err) {
      // The authService already logs the error, but we can set a user-friendly message
      setError(err.response?.data?.message || 'Login failed. Please check your credentials.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div style={{ maxWidth: '400px', margin: '50px auto', padding: '20px', border: '1px solid #ccc', borderRadius: '8px' }}>
      <h2>Login</h2>
      <AuthForm
        username={username}
        password={password}
        onUsernameChange={(e) => setUsername(e.target.value)}
        onPasswordChange={(e) => setPassword(e.target.value)}
        onSubmit={handleSubmit}
        loading={loading}
      />
      {error && <p style={{ color: 'red', marginTop: '10px' }}>{error}</p>}
    </div>
  );
};

export default LoginPage;
```

### `src/features/Auth/components/AuthForm.js` (A simple form component)

```javascript
import React from 'react';

const AuthForm = ({ username, password, onUsernameChange, onPasswordChange, onSubmit, loading }) => {
  return (
    <form onSubmit={onSubmit}>
      <div>
        <label htmlFor="username">Username:</label>
        <input
          type="text"
          id="username"
          value={username}
          onChange={onUsernameChange}
          required
        />
      </div>
      <div style={{ marginTop: '10px' }}>
        <label htmlFor="password">Password:</label>
        <input
          type="password"
          id="password"
          value={password}
          onChange={onPasswordChange}
          required
        />
      </div>
      <button type="submit" disabled={loading} style={{ marginTop: '20px', padding: '10px 15px', cursor: loading ? 'not-allowed' : 'pointer' }}>
        {loading ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
};

export default AuthForm;
```

## Create the code for the Dashboard, Product List, Product API service, and integrate a search bar into the header.

Here's the plan:

1.  **Dashboard (`features/Dashboard/pages/DashboardPage.js`):**
    * Create a basic Dashboard page.
    * Integrate a generic `Header` component from `base/Layout/Header`.
    * Add some dummy menu items and show how routing would work.

2.  **Product List (`features/Products/pages/ProductsListPage.js`):**
    * Create a page to display a list of products.
    * Use a `ProductCard` component to render individual products.
    * Show how to fetch data using the new product service.

3.  **Product Service (`common/services/productService.js`):**
    * Create a dedicated service to handle API calls related to products.
    * It will have a method to fetch all products.

4.  **Header with Search Bar (`base/Layout/Header/Header.js` and `base/Layout/Header/Header.module.css`):**
    * Modify the `Header` component to include a `SearchBar`.
    * The search bar will allow input, and we'll demonstrate how a search query *could* be handled (e.g., passed to a parent component or a global state).

Let's get started with the code.

---

### 1. Update `src/core/routes/AppRoutes.js` (Add new routes)

First, let's ensure our new pages are part of the application's routes.

```javascript
// src/core/routes/AppRoutes.js
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import MainLayout from '../../base/Layout/MainLayout';
import AuthLayout from '../../base/Layout/AuthLayout';
import HomePage from '../../features/Home/pages/HomePage';
import LoginPage from '../../features/Auth/pages/LoginPage';
import DashboardPage from '../../features/Dashboard/pages/DashboardPage'; // New
import ProductsListPage from '../../features/Products/pages/ProductsListPage'; // New
import ProductDetailsPage from '../../features/Products/pages/ProductDetailsPage'; // Placeholder
import OrderSuccessPage from '../../features/OrderSuccess/pages/OrderSuccessPage'; // Placeholder
import CheckoutPage from '../../features/Checkout/pages/CheckoutPage'; // Placeholder
import ProtectedRoute from './ProtectedRoute'; // We'll create this if it's not already there

const AppRoutes = () => {
  return (
    <Router>
      <Routes>
        {/* Public Routes with AuthLayout */}
        <Route element={<AuthLayout />}>
          <Route path="/login" element={<LoginPage />} />
          {/* Add other public routes like registration here */}
        </Route>

        {/* Authenticated Routes with MainLayout */}
        <Route element={<MainLayout />}>
          <Route path="/" element={<HomePage />} />
          {/* Protected routes */}
          <Route element={<ProtectedRoute />}>
            <Route path="/dashboard" element={<DashboardPage />} />
            <Route path="/products" element={<ProductsListPage />} />
            <Route path="/products/:id" element={<ProductDetailsPage />} /> {/* For future use */}
            <Route path="/checkout" element={<CheckoutPage />} /> {/* For future use */}
            <Route path="/order-success" element={<OrderSuccessPage />} /> {/* For future use */}
          </Route>
        </Route>

        {/* Fallback for 404 (optional) */}
        <Route path="*" element={<div>404 Not Found</div>} />
      </Routes>
    </Router>
  );
};

export default AppRoutes;
```

### 2. Create `src/core/routes/ProtectedRoute.js` (If not already present)

This component will act as a guard for authenticated routes.

```javascript
// src/core/routes/ProtectedRoute.js
import React from 'react';
import { Navigate, Outlet } from 'react-router-dom';
import authService from '../../common/services/authService'; // Adjust path

const ProtectedRoute = ({ children }) => {
  const isAuthenticated = authService.isAuthenticated();

  if (!isAuthenticated) {
    // Redirect them to the login page, but save the current location they were trying to go to
    // so they can be redirected there after logging in.
    return <Navigate to="/login" replace />;
  }

  return children ? children : <Outlet />;
};

export default ProtectedRoute;
```

---

### 3. Dashboard with Header and Menus

#### `src/base/Layout/MainLayout.js` (Ensuring it wraps children with Header and Footer)

```javascript
// src/base/Layout/MainLayout.js
import React from 'react';
import { Outlet } from 'react-router-dom';
import Header from './Header/Header';
import Footer from './Footer/Footer';

const MainLayout = () => {
  // In a real app, you might have state here to pass search queries
  // or other global layout-related data.
  const handleSearch = (query) => {
    console.log("Search initiated from MainLayout:", query);
    // You would typically navigate to a search results page or update a global state
    // For now, it's just a console log.
  };

  return (
    <div style={{ display: 'flex', flexDirection: 'column', minHeight: '100vh' }}>
      <Header onSearch={handleSearch} /> {/* Pass the search handler down */}
      <main style={{ flexGrow: 1, padding: '20px' }}>
        <Outlet /> {/* This is where the actual page content will be rendered */}
      </main>
      <Footer />
    </div>
  );
};

export default MainLayout;
```

#### `src/base/Layout/Header/Header.js` (Modified to include SearchBar and menus)

```javascript
// src/base/Layout/Header/Header.js
import React from 'react';
import { Link, useNavigate } from 'react-router-dom';
import SearchBar from '../../common/components/SearchBar/SearchBar'; // Import the new SearchBar
import authService from '../../../common/services/authService';
import styles from './Header.module.css';

const Header = ({ onSearch }) => {
  const navigate = useNavigate();

  const handleLogout = () => {
    authService.logout();
    navigate('/login');
  };

  return (
    <header className={styles.header}>
      <div className={styles.logo}>
        <Link to="/">E-Commerce App</Link>
      </div>
      <nav className={styles.nav}>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/products">Products</Link></li>
          <li><Link to="/dashboard">Dashboard</Link></li>
          {/* Add more menu items as needed */}
        </ul>
      </nav>
      <div className={styles.actions}>
        <SearchBar onSearch={onSearch} /> {/* Integrate SearchBar */}
        {authService.isAuthenticated() ? (
          <button onClick={handleLogout} className={styles.authButton}>Logout</button>
        ) : (
          <Link to="/login" className={styles.authButton}>Login</Link>
        )}
      </div>
    </header>
  );
};

export default Header;
```

#### `src/base/Layout/Header/Header.module.css` (Basic styling for Header)

```css
/* src/base/Layout/Header/Header.module.css */
.header {
  background-color: #282c34;
  padding: 15px 30px;
  color: white;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.logo a {
  color: white;
  text-decoration: none;
  font-size: 1.5em;
  font-weight: bold;
}

.nav ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
}

.nav li {
  margin-right: 25px;
}

.nav a {
  color: white;
  text-decoration: none;
  font-weight: 500;
  transition: color 0.3s ease;
}

.nav a:hover,
.nav a.active { /* Add .active for active route highlighting if using NavLink */
  color: #61dafb;
}

.actions {
  display: flex;
  align-items: center;
  gap: 20px; /* Space between search bar and buttons */
}

.authButton {
  background-color: #61dafb;
  color: #282c34;
  border: none;
  padding: 8px 15px;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
  text-decoration: none; /* For Link component styling */
  transition: background-color 0.3s ease;
}

.authButton:hover {
  background-color: #21a1f1;
}
```

#### `src/features/Dashboard/pages/DashboardPage.js` (Dashboard Content)

```javascript
// src/features/Dashboard/pages/DashboardPage.js
import React from 'react';
import { Link } from 'react-router-dom';
import styles from '../Dashboard.module.css'; // Assuming you'll add styling

const DashboardPage = () => {
  return (
    <div className={styles.dashboardContainer}>
      <h1>Welcome to Your Dashboard!</h1>
      <p>Here you can manage your orders, profile, and more.</p>

      <div className={styles.dashboardSections}>
        <div className={styles.sectionCard}>
          <h3>Order History</h3>
          <p>View your past purchases and their statuses.</p>
          <Link to="/dashboard/orders" className={styles.sectionLink}>View Orders</Link>
        </div>
        <div className={styles.sectionCard}>
          <h3>Profile Settings</h3>
          <p>Update your personal information and preferences.</p>
          <Link to="/dashboard/profile" className={styles.sectionLink}>Edit Profile</Link>
        </div>
        <div className={styles.sectionCard}>
          <h3>Shipping Addresses</h3>
          <p>Manage your saved shipping addresses.</p>
          <Link to="/dashboard/addresses" className={styles.sectionLink}>Manage Addresses</Link>
        </div>
      </div>
    </div>
  );
};

export default DashboardPage;
```

#### `src/features/Dashboard/Dashboard.module.css` (Basic styling for Dashboard)

```css
/* src/features/Dashboard/Dashboard.module.css */
.dashboardContainer {
  padding: 30px;
  max-width: 1200px;
  margin: 0 auto;
}

.dashboardContainer h1 {
  color: #333;
  margin-bottom: 20px;
  text-align: center;
}

.dashboardContainer p {
  text-align: center;
  margin-bottom: 40px;
  color: #555;
  font-size: 1.1em;
}

.dashboardSections {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 30px;
}

.sectionCard {
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 25px;
  text-align: center;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.sectionCard:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
}

.sectionCard h3 {
  color: #007bff;
  margin-bottom: 15px;
  font-size: 1.4em;
}

.sectionCard p {
  color: #666;
  font-size: 0.95em;
  margin-bottom: 20px;
}

.sectionLink {
  display: inline-block;
  background-color: #007bff;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  text-decoration: none;
  font-weight: bold;
  transition: background-color 0.3s ease;
}

.sectionLink:hover {
  background-color: #0056b3;
}
```

---

### 4. Product List with Data from API

#### `src/common/services/productService.js` (New Product Service)

```javascript
// src/common/services/productService.js
import api from './api'; // Import your pre-configured Axios instance

const productService = {
  /**
   * Fetches a list of products from the API.
   * Can accept query parameters for filtering/pagination if the API supports it.
   * @param {Object} [params] - Optional query parameters (e.g., { category: 'electronics', limit: 10 }).
   * @returns {Promise<Array>} - A promise that resolves with an array of product data.
   * @throws {Error} - Throws an error if the API call fails.
   */
  getAllProducts: async (params = {}) => {
    try {
      // Example: Using a dummy API endpoint for products. Replace with your actual endpoint.
      // If your API supports query params for search/filter, pass them like this:
      // const response = await api.get('/products', { params });
      const response = await api.get('/products'); // Assuming a simple /products endpoint for now
      return response.data; // Assuming API returns an array of products directly
    } catch (error) {
      console.error('Failed to fetch products:', error.response ? error.response.data : error.message);
      throw error; // Re-throw for component to handle
    }
  },

  /**
   * Fetches details for a single product by its ID.
   * @param {string} productId - The ID of the product to fetch.
   * @returns {Promise<Object>} - A promise that resolves with the single product's data.
   * @throws {Error} - Throws an error if the API call fails.
   */
  getProductDetails: async (productId) => {
    try {
      const response = await api.get(`/products/${productId}`);
      return response.data;
    } catch (error) {
      console.error(`Failed to fetch product details for ID ${productId}:`, error.response ? error.response.data : error.message);
      throw error;
    }
  },

  // You can add more methods here like:
  // searchProducts: async (query) => { /* ... */ },
  // getProductsByCategory: async (category) => { /* ... */ },
};

export default productService;
```

#### `src/features/Products/pages/ProductsListPage.js` (Product List Page)

```javascript
// src/features/Products/pages/ProductsListPage.js
import React, { useEffect, useState } from 'react';
import productService from '../../../common/services/productService';
import ProductCard from '../components/ProductCard/ProductCard'; // Import ProductCard
import Spinner from '../../../common/components/Spinner/Spinner'; // Import Spinner
import styles from '../Products.module.css';

const ProductsListPage = () => {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchProducts = async () => {
      try {
        setLoading(true);
        setError(null);
        const data = await productService.getAllProducts();
        setProducts(data);
      } catch (err) {
        setError('Failed to load products. Please try again later.');
        console.error('Error fetching products:', err);
      } finally {
        setLoading(false);
      }
    };

    fetchProducts();
  }, []); // Empty dependency array means this runs once on mount

  if (loading) {
    return (
      <div className={styles.productsLoading}>
        <Spinner />
        <p>Loading products...</p>
      </div>
    );
  }

  if (error) {
    return <div className={styles.productsError}>{error}</div>;
  }

  if (products.length === 0) {
    return <div className={styles.noProducts}>No products found.</div>;
  }

  return (
    <div className={styles.productsPage}>
      <h1>Our Products</h1>
      <div className={styles.productsGrid}>
        {products.map((product) => (
          <ProductCard key={product.id} product={product} />
        ))}
      </div>
    </div>
  );
};

export default ProductsListPage;
```

#### `src/features/Products/components/ProductCard/ProductCard.js` (Individual Product Card)

```javascript
// src/features/Products/components/ProductCard/ProductCard.js
import React from 'react';
import { Link } from 'react-router-dom';
import styles from './ProductCard.module.css'; // Assuming styling

const ProductCard = ({ product }) => {
  if (!product) return null;

  return (
    <Link to={`/products/${product.id}`} className={styles.productCard}>
      <div className={styles.productImage}>
        <img src={product.imageUrl || 'https://via.placeholder.com/150'} alt={product.name} />
      </div>
      <div className={styles.productInfo}>
        <h3 className={styles.productName}>{product.name}</h3>
        <p className={styles.productPrice}>${product.price ? product.price.toFixed(2) : 'N/A'}</p>
        <button className={styles.addToCartButton}>Add to Cart</button>
      </div>
    </Link>
  );
};

export default ProductCard;
```

#### `src/features/Products/components/ProductCard/ProductCard.module.css` (Basic styling for ProductCard)

```css
/* src/features/Products/components/ProductCard/ProductCard.module.css */
.productCard {
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
  text-decoration: none; /* Remove underline for Link */
  color: inherit; /* Inherit text color */
  display: flex;
  flex-direction: column;
}

.productCard:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
}

.productImage {
  width: 100%;
  height: 180px; /* Fixed height for consistent cards */
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f0f0f0;
}

.productImage img {
  width: 100%;
  height: 100%;
  object-fit: contain; /* Ensure the image fits within the box without cropping */
  display: block;
}

.productInfo {
  padding: 15px;
  display: flex;
  flex-direction: column;
  flex-grow: 1; /* Allow info section to grow */
}

.productName {
  font-size: 1.1em;
  font-weight: 600;
  margin-top: 0;
  margin-bottom: 10px;
  color: #333;
  min-height: 2.2em; /* Ensure consistent height for titles across cards */
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2; /* Limit to 2 lines */
  -webkit-box-orient: vertical;
}

.productPrice {
  font-size: 1.3em;
  color: #007bff;
  font-weight: bold;
  margin-top: auto; /* Push price and button to bottom */
  margin-bottom: 15px;
}

.addToCartButton {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.3s ease;
  width: 100%; /* Make button full width */
}

.addToCartButton:hover {
  background-color: #218838;
}
```

#### `src/features/Products/Products.module.css` (General Product List Styling)

```css
/* src/features/Products/Products.module.css */
.productsPage {
  padding: 30px;
  max-width: 1200px;
  margin: 0 auto;
}

.productsPage h1 {
  text-align: center;
  margin-bottom: 40px;
  color: #333;
}

.productsGrid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); /* Responsive grid */
  gap: 30px;
}

.productsLoading,
.productsError,
.noProducts {
  text-align: center;
  padding: 50px;
  font-size: 1.2em;
  color: #555;
}

.productsError {
  color: red;
}
```

#### `src/common/components/Spinner/Spinner.js` (Simple loading spinner)

```javascript
// src/common/components/Spinner/Spinner.js
import React from 'react';
import styles from './Spinner.module.css';

const Spinner = () => {
  return (
    <div className={styles.spinnerContainer}>
      <div className={styles.spinner}></div>
    </div>
  );
};

export default Spinner;
```

#### `src/common/components/Spinner/Spinner.module.css`

```css
/* src/common/components/Spinner/Spinner.module.css */
.spinnerContainer {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100px; /* Or whatever height is appropriate */
}

.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #007bff; /* Primary color */
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
```

---

### 5. Search Bar on Header

#### `src/common/components/SearchBar/SearchBar.js` (New SearchBar component)

```javascript
// src/common/components/SearchBar/SearchBar.js
import React, { useState } from 'react';
import styles from './SearchBar.module.css';

const SearchBar = ({ onSearch }) => {
  const [query, setQuery] = useState('');

  const handleChange = (e) => {
    setQuery(e.target.value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (query.trim()) {
      onSearch(query.trim()); // Pass the query to the parent handler
    }
  };

  return (
    <form className={styles.searchBar} onSubmit={handleSubmit}>
      <input
        type="text"
        placeholder="Search products..."
        value={query}
        onChange={handleChange}
        className={styles.searchInput}
      />
      <button type="submit" className={styles.searchButton}>
        <img src="/icons/search.svg" alt="Search" className={styles.searchIcon} />
        {/* You'll need to place a search.svg icon in your public/icons folder or assets/icons */}
      </button>
    </form>
  );
};

export default SearchBar;
```

#### `src/common/components/SearchBar/SearchBar.module.css` (Basic styling for SearchBar)

```css
/* src/common/components/SearchBar/SearchBar.module.css */
.searchBar {
  display: flex;
  border-radius: 5px;
  overflow: hidden;
  background-color: rgba(255, 255, 255, 0.1);
  transition: background-color 0.3s ease;
}

.searchBar:focus-within {
  background-color: rgba(255, 255, 255, 0.2);
}

.searchInput {
  border: none;
  padding: 8px 12px;
  background: transparent;
  color: white;
  outline: none;
  width: 200px; /* Adjust width as needed */
}

.searchInput::placeholder {
  color: rgba(255, 255, 255, 0.7);
}

.searchButton {
  background-color: transparent;
  border: none;
  padding: 8px 12px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.searchIcon {
  width: 18px;
  height: 18px;
  filter: invert(1); /* Makes a black icon white */
}
```

**Note:** For the `searchIcon`, you would need to have an `search.svg` file in your `public/icons` or `src/assets/icons` directory. If using `src/assets`, remember to import it: `import SearchIcon from '../../assets/icons/search.svg';` and use `{SearchIcon}` in `<img>` tag. For simplicity, I've assumed it's in `public/icons`.

---

### To Make it Run:

1.  **Backend API:** Ensure your backend API has endpoints like `/api/auth/login` and `/api/products` that return appropriate data (JWT token for login, array of product objects for products). You can use a simple mock API (like `json-server`) for development if your backend isn't ready.
    * Example `db.json` for `json-server`:
        ```json
        {
          "products": [
            { "id": "1", "name": "Laptop Pro", "price": 1200.00, "imageUrl": "https://via.placeholder.com/150/0000FF/FFFFFF?text=Laptop" },
            { "id": "2", "name": "Wireless Mouse", "price": 25.99, "imageUrl": "https://via.placeholder.com/150/FF0000/FFFFFF?text=Mouse" },
            { "id": "3", "name": "Mechanical Keyboard", "price": 99.50, "imageUrl": "https://via.placeholder.com/150/00FF00/000000?text=Keyboard" },
            { "id": "4", "name": "External SSD 1TB", "price": 89.99, "imageUrl": "https://via.placeholder.com/150/FFFF00/000000?text=SSD" }
          ],
          "auth": {
            "login": { "token": "mock_jwt_token_12345abcdef" }
          }
        }
        ```
    * To run `json-server`: `npm install -g json-server` then `json-server --watch db.json --port 5000` (adjust port if needed).
    * **Crucially, set `baseURL: 'http://localhost:5000/api'` in `src/common/services/api.js` if using `json-server` with `db.json` structure like `auth` and `products` as top-level keys.** If `json-server` serves them directly as `http://localhost:5000/products`, adjust `api.get('/products')` accordingly.

2.  **Run React App:** `npm start` or `yarn start`.

Now, when you navigate to `/login`, you can log in (with any username/password for the mock API), get redirected to `/dashboard`, and see the "Products" link in the header leading to the product list. The search bar will log its query to the console when used.