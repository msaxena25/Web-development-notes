### **Fundamental Concepts**

1.  **What is React?**
    React is a free and open-source front-end JavaScript library for building user interfaces based on components. It is maintained by Meta and a community of individual developers and companies.

2.  **What are the major features of React?**
    The major features are the use of a **Virtual DOM** for performance, **component-based architecture** for reusability, **JSX** for writing UI, and **unidirectional data flow** for predictability.

3.  **What is JSX?**
    JSX (JavaScript XML) is a syntax extension for JavaScript that allows you to write HTML-like code within your JavaScript files. It makes writing React components more intuitive and readable.

4.  **Can browsers read JSX directly?**
    No, browsers cannot read JSX directly. It must be transpiled into regular JavaScript using a tool like **Babel** before it can be executed in a browser.

5.  **What is the difference between a React Element and a Component?**
    An **element** is a plain object describing what you want to see on the screen, like a DOM node or another component. A **component** is a reusable piece of code (a function or a class) that returns a React element.

6.  **What is the Virtual DOM?**
    The Virtual DOM (VDOM) is an in-memory, lightweight representation of the real DOM. React uses it to efficiently update the UI by comparing VDOM versions and only applying the necessary changes to the actual DOM.

7.  **What is `create-react-app`?**
    `create-react-app` is an officially supported command-line tool that sets up a modern React web application with a pre-configured build setup. It saves you from manual configuration of tools like Webpack and Babel.

8.  **What are Fragments?**
    Fragments (`<></>`) are a feature in React that let you group a list of children elements without adding extra nodes to the DOM. This helps in keeping the rendered HTML cleaner.

9.  **Why do we need keys when rendering lists?**
    Keys are special string attributes that help React identify which items in a list have changed, been added, or been removed. They are crucial for efficient list re-rendering and maintaining component state.

10. **What is the difference between `class` and `className` in React?**
    Since `class` is a reserved keyword in JavaScript, React uses `className` to specify the CSS class for an element. JSX gets compiled to JavaScript, hence the need for this distinction.

11. **What are synthetic events in React?**
    Synthetic events are React's cross-browser wrappers around the browser's native events. They provide a consistent API, making event handling predictable across different browsers.

12. **How do you write comments in React (JSX)?**
    You write comments in JSX using JavaScript syntax within curly braces. For multi-line comments, you use ` {/* comment */} `.

13. **What is the purpose of the `public` folder in a React project?**
    The `public` folder contains static assets that don't get processed by Webpack, such as the main `index.html` file, favicons, and images. Files in this folder are accessible directly from the browser.

14. **What is reconciliation?**
    Reconciliation is the process by which React updates the DOM. When a component's state or props change, React compares the new Virtual DOM with the old one and efficiently updates the real DOM with the differences.

15. **What is unidirectional data flow?**
    Unidirectional data flow means that data in a React application flows in a single direction, from parent components down to child components via props. This makes the application's state more predictable and easier to debug.

***

### **Components & Props**

16. **What is the difference between a functional and a class component?**
    **Functional components** are simple JavaScript functions that accept props and return JSX. **Class components** are ES6 classes that extend `React.Component` and have their own state and lifecycle methods. With Hooks, functional components can now manage state and side effects as well.

17. **What are props in React?**
    Props (short for properties) are read-only inputs passed from a parent component to a child component. They allow for the dynamic configuration and rendering of components.

18. **Can you change props in a component?**
    No, props are **immutable** (read-only) from the perspective of the receiving component. A component must never modify its own props.

19. **What is prop drilling?**
    Prop drilling is the process of passing props down through multiple layers of nested components that do not need the props themselves, just to get them to a deeply nested child. It can be avoided using Context API or state management libraries.

20. **What is "lifting state up"?**
    "Lifting state up" is a common pattern where you move the shared state to the closest common ancestor of the components that need it. This allows the components to share and manipulate the same state data.

21. **What are controlled components?**
    A controlled component is a form element (like `<input>`) whose value is controlled by React state. The value of the input is set by the component's state, and any changes are handled by an `onChange` handler.

22. **What are uncontrolled components?**
    An uncontrolled component is a form element where the form data is handled by the DOM itself, not by React state. You typically use a `ref` to get the form value when needed.

23. **What are Higher-Order Components (HOCs)?**
    A Higher-Order Component is an advanced pattern for reusing component logic. It is a function that takes a component as an argument and returns a new, enhanced component.

24. **What is the `children` prop?**
    The `children` prop (`props.children`) is a special prop that allows you to pass components or elements as data between the opening and closing tags of a component. It is used to create flexible and reusable container components.

25. **What is conditional rendering?**
    Conditional rendering is the process of rendering different UI markup based on certain conditions (e.g., state or props). It can be achieved using `if-else` statements, ternary operators, or logical `&&` operators.

***

### **State & Lifecycle**

26. **What is state in React?**
    State is an object that represents the parts of a component that can change over time. It is managed within the component and, when it changes, causes the component to re-render.

27. **How do you update a component's state?**
    In a class component, you use `this.setState()`. In a functional component, you use the update function returned by the `useState` hook. Direct mutation of the state object (e.g., `this.state.key = value`) will not work.

28. **Why should you not modify the state directly?**
    Modifying the state directly does not trigger a re-render of the component, leading to an inconsistent UI. Using `setState()` or a state updater function ensures that React is aware of the change and can update the DOM accordingly.

29. **What are the three main phases of a class component's lifecycle?**
    The three main phases are **Mounting** (putting elements into the DOM), **Updating** (re-rendering due to changes in state or props), and **Unmounting** (removing components from the DOM).

30. **What is the purpose of `componentDidMount()`?**
    `componentDidMount()` is a lifecycle method that runs after the component has been rendered to the DOM for the first time. It is commonly used for initiating network requests or setting up subscriptions.

31. **What is the purpose of `componentWillUnmount()`?**
    `componentWillUnmount()` is a lifecycle method that runs just before a component is removed from the DOM. It is used for cleanup tasks, such as invalidating timers, canceling network requests, or cleaning up subscriptions.

32. **What is the purpose of `shouldComponentUpdate()`?**
    `shouldComponentUpdate()` is a lifecycle method used for performance optimization. It allows you to control whether a component should re-render after receiving new props or state, preventing unnecessary renders.

33. **What are error boundaries?**
    Error boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI. They prevent the entire application from crashing due to a single component error.

34. **How do you create an error boundary?**
    You can create an error boundary by defining a class component with either a `static getDerivedStateFromError()` or `componentDidCatch()` lifecycle method.

35. **What is the difference between `componentDidUpdate` and `useEffect`?**
    `componentDidUpdate` is a lifecycle method in class components that runs after an update. `useEffect` is a Hook in functional components that can replicate the functionality of `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` combined.

***

### **React Hooks**

36. **What are React Hooks?**
    Hooks are functions that let you "hook into" React state and lifecycle features from functional components. They allow you to use state and other React features without writing a class.

37. **What are the rules of Hooks?**
    The two main rules are: 1) Only call Hooks at the **top level** of your React functions (not inside loops, conditions, or nested functions). 2) Only call Hooks from **React functional components** or custom Hooks.

38. **What does the `useState` Hook do?**
    The `useState` Hook allows you to add a state variable to a functional component. It returns an array containing the current state value and a function to update it.

39. **What does the `useEffect` Hook do?**
    The `useEffect` Hook lets you perform side effects in functional components. It is a close replacement for `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` in class components.

40. **How do you run an effect only once after the initial render?**
    You can run an effect only once by passing an empty dependency array (`[]`) as the second argument to the `useEffect` Hook. This tells React that the effect does not depend on any props or state, so it never needs to re-run.

41. **What is the purpose of the `useContext` Hook?**
    The `useContext` Hook allows a functional component to subscribe to React context without introducing nesting. It provides a way to pass data through the component tree without having to pass props down manually at every level.

42. **What does the `useRef` Hook do?**
    The `useRef` Hook returns a mutable `ref` object whose `.current` property can be used to hold a value that persists across renders. It is commonly used to access a DOM element directly.

43. **What is the difference between `useMemo` and `useCallback`?**
    `useMemo` **memoizes a value**, returning the cached result of a function call. `useCallback` **memoizes a function**, returning the function itself, which is useful for preventing re-renders in child components that rely on function props.

44. **What is a custom Hook?**
    A custom Hook is a reusable JavaScript function whose name starts with "use" and that can call other Hooks. It allows you to extract component logic into reusable functions, sharing stateful logic between components.

45. **What problem does the `useReducer` Hook solve?**
    The `useReducer` Hook is an alternative to `useState` for managing more complex component state logic. It is particularly useful when you have state that involves multiple sub-values or when the next state depends on the previous one.

***

### **Advanced Concepts & Ecosystem**

46. **What is Redux?**
    Redux is a predictable state container for JavaScript applications, often used with React. It helps you manage your application's state in a single, global store, making state management more predictable and easier to trace.

47. **What are the core principles of Redux?**
    The three core principles are: 1) **Single source of truth** (the state of your whole application is stored in a single store). 2) **State is read-only** (the only way to change the state is to emit an action). 3) **Changes are made with pure functions** (reducers describe how the state tree is transformed by actions).

48. **What is React Router?**
    React Router is the standard library for handling routing in React applications. It enables navigation among different components in a single-page application, allowing the user's view to be synchronized with the URL.

49. **How do you optimize the performance of a React application?**
    Performance can be optimized by using `React.memo` for components, memoizing values with `useMemo` and `useCallback`, using code-splitting with `React.lazy` and `Suspense`, and virtualizing long lists.

50. **What is Server-Side Rendering (SSR) in the context of React?**
    Server-Side Rendering is the ability of an application to render the initial page on the server instead of in the browser. This can improve performance by showing content to the user faster and is beneficial for SEO.

-----------


Okay, here are 30 questions covering build, compile, tools, deployment, memory, and testing, with concise answers suitable for a written paper:

---

### **Build**

1.  **What is the "build" process in software development?**
    The build process transforms source code and assets into an executable application. It typically involves compilation, linking, packaging, and optimizing for deployment.

2.  **What is the purpose of a build tool?**
    Build tools automate the creation of executable applications from source code. They manage dependencies, compile code, run tests, and package the final output.

3.  **Name some common build tools for web development.**
    Common build tools include Webpack, Parcel, Vite (for bundlers), Gulp, and Grunt (for task runners). Maven and Gradle are popular in Java ecosystems.

4.  **What are build artifacts?**
    Build artifacts are the generated files or components produced during the build process. Examples include compiled code, executable binaries, compressed archives, and deployable packages.

5.  **What is the difference between a "clean build" and an "incremental build"?**
    A **clean build** compiles all source code from scratch, ignoring previous build outputs. An **incremental build** only recompiles changed source code and its dependencies, which is faster.

---

### **Compile**

6.  **What is compilation in programming?**
    Compilation is the process of translating source code written in a high-level programming language into low-level machine code or bytecode that a computer can execute.

7.  **What is a compiler?**
    A compiler is a software program that takes source code written in one programming language and translates it into another computer language (often machine code or bytecode) for execution.

8.  **Explain the difference between compilation and interpretation.**
    **Compilation** translates the entire program into machine code before execution. **Interpretation** translates and executes the code line by line at runtime without a separate compilation step.

9.  **What is a transpiler? Give an example.**
    A transpiler is a type of compiler that translates source code from one high-level language to another high-level language. An example is Babel, which transpiles modern JavaScript (ES6+) into older JavaScript (ES5) for wider browser compatibility.

10. **What is bytecode?**
    Bytecode is an intermediate-level, platform-independent code compiled from source code (e.g., Java, Python). It is then executed by a virtual machine (e.g., JVM, CPython VM) specific to the platform.

---

### **Tools**

11. **What is Git and why is it used?**
    Git is a distributed version control system used for tracking changes in source code during software development. It enables multiple developers to collaborate on projects without conflicts.

12. **What is Docker and what problem does it solve?**
    Docker is a platform for developing, shipping, and running applications in isolated environments called containers. It solves the "it works on my machine" problem by packaging code and dependencies consistently.

13. **What is CI/CD?**
    CI/CD stands for Continuous Integration (CI) and Continuous Delivery/Deployment (CD). CI automatically merges code changes into a central repository and runs tests. CD automates the delivery or deployment of validated code to production.

14. **Name a popular CI/CD tool.**
    Popular CI/CD tools include Jenkins, GitLab CI/CD, GitHub Actions, CircleCI, Travis CI, and Azure DevOps.

15. **What is a linter and why is it useful?**
    A linter is a static code analysis tool that checks source code for programmatic and stylistic errors, potential bugs, and bad practices. It helps enforce code quality, consistency, and identify issues early.

---

### **Deployment**

16. **What is software deployment?**
    Software deployment is the process of making a software application available for use. It includes all activities involved in preparing, installing, configuring, and running the software in a specific environment.

17. **What are the different deployment environments typically used?**
    Common deployment environments include Development, Testing (QA/Staging), User Acceptance Testing (UAT), and Production. Each serves a distinct purpose in the software lifecycle.

18. **Explain Blue/Green Deployment.**
    Blue/Green Deployment is a strategy where two identical production environments ("Blue" and "Green") are maintained. Only one is active at a time. New releases are deployed to the inactive environment, tested, and then traffic is switched, minimizing downtime.

19. **What is a rollback in deployment?**
    A rollback is the process of reverting a deployed application to a previous, stable version. It's typically performed when a new deployment introduces critical bugs or issues in production.

20. **What is continuous deployment (CD)?**
    Continuous Deployment is an extension of Continuous Delivery where every change that passes automated tests is automatically deployed to production without human intervention.

---

### **Memory Management**

21. **What is memory management in programming?**
    Memory management is the process of allocating and deallocating computer memory to running programs and data. Its goal is to optimize system performance and ensure efficient use of resources.

22. **Explain the difference between Stack and Heap memory.**
    **Stack** memory is used for static memory allocation (e.g., local variables, function calls), with LIFO (Last-In, First-Out) access. **Heap** memory is used for dynamic memory allocation (e.g., objects, arrays) and is managed by the system's garbage collector.

23. **What is garbage collection?**
    Garbage collection is an automatic memory management process that reclaims memory occupied by objects that are no longer referenced or reachable by the program. It prevents memory leaks and simplifies development.

24. **What is a memory leak?**
    A memory leak occurs when a program allocates memory that is no longer needed but fails to deallocate it, making that memory unavailable for other processes. Over time, this can lead to performance degradation or system crashes.

25. **How can you typically identify a memory leak?**
    Memory leaks can be identified by monitoring memory usage over time, observing steadily increasing consumption. Tools like browser developer tools (memory profilers), `valgrind`, or dedicated APM solutions help pinpoint leaked resources.

---

### **Testing**

26. **What is software testing?**
    Software testing is the process of evaluating a software application to verify that it meets specified requirements, identify defects or errors, and ensure its quality and correctness.

27. **What are the different levels of software testing?**
    The main levels are Unit Testing (individual components), Integration Testing (interactions between components), System Testing (entire system), and Acceptance Testing (user requirements).

28. **Explain Unit Testing.**
    Unit testing is the lowest level of testing, where individual units or components of a software application are tested in isolation. It aims to verify that each unit of code performs as expected.

29. **What is Test-Driven Development (TDD)?**
    TDD is a software development approach where tests are written **before** the actual code. You write a failing test, then write just enough code to make the test pass, and then refactor the code.

30. **What is a mock object in testing?**
    A mock object is a simulated object that mimics the behavior of real objects in a controlled way during testing. It's used to isolate the component being tested from its dependencies, allowing focused unit testing.