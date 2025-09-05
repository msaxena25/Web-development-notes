# Implementing Routing in React Native with React Navigation

React Navigation is the de-facto standard for routing and navigation in React Native applications. It provides a highly customizable and performant way to manage the flow between different screens in your app.

### Core Concepts of React Navigation

Before diving into code, let's understand some fundamental concepts:

1.  **Navigators:** These are components that define your navigation structure. Common types include:
    * **Stack Navigator:** Manages a stack of screens. New screens are pushed onto the stack, and you can pop back to previous ones. Ideal for sequential flows (e.g., login, registration, detail views).
    * **Tab Navigator:** Displays tabs (at the bottom or top) for switching between different sections of your app.
    * **Drawer Navigator:** Provides a sliding drawer menu (usually from the left or right) for navigating to different parts of the app.
    * **Native Stack Navigator:** A more performant stack navigator that uses native navigation primitives (often preferred for bare React Native projects).

2.  **Screens:** These are your individual React components that represent a distinct view or page in your application. Each screen component is typically rendered within a Navigator.

3.  **`NavigationContainer`:** This is the root component that manages your navigation tree. It must wrap all your navigators and screens. Think of it as the "brain" of your navigation.

4.  **`navigation` Prop:** Every screen component rendered by a navigator automatically receives a `navigation` prop. This prop contains methods (like `Maps`, `goBack`, `push`, `replace`) that allow you to programmatically control navigation.

5.  **`route` Prop:** Also automatically passed to screen components, the `route` prop contains information about the current route, including its name and any parameters passed to it.

### Step-by-Step Implementation
---

#### Step 1: Install Required Packages

You'll need the core React Navigation library and the specific navigator you want to use. We'll start with the `NativeStack` Navigator, which is generally recommended for bare React Native projects for performance.

Open your terminal in your project root and run:

```bash
# Core navigation library
npm install @react-navigation/native

# Dependencies required by React Navigation
npm install react-native-screens react-native-safe-area-context

# Stack Navigator (using the native stack version)
npm install @react-navigation/native-stack

# For Expo users: use `npx expo install` instead of `npm install` for managed dependencies
# npx expo install @react-navigation/native react-native-screens react-native-safe-area-context @react-navigation/native-stack
```

**Important:** For `react-native-screens` on iOS, make sure you have CocoaPods installed and run `cd ios && pod install` after installation. For Android, no extra steps are usually needed.

---

#### Step 2: Wrap your App with `NavigationContainer`

The `NavigationContainer` is essential. It should typically wrap your entire application's navigation structure. You'll usually put it in your main `App.js` (or `App.tsx`) file.

**`App.js` (or `App.tsx`)**

```javascript
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
// We'll import our screens and navigators here soon

function App() {
  return (
    // NavigationContainer must wrap all your navigators
    <NavigationContainer>
      {/* Your navigators will go here */}
      <Text>Navigation setup will go here soon!</Text>
    </NavigationContainer>
  );
}

export default App;
```

---

#### Step 3: Create Your Screen Components

Let's create two simple screen components: `HomeScreen` and `DetailsScreen`. It's good practice to put your screen components in a dedicated folder (e.g., `src/screens` or just `screens` in the root).

**Create a `screens` folder:**
```
my-react-native-app/
├── screens/
│   ├── HomeScreen.js
│   └── DetailsScreen.js
├── App.js
└── ...
```

**`screens/HomeScreen.js`**

```javascript
import React from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';

// Screen components receive `navigation` and `route` props automatically
function HomeScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Welcome to the Home Screen!</Text>
      <Button
        title="Go to Details"
        onPress={() => navigation.navigate('Details', { itemId: 86, otherParam: 'anything you want' })}
      />
    </View>
  );
}

export default HomeScreen;
```

**`screens/DetailsScreen.js`**

```javascript
import React from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';

function DetailsScreen({ navigation, route }) {
  // Access parameters passed from the previous screen via `route.params`
  const { itemId, otherParam } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.text}>Details Screen</Text>
      <Text style={styles.paramText}>Item ID: {JSON.stringify(itemId)}</Text>
      <Text style={styles.paramText}>Other Param: {JSON.stringify(otherParam)}</Text>

      <Button
        title="Go to Home"
        onPress={() => navigation.navigate('Home')}
      />
      <Button
        title="Go back"
        onPress={() => navigation.goBack()}
        // `goBack()` navigates to the previous screen in the stack
      />
      <Button
        title="Update the title"
        onPress={() => navigation.setOptions({ title: 'Updated!' })}
        // `setOptions` allows you to dynamically change navigator options for the current screen
      />
    </View>
  );
}

export default DetailsScreen;
```

---

#### Step 4: Set Up Your Stack Navigator

Now, let's integrate these screens into a `NativeStack` Navigator within your `App.js`.

**`App.js` (or `App.tsx`)**

```javascript
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack'; // Import the Stack Navigator

// Import your screen components
import HomeScreen from './screens/HomeScreen';
import DetailsScreen from './screens/DetailsScreen';

// Create a Stack Navigator instance
const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        // `initialRouteName` sets the screen that should be rendered first
        initialRouteName="Home"
        screenOptions={{
          // Global options for all screens in this navigator (can be overridden per screen)
          headerStyle: {
            backgroundColor: '#6200EE', // Example header background color
          },
          headerTintColor: '#fff', // Example header text color
          headerTitleStyle: {
            fontWeight: 'bold',
          },
        }}
      >
        {/* Define each screen that belongs to this navigator */}
        <Stack.Screen
          name="Home" // The unique name for this screen
          component={HomeScreen} // The component to render for this screen
          options={{
            // Specific options for the Home screen (overrides global `screenOptions`)
            title: 'My Awesome App', // Title displayed in the header
          }}
        />
        <Stack.Screen
          name="Details"
          component={DetailsScreen}
          options={({ route }) => ({
            // Dynamic options: Access route params to customize title
            title: `Details: ${route.params.itemId}`,
          })}
        />
        {/* You can add more Stack.Screen components here for other screens */}
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

---


### Advanced React Navigation Concepts

This is just the tip of the iceberg. Here are some advanced topics you'll likely encounter:

* **Passing Parameters:** As shown in the example, you can pass data between screens using the second argument of `navigation.navigate()`. Access them via `route.params`.
* **Nested Navigators:** You can nest navigators (e.g., a Tab Navigator inside a Stack Navigator) to create complex navigation flows.
    ```javascript
    // Example of a Tab Navigator nested inside a Stack Navigator
    const Tab = createBottomTabNavigator();

    function HomeTabs() {
      return (
        <Tab.Navigator>
          <Tab.Screen name="Feed" component={FeedScreen} />
          <Tab.Screen name="Settings" component={SettingsScreen} />
        </Tab.Navigator>
      );
    }

    const Stack = createNativeStackNavigator();

    function App() {
      return (
        <NavigationContainer>
          <Stack.Navigator>
            <Stack.Screen name="HomeTabs" component={HomeTabs} options={{ headerShown: false }} />
            <Stack.Screen name="Profile" component={ProfileScreen} />
          </Stack.Navigator>
        </NavigationContainer>
      );
    }
    ```
* **Authentication Flow:** For apps with authentication, you often have different navigators for authenticated and unauthenticated states. You can conditionally render these based on user login status.
    ```javascript
    // Simplified example
    function App() {
      const [isLoggedIn, setIsLoggedIn] = React.useState(false); // Get this from context/redux

      return (
        <NavigationContainer>
          {isLoggedIn ? <AuthenticatedStack /> : <AuthStack />}
        </NavigationContainer>
      );
    }
    ```
--------------

# Expo Router

---

### React Navigation

**Philosophy:** **Configuration-first, Imperative, Highly Customizable.**

React Navigation is a standalone, general-purpose navigation library that works with both bare React Native and Expo projects. You explicitly define your navigation structure in code.

**Key Features:**
* **Diverse Navigators:** Offers a wide range of pre-built navigators (Stack, Tab, Drawer, Material Top/Bottom Tabs, Modal, etc.)
* **Highly Customizable:** Every aspect of navigation, including headers, transitions, gestures, and animations, can be deeply customized using props and options.
* **Nested Navigators:** Seamlessly nest different types of navigators to create complex UI flows.
* **Parameter Passing:** Easy to pass data between screens using `route.params`.
* **Hooks API:** Provides `useNavigation`, `useRoute`, `useFocusEffect`, etc., for simplified interaction with navigation state in functional components.
* **Deep Linking:** Robust support for deep linking to specific screens from external URLs, though it requires manual configuration.
* **Web Support:** Can be used for web applications (React Native for Web) to share navigation logic across platforms.
* **Mature Ecosystem:** Has been around for a long time, leading to extensive documentation, a large community, and many third-party integrations and examples.

### Expo Router

**Philosophy:** **Convention-over-Configuration, File-based, Universal-first.**

Expo Router is a routing library built by the Expo team specifically for Expo projects. It builds on top of React Navigation but abstracts away much of the manual configuration by using your file system as the source of truth for routes.

**How it Works:**
1.  **File-Based Routing:** Your project's directory structure (typically within an `app/` folder) directly maps to your application's routes.
    * `app/index.tsx` -> `/` (root route)
    * `app/profile.tsx` -> `/profile`
    * `app/settings/index.tsx` -> `/settings`
    * `app/users/[id].tsx` -> `/users/:id` (dynamic route segment)

2.  **Automatic Discovery:** Metro Bundler (used by Expo) automatically discovers and configures routes based on your file names.
3.  **Layouts (`_layout.tsx`):** Special files named `_layout.tsx` (or `_layout.js`) within directories define the shared UI and navigation structure for all nested routes. This is where you configure `Stack`, `Tab`, or `Drawer` navigators.
4.  **Declarative/Imperative Mix:** While the structure is declarative (file-based), you can still navigate imperatively using `router.push()`, `router.navigate()`, `router.goBack()`, etc., similar to React Navigation.
5.  **Universal by Design:** Designed from the ground up for universal apps (iOS, Android, Web) with shared routing logic and automatic deep linking.

**Key Features:**
* **Zero-Config Setup (Mostly):** Very quick to get started with basic routing as it infers routes from your file system.
* **Automatic Deep Linking:** Deep linking is handled automatically based on your file structure, requiring less manual setup.
* **Type Safety (with TypeScript):** Automatically generates types for your routes, providing compile-time safety for navigation parameters.
* **Web-First Features:** Supports web-specific features like static rendering, API routes (similar to Next.js API routes), and `Link` components that render as `<a>` tags on the web.
* **Layouts:** Powerful `_layout.tsx` files for defining shared UI and nested navigators for entire sections of your app.
* **Auth Flow Primitives:** Built-in patterns and hooks for handling authentication flows (e.g., redirecting unauthenticated users).
* **Built on React Navigation:** Under the hood, Expo Router uses React Navigation. This means it leverages the robust foundation of React Navigation for native navigation primitives.

**When to Use Expo Router:**
* **New Expo projects.**
* **When rapid development and ease of setup are priorities.**
* **Projects that require strong universal (iOS, Android, Web) support** with shared code and deep linking.
* **Teams that prefer convention-over-configuration** and a structured approach to routing.
* **Developers coming from web frameworks like Next.js** who are familiar with file-based routing.
* **When type-safe routing** is a high priority.

---

# Why React native use Stack for navigation?

Great question! Let’s break it down 💡

### 📚 What React Navigation Is Doing Here

React Navigation is a powerful routing and navigation library for React Native apps. The line:

```js
const Stack = createNativeStackNavigator();
```

sets up a **stack navigator**, which is one of the most common navigation patterns.

---

### 🧱 Why Use a Stack Navigator?

The **stack** mimics how screens are added and removed in a mobile app—like pages on a stack of paper. It makes sense for flows where you:

- Push a new screen when navigating (e.g. from Home → Details)
- Pop the screen when going back (e.g. Details → Home)

---

### 🛠 What `createNativeStackNavigator()` Does

- It creates a stack-based navigation system.
- Powered under the hood by **native code** (via `react-native-screens`) for better performance on mobile devices.
- Enables transitions, headers, and gestures that feel natural on iOS and Android.

---

### ✅ Pros of Using Stack Navigation

- **Intuitive UX**: Users expect this navigation flow on mobile.
- **Memory Efficiency**: Only the top screen is active, others are paused.
- **Native Performance**: Faster and smoother than JavaScript-based stacks.
- **Customizable**: You can control headers, animations, gestures, etc.

---

# What are Building blocks to create react native navigation ?
---

### 🧱 Core Building Blocks of React Native Navigation

1. **Navigators**
   - These are the **containers** that define how screens relate to each other.
   - Popular ones:
     - `Stack Navigator`: Push/pop behavior like a history stack.
     - `Tab Navigator`: Bottom tabs or top tabs for switching between sections.
     - `Drawer Navigator`: Side menu that slides in from the edge.
   - Created using:
     ```js
     import { createNativeStackNavigator } from '@react-navigation/native-stack';
     ```

2. **Screens**
   - Individual React components that you navigate between.
   - You register them inside navigators:
     ```js
     <Stack.Screen name="Home" component={HomeScreen} />
     ```

3. **Navigation Container**
   - Wraps the whole navigation structure.
   - Think of it as the **brain** that keeps track of state and deep linking.
     ```js
     import { NavigationContainer } from '@react-navigation/native';
     ```

4. **Navigators from React Navigation**
   - Provided by `@react-navigation/native`, `@react-navigation/native-stack`, etc.
   - These let you pick between native and JavaScript-based stack implementations.

5. **Navigation Props & Hooks**
   - `useNavigation`, `useRoute`: Give you access to navigation actions and route details inside your components.
   - Useful for programmatic navigation like:
     ```js
     navigation.navigate("Profile", { userId: 123 });
     ```

6. **Params & State**
   - You can pass **parameters** between screens.
   - Navigation state tracks where your user is and what screens are stacked.

7. **Custom Headers & Transition Animations**
   - Built-in or user-defined to make UI look slick and fit your app’s style.

---


# All common React Navigation methods with **code examples** and **2-line use cases** each:

---

### ✅ 1. `navigate(name, params)`

```js
navigation.navigate('Profile', { userId: 42 });
```

**Use case:** Navigate to a screen and pass data like user ID or product ID.

---

### ✅ 2. `push(name, params)`

```js
navigation.push('Profile', { userId: 42 });
```

**Use case:** Push another instance of the same screen onto the stack (e.g., viewing multiple users' profiles one after another).

---

### ✅ 3. `goBack()`

```js
navigation.goBack();
```

**Use case:** Go back to the previous screen in the stack — similar to a browser "Back" button.

---

### ✅ 4. `replace(name, params)`

```js
navigation.replace('Login', { redirectTo: 'Home' });
```

**Use case:** Replace the current screen (e.g., after logout or signup success) so the user can’t go back.

---

### ✅ 5. `reset(state)`

```js
navigation.reset({
  index: 0,
  routes: [{ name: 'Home' }],
});
```

**Use case:** Wipe navigation history and start fresh (like after onboarding or logout).

---

### ✅ 6. `pop()`

```js
navigation.pop();
```

**Use case:** Go back one screen — same as `goBack()`, but useful if you need more control in stacks.

---

### ✅ 7. `popToTop()`

```js
navigation.popToTop();
```

**Use case:** Return to the first screen in the stack (e.g., after completing a multi-step form).

---

### ✅ 8. `setParams(params)`

```js
navigation.setParams({ title: 'Updated Title' });
```

**Use case:** Update route params dynamically (e.g., to change the screen title based on user input).

---

### ✅ 9. `navigate({...})` with nested screens

```js
navigation.navigate('Account', {
  screen: 'Settings',
  params: { mode: 'dark' },
});
```

**Use case:** Navigate to a screen inside a nested navigator (like Tab or Drawer inside a Stack).

---


# How to create a custom drawer navigator with nested items in react native?

To create a custom drawer navigator with nested items in React Native, you can use thereact-navigation library along with a custom drawer content component. In the custom drawer content,you can create sections with headers that can be expanded or collapsed to show or hide nested items.You can manage the expanded state using React's useState hook and render the nested items conditionallybased on this state. The example above demonstrates how to implement this functionality.