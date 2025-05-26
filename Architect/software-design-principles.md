# **Software Design Principles** and **Patterns** 

---

### **Design Principles**

#### 1. **Single Responsibility Principle (SRP)**
   - **Definition**: Each class or module should have one responsibility or reason to change.
   - **Example**: A service class only handling API calls and not UI logic.

#### 2. **Open/Closed Principle (OCP)**
   - **Definition**: Classes should be open for extension but closed for modification.
   - **Example**: Adding new functionality via inheritance or interfaces without altering existing code.

#### 3. **Liskov Substitution Principle (LSP)**
   - **Definition**: Subclasses should be replaceable with their parent class without breaking the application.
   - **Example**: A derived class implementing a method from its base class without unexpected behavior.

#### 4. **Interface Segregation Principle (ISP)**
   - **Definition**: Avoid creating large interfaces; instead, create smaller, specific interfaces.
   - **Example**: Separate interfaces for `Readable` and `Writable` rather than a single `FileManager`.

#### 5. **Dependency Inversion Principle (DIP)**
   - **Definition**: Depend on abstractions, not on concrete implementations.
   - **Example**: Using interfaces or abstract classes for dependency injection in Angular services.

---

# **some software design patterns** 

---

### **1. Singleton Pattern**
- **Purpose**: Ensures a class has only one instance and provides a global access point.
- **Use Case**: Managing configurations, logging, or database connections.
- **Example**: A configuration service that is shared across an entire Angular/React application.

---

### **2. Factory Pattern**
- **Purpose**: Provides a way to create objects without specifying their concrete class.
- **Use Case**: Dynamically creating different objects based on conditions.
- **Example**: A factory that creates different HTTP request handlers based on the API endpoint.

---

### **3. Observer Pattern**
- **Purpose**: Defines a one-to-many relationship where changes in one object notify dependent objects.
- **Use Case**: Event-driven programming, real-time notifications, and Angular’s `Observable`.
- **Example**: React’s state management with `Redux` or `MobX`.

---

### **4. MVC (Model-View-Controller)**
- **Purpose**: Separates an application into three layers: Model (data), View (UI), and Controller (logic).
- **Use Case**: Structuring large web applications.
- **Example**: Frameworks like Ruby on Rails, Laravel, and ASP.NET MVC implement this pattern.

---

### **5. Repository Pattern**
- **Purpose**: Provides an abstraction over data access, decoupling business logic from data sources.
- **Use Case**: Accessing data from databases or APIs.
- **Example**: A `UserRepository` that fetches user data irrespective of whether it’s a database or an API.

---

### **6. Decorator Pattern**
- **Purpose**: Dynamically adds behavior or responsibilities to objects without modifying their structure.
- **Use Case**: Adding middleware in Express.js or enhancing components in Angular.
- **Example**: Adding authentication checks to API routes in a Node.js app.

---

### **7. Proxy Pattern**
- **Purpose**: Acts as a placeholder to control access to an object.
- **Use Case**: Caching, authorization, or lazy-loading resources.
- **Example**: A proxy that validates API requests before sending them to the server.

---

### **8. Dependency Injection (DI)**
- **Purpose**: Decouples object creation by providing dependencies from an external source.
- **Use Case**: Managing dependencies in frameworks like Angular or Spring.
- **Example**: Injecting a `UserService` into a `LoginComponent`.

---

### **9. Builder Pattern**
- **Purpose**: Constructs complex objects step-by-step, separating construction from representation.
- **Use Case**: Creating objects with many optional properties.
- **Example**: Constructing a complex query in SQL or a flexible form configuration in a web app.

---

### **10. Strategy Pattern**
- **Purpose**: Defines a family of algorithms, encapsulates them, and allows switching between them dynamically.
- **Use Case**: Handling multiple payment methods or dynamic sorting logic.
- **Example**: Choosing different compression algorithms based on file size.

---

### **Summary Table**

| **Pattern**         | **Purpose**                                                                                   | **Example in Web Development**                                  |
|----------------------|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| **Singleton**        | One shared instance for global state management.                                             | Config, logging, database connection.                         |
| **Factory**          | Dynamic object creation without knowing specific classes.                                     | Creating request handlers for APIs.                           |
| **Observer**         | Reacts to state changes and notifies subscribers.                                             | Angular `Observable`, Redux.                                  |
| **MVC**              | Separates data, logic, and UI.                                                               | Frameworks like Laravel, Rails.                               |
| **Repository**       | Abstracts data access logic.                                                                 | Centralized API/data fetching logic.                          |
| **Decorator**        | Adds functionality dynamically.                                                              | Middleware in Express.js.                                     |
| **Proxy**            | Controls access to resources.                                                                | API request validation, caching.                              |
| **Dependency Injection** | Decouples object creation and usage.                                                         | Angular DI system.                                            |
| **Builder**          | Constructs complex objects step-by-step.                                                     | SQL query builders, dynamic forms.                            |
| **Strategy**         | Enables algorithm switching.                                                                 | Payment method handling, file compression algorithms.         |

-----

# **What design patterns Angular uses**

---

### **1. Dependency Injection (DI)**
- **Description**: Angular uses DI to manage the creation and lifecycle of objects, injecting them where required.
- **Purpose**: Reduces tight coupling between components and services.
- **Example**: Services injected into components using Angular’s injector framework.

---

### **2. Observer Pattern**
- **Description**: Angular leverages observables from RxJS for event handling, asynchronous programming, and data streams.
- **Purpose**: Enables reactive programming by notifying subscribers of state changes.
- **Example**: `HttpClient` methods return observables, and components can subscribe to them.

---

### **3. Singleton Pattern**
- **Description**: Services in Angular provided at the root level (`providedIn: 'root'`) are singleton instances.
- **Purpose**: Ensures a single shared instance across the app.
- **Example**: `AuthService` managing user authentication across components.

---

### **4. Component-Based Architecture**
- **Description**: Angular uses a component-based structure to modularize the UI.
- **Purpose**: Promotes reusability and separation of concerns.
- **Example**: Every feature is encapsulated in components, like `HeaderComponent`, `FooterComponent`.

---

### **5. Module Pattern**
- **Description**: Angular modules (`NgModule`) encapsulate related components, directives, pipes, and services.
- **Purpose**: Provides logical grouping and lazy loading.
- **Example**: `AppModule`, `UserModule`, or `SharedModule`.

---

### **6. Factory Pattern**
- **Description**: Factories are used to create services dynamically.
- **Purpose**: Provides flexibility in object creation based on runtime conditions.
- **Example**: Angular’s `FactoryProvider` in DI configuration.

---

### **7. Proxy Pattern**
- **Description**: Used in Angular's HTTP Interceptors to intercept and modify requests or responses.
- **Purpose**: Adds pre-processing logic for HTTP requests and responses.
- **Example**: Adding authorization headers via an interceptor.

---

### **8. Mediator Pattern**
- **Description**: Angular’s EventEmitter facilitates communication between components and services, acting as a mediator.
- **Purpose**: Promotes decoupled communication between components.
- **Example**: A child component emitting an event to a parent component.

---

### **9. Strategy Pattern**
- **Description**: Angular Forms (Reactive and Template-Driven) allow choosing strategies for form handling.
- **Purpose**: Offers flexibility in form validation and state management.
- **Example**: Reactive forms using dynamic validators for custom logic.

---

### **10. Builder Pattern**
- **Description**: Used in Angular Forms to create complex forms step-by-step.
- **Purpose**: Helps build dynamic and nested forms easily.
- **Example**: `FormBuilder` used to construct forms with controls and validations.

---

### **11. Template Method Pattern**
- **Description**: Angular directives and lifecycle hooks (e.g., `ngOnInit`, `ngOnDestroy`) follow this pattern.
- **Purpose**: Provides a consistent structure for lifecycle-related tasks.
- **Example**: Implementing `ngOnInit` to initialize data for components.

---

### **12. State Pattern**
- **Description**: Angular’s state management libraries like NgRx, Akita, or BehaviorSubject follow this pattern.
- **Purpose**: Manages application state in a predictable manner.
- **Example**: Using NgRx to manage UI and data states.

---

### **13. Composite Pattern**
- **Description**: Angular allows nesting components within each other to build a complex UI.
- **Purpose**: Simplifies building complex hierarchies of components.
- **Example**: A `DashboardComponent` containing multiple child components like `ChartComponent` and `TableComponent`.

---

### **14. Command Pattern**
- **Description**: Encapsulates requests as objects in Angular for reusable and undoable operations.
- **Purpose**: Decouples commands from execution logic.
- **Example**: Angular's `Router` using route definitions to handle navigation commands.

---

### **15. Decorator Pattern**
- **Description**: Angular heavily uses decorators (`@Component`, `@Injectable`, `@Directive`) to add metadata.
- **Purpose**: Provides additional behavior or metadata to classes.
- **Example**: `@Component` decorator adding component-specific configuration.

---

### Summary Table:

| **Pattern**            | **Purpose**                                         | **Angular Example**                            |
|-------------------------|-----------------------------------------------------|-----------------------------------------------|
| Dependency Injection    | Manage object creation and dependency resolution.   | Services like `HttpClient` injected into components. |
| Observer                | Reactive programming with asynchronous streams.     | RxJS Observables for API calls.               |
| Singleton               | Shared instance for app-wide data.                 | `AuthService` provided at the root level.     |
| Component-Based         | Modularize UI and encapsulate logic.               | Components like `HeaderComponent`.            |
| Module Pattern          | Group and encapsulate functionality.               | Angular modules like `AppModule`.             |
| Factory                 | Dynamic creation of objects.                       | `FactoryProvider` for conditional services.   |
| Proxy                   | Modify requests or responses.                      | HTTP interceptors for API calls.              |
| Mediator                | Decoupled communication.                           | `EventEmitter` for component interaction.     |
| Strategy                | Flexible behavior selection.                       | Reactive and Template-Driven Forms.           |
| Builder                 | Step-by-step construction.                         | `FormBuilder` for forms.                      |

Angular's design patterns make it a robust framework that is easy to scale, maintain, and extend. These patterns provide solutions to common software challenges and improve the overall architecture of web applications.

-----------

# **Factory Design Pattern**

The **Factory Design Pattern** is like a factory in the real world that produces different products based on your request. Instead of creating objects (products) yourself, you ask the factory to do it for you. This approach hides the complex creation logic and gives you the correct object based on your input.

---

### **Simple Explanation**
Imagine you’re at a bakery.  
- You say: "I want a cake" (your input).  
- The baker knows how to make a cake (complex logic).  
- You get a cake, ready to eat, without knowing how it was made.  

Similarly, the **Factory Pattern** works like the baker. You provide a request, and it gives you the appropriate object without exposing the details of its creation.

---

### **Key Points**
1. **What it does**: A single place (factory) is responsible for creating objects, so you don’t need to worry about the details.
2. **Why it’s useful**: Simplifies object creation, especially if it’s complex or varies based on input.
3. **Example in code**: A function or class that decides which object to return based on some condition.

---

### **Example: Simple Pizza Factory**

#### Without Factory
```typescript
class VegPizza {
  prepare() {
    console.log('Preparing Veg Pizza');
  }
}

class NonVegPizza {
  prepare() {
    console.log('Preparing Non-Veg Pizza');
  }
}

// Client code creates objects directly
const pizza1 = new VegPizza();
pizza1.prepare();

const pizza2 = new NonVegPizza();
pizza2.prepare();
```

#### With Factory
```typescript
class PizzaFactory {
  static createPizza(type: string) {
    if (type === 'Veg') {
      return new VegPizza();
    } else if (type === 'NonVeg') {
      return new NonVegPizza();
    } else {
      throw new Error('Invalid pizza type');
    }
  }
}

// Client code uses the factory
const pizza1 = PizzaFactory.createPizza('Veg');
pizza1.prepare(); // Output: Preparing Veg Pizza

const pizza2 = PizzaFactory.createPizza('NonVeg');
pizza2.prepare(); // Output: Preparing Non-Veg Pizza
```

---

### **What Happened Here?**
- **Without Factory**: You directly create the objects (`VegPizza`, `NonVegPizza`) yourself. The logic of object creation is in the client code.
- **With Factory**: The creation logic moves to a central place (`PizzaFactory`). The client code simply uses the factory and doesn’t care how the objects are created.

---

### **Factory Design Pattern: Technical Explanation**

The **Factory Design Pattern** is a **creational design pattern** used in object-oriented programming. Its purpose is to provide a way to encapsulate the instantiation logic of objects, allowing the client to request objects without specifying the exact class to instantiate.

---

### Key Characteristics of the Factory Pattern:
1. **Centralized Object Creation**: A factory (method or class) is responsible for creating and returning objects.
2. **Encapsulation of Logic**: The object creation logic is abstracted from the client.
3. **Polymorphism**: Factory uses inheritance or interfaces to determine which object to create at runtime.
4. **Single Responsibility Principle**: The factory is solely responsible for object creation, keeping client code clean and focused on its tasks.
5. **Open/Closed Principle**: New object types can be added by extending the factory without modifying the existing code.

---

# Explain **proxy** ?

The term **proxy** generally refers to something that acts as a substitute or intermediary for another. In various contexts, it holds slightly different meanings:

---

### Contextual Meanings:
1. **Software Development**:
   - In design patterns, a **proxy** object provides a way to access the real object while adding additional behavior (e.g., lazy initialization, access control, logging).
   - For example:
     - **Virtual Proxy**: Delays the creation of an expensive object until it's needed.
     - **Protection Proxy**: Adds security checks before allowing access to the real object.

2. **Network/Internet**:
   - A **proxy server** is an intermediary server between a client (like a web browser) and the internet. It can filter requests, improve performance with caching, or provide anonymity.
   - Example: A corporate proxy server that blocks access to unauthorized websites.

3. **Legal/Business**:
   - A **proxy** is a person authorized to act on behalf of someone else. For example, in meetings or voting, a proxy represents someone who cannot be present.

---------