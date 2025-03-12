To make the code more organized, maintainable, and scalable in an Angular application, here are some best practices and ideas you can apply:

### 1. **Use Feature Modules**
   - **Why**: Feature modules help in keeping the application modular and organized. Each feature (like Home, Product, Checkout) should have its own module.
   - **How**: Create a module for each major feature and import only the required components, services, and routing logic.
   
   Example:
   ```typescript
   // home.module.ts
   import { NgModule } from '@angular/core';
   import { CommonModule } from '@angular/common';
   import { HomeComponent } from './home.component';
   
   @NgModule({
     declarations: [HomeComponent],
     imports: [CommonModule],
     exports: [HomeComponent],
   })
   export class HomeModule {}
   ```

### 2. **Smart and Dumb Component Pattern**
   - **Why**: This pattern separates the logic of your application (smart components) from the presentation (dumb components). Smart components are responsible for fetching data, handling business logic, and passing data to dumb components, which are only responsible for rendering the UI.
   - **How**: Split components into containers (smart) and presentational (dumb).
   
   Example:
   ```typescript
   // smart.component.ts
   @Component({
     selector: 'app-smart',
     template: `<app-dumb [data]="data"></app-dumb>`,
   })
   export class SmartComponent implements OnInit {
     data: any;

     constructor(private dataService: DataService) {}

     ngOnInit() {
       this.dataService.getData().subscribe(response => {
         this.data = response;
       });
     }
   }
   
   // dumb.component.ts
   @Component({
     selector: 'app-dumb',
     template: `<div>{{data}}</div>`,
   })
   export class DumbComponent {
     @Input() data: any;
   }
   ```

### 3. **Use a Shared Module for Reusable Components/Services**
   - **Why**: A shared module is used to declare and export components, directives, and pipes that are reused across multiple feature modules.
   - **How**: Put reusable components, services, and pipes into a `SharedModule` and import that module where needed.

   Example:
   ```typescript
   // shared.module.ts
   import { NgModule } from '@angular/core';
   import { CommonModule } from '@angular/common';
   import { CustomPipe } from './pipes/custom.pipe';
   import { CustomComponent } from './components/custom.component';
   
   @NgModule({
     declarations: [CustomPipe, CustomComponent],
     imports: [CommonModule],
     exports: [CustomPipe, CustomComponent],
   })
   export class SharedModule {}
   ```

### 4. **Use Angular Services for Reusable Logic**
   - **Why**: Services are the backbone of Angular applications and can help to separate business logic and API calls from components.
   - **How**: Create services to handle all data fetching, business logic, or other reusable tasks and inject them into your components.

   Example:
   ```typescript
   // product.service.ts
   @Injectable({
     providedIn: 'root',
   })
   export class ProductService {
     constructor(private http: HttpClient) {}

     getProducts(): Observable<Product[]> {
       return this.http.get<Product[]>('api/products');
     }
   }
   ```

### 5. **Leverage Lazy Loading for Large Applications**
   - **Why**: Lazy loading helps in improving the performance by loading the necessary feature modules only when required, not on the initial load.
   - **How**: Use Angular's built-in lazy loading feature for feature modules to improve load times.

   Example:
   ```typescript
   // app-routing.module.ts
   const routes: Routes = [
     { path: 'home', loadChildren: () => import('./home/home.module').then(m => m.HomeModule) },
     { path: 'product', loadChildren: () => import('./product/product.module').then(m => m.ProductModule) },
   ];
   ```

### 6. **Use Enum for Constant Values**
   - **Why**: Enums provide a better way to organize constant values and prevent magic numbers/strings in the code.
   - **How**: Define enums to hold constant values like roles, status, actions, etc.

   Example:
   ```typescript
   // app.constants.ts
   export enum UserRole {
     Admin = 'ADMIN',
     User = 'USER',
     Guest = 'GUEST',
   }

   export enum OrderStatus {
     Pending = 'PENDING',
     Shipped = 'SHIPPED',
     Delivered = 'DELIVERED',
   }
   ```

### 7. **Use a Store (NgRx or Akita) for State Management**
   - **Why**: State management libraries like NgRx or Akita help in managing state in large applications by providing a central store and reducing the complexity of passing data between components.
   - **How**: Implement a state management solution for handling state in your application. This is useful for handling user data, shopping carts, etc., at a global level.

   Example (with NgRx):
   ```typescript
   // product.actions.ts
   import { createAction, props } from '@ngrx/store';
   export const loadProducts = createAction('[Product] Load Products');
   export const loadProductsSuccess = createAction('[Product] Load Products Success', props<{ products: Product[] }>());
   ```

### 8. **Use RxJS for Async Data Handling**
   - **Why**: Angular leverages RxJS to manage asynchronous operations like HTTP requests, user input events, etc. It makes data handling more predictable and reactive.
   - **How**: Use RxJS operators like `map`, `switchMap`, `mergeMap`, `catchError`, etc., to compose complex async workflows and transform data.
   
   Example:
   ```typescript
   // product.component.ts
   this.productService.getProducts().pipe(
     catchError(error => {
       this.error = 'Failed to load products';
       return of([]);
     })
   ).subscribe(products => {
     this.products = products;
   });
   ```

### 9. **Use Guards to Control Navigation**
   - **Why**: Guards help in protecting routes from unauthorized access, checking preconditions before route navigation, or preventing navigation under certain conditions.
   - **How**: Use route guards to manage access control and navigation flow in your application.

   Example:
   ```typescript
   // auth.guard.ts
   @Injectable({
     providedIn: 'root',
   })
   export class AuthGuard implements CanActivate {
     constructor(private authService: AuthService) {}

     canActivate(): boolean {
       return this.authService.isAuthenticated();
     }
   }
   ```

### 10. **Organize Your Folder Structure Based on Feature**
   - **Why**: Organizing your files by feature or domain (like `product`, `user`, `cart`) makes the project easier to scale and maintain.
   - **How**: Keep all related components, services, models, and other files within their respective feature folders.

   Example:
   ```bash
   /src
     /app
       /auth
         auth.component.ts
         auth.service.ts
         auth.module.ts
       /product
         product.component.ts
         product.service.ts
         product.module.ts
   ```

### 11. **Use Dependency Injection for Better Testability**
   - **Why**: Dependency Injection (DI) helps make your components, services, and other parts of the application easier to test and manage by injecting the dependencies instead of creating them within the class.
   - **How**: Use Angular's DI system to inject services, making your code more modular and testable.

   Example:
   ```typescript
   constructor(private authService: AuthService) {}
   ```

-----

## **Use of Interceptors and Logger service**

Yes, absolutely! Using **interceptors** in Angular is a great way to handle common API logic (like adding authorization headers, handling errors, or logging requests and responses) across your entire application.

Here’s how we can implement **interceptors** and **logger services** to handle these concerns:

### 1. **API Interceptors**:
   Interceptors allow us to intercept HTTP requests and responses, modify them, or handle any global logic like adding authorization tokens, error handling, etc. 

   - **Authorization Headers**: Adding tokens to every outgoing API request.
   - **Logging Requests/Responses**: Logging request details like the URL, method, or response status code.
   - **Error Handling**: Intercepting API errors globally to show user-friendly messages.

#### Example: HTTP Interceptor for Authorization and Error Handling
```typescript
// auth.interceptor.ts
import { Injectable } from '@angular/core';
import { HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { AuthService } from './auth.service';
import { LoggerService } from './logger.service';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {

  constructor(private authService: AuthService, private loggerService: LoggerService) {}

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = this.authService.getToken();
    
    // Clone the request and add the Authorization header if a token exists
    const clonedRequest = req.clone({
      setHeaders: {
        Authorization: token ? `Bearer ${token}` : ''
      }
    });

    // Log the request details
    this.loggerService.logRequest(clonedRequest);

    return next.handle(clonedRequest).pipe(
      catchError((error: HttpErrorResponse) => {
        // Handle global errors
        if (error.status === 401) {
          this.authService.logout(); // Handle logout logic
        }
        
        // Log the error response
        this.loggerService.logError(error);

        // Return a user-friendly error message
        return throwError('Something went wrong! Please try again later.');
      })
    );
  }
}
```

### 2. **Logger Service**:
   A **Logger Service** is useful to track API calls, user interactions, or errors, which is important for debugging, tracking usage, and understanding application behavior.

#### Example: Logger Service for Request and Error Logging
```typescript
// logger.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class LoggerService {

  constructor() {}

  // Logs request details (e.g., URL, method)
  logRequest(request: any): void {
    console.log('Request:', request.method, request.url);
    if (request.body) {
      console.log('Request Body:', request.body);
    }
  }

  // Logs error details
  logError(error: any): void {
    console.error('Error:', error);
  }

  // Logs successful response details
  logResponse(response: any): void {
    console.log('Response:', response);
  }
}
```

### 3. **Registering Interceptors in App Module**:
   Angular interceptors are registered as providers in the Angular module (`AppModule`). You can add multiple interceptors for different use cases.

#### Example: Registering the `AuthInterceptor` in the `AppModule`
```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { AppComponent } from './app.component';
import { AuthInterceptor } from './auth.interceptor';
import { LoggerService } from './logger.service';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true // Allow multiple interceptors
    },
    LoggerService // Registering Logger Service
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```
----

## **Common components**, **directives**, and **pipes**

Yes, absolutely! Having **common components**, **directives**, and **pipes** in your Angular project is a best practice to promote **code reuse** and maintainability. These shared features can be placed in a dedicated **Shared Module** or a **Core Module** depending on the use case. Here's how you can structure and utilize them:

---

### **1. Common Components**
   Common components are those that are reused across the application, such as headers, footers, breadcrumbs, loaders, modals, or notification components.

#### Example:
- **`LoaderComponent`**: A spinner or loading indicator used across pages.
- **`ModalComponent`**: A reusable modal dialog.

**Folder Structure**:
```plaintext
src/app/shared/components/
  - loader/
      loader.component.ts
      loader.component.html
      loader.component.css
  - modal/
      modal.component.ts
      modal.component.html
      modal.component.css
```

**Shared Module Example**:
```typescript
@NgModule({
  declarations: [LoaderComponent, ModalComponent],
  exports: [LoaderComponent, ModalComponent],
  imports: [CommonModule]
})
export class SharedModule {}
```

**Usage**:
```html
<!-- Reusing the loader -->
<app-loader></app-loader>
```

---

### **2. Common Directives**
   Common directives allow you to encapsulate custom DOM behavior that can be reused across the application.

#### Example:
- **`HighlightDirective`**: Highlights text on hover.
- **`DebounceClickDirective`**: Debounces multiple button clicks.

**Folder Structure**:
```plaintext
src/app/shared/directives/
  - highlight.directive.ts
  - debounce-click.directive.ts
```

**HighlightDirective Example**:
```typescript
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(private el: ElementRef) {}

  @HostListener('mouseenter') onMouseEnter() {
    this.el.nativeElement.style.backgroundColor = 'yellow';
  }

  @HostListener('mouseleave') onMouseLeave() {
    this.el.nativeElement.style.backgroundColor = null;
  }
}
```

**Usage**:
```html
<p appHighlight>Hover over me to see the highlight effect!</p>
```

---

### **3. Common Pipes**
   Common pipes transform data in a reusable way, like formatting dates, currencies, or filtering data.

#### Example:
- **`TruncatePipe`**: Truncates long text.
- **`CurrencyPipe`**: Formats a number as currency with custom logic.

**Folder Structure**:
```plaintext
src/app/shared/pipes/
  - truncate.pipe.ts
  - currency.pipe.ts
```

**TruncatePipe Example**:
```typescript
@Pipe({
  name: 'truncate'
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 20): string {
    return value.length > limit ? value.substring(0, limit) + '...' : value;
  }
}
```

**Usage**:
```html
<p>{{ 'This is a long sentence that needs truncation' | truncate:10 }}</p>
<!-- Output: This is a... -->
```

---

### **4. Shared Module to Group All Shared Resources**
To make common components, directives, and pipes reusable, group them in a **SharedModule**.

**SharedModule Example**:
```typescript
@NgModule({
  declarations: [
    LoaderComponent,
    ModalComponent,
    HighlightDirective,
    TruncatePipe
  ],
  exports: [
    LoaderComponent,
    ModalComponent,
    HighlightDirective,
    TruncatePipe
  ],
  imports: [
    CommonModule
  ]
})
export class SharedModule {}
```

---

### **5. Core Module for Singleton Services**
While shared components/directives/pipes go into the `SharedModule`, services that should be singletons (like `AuthService`, `LoggerService`, etc.) belong in the **CoreModule**.

**Folder Structure**:
```plaintext
src/app/core/
  - services/
      - auth.service.ts
      - logger.service.ts
  - guards/
      - auth.guard.ts
  - interceptors/
      - auth.interceptor.ts
```
-----

## **How many Ways to Get service**

In Angular, there are multiple ways to get a service instance. Below are the different methods:

---

### **1. Using Constructor Injection (Recommended)**
This is the most common and recommended way using **dependency injection (DI)**.

```typescript
 constructor(private myService: MyService) {
    this.data = this.myService.getData();
  }
```
🔹 **Best Practice:** Use this method for singleton services.

---

### **2. Using Injector (`inject()` - Angular 14+)**
Instead of constructor injection, you can use the `inject()` function (Angular 14+).

```typescript
  import { inject } from '@angular/core';

export class ExampleComponent {
  myService = inject(MyService);
  data = this.myService.getData();
}
```
🔹 **Best Practice:** Useful in **standalone components or directives**.

---

### **3. Using Injector (`Injector` Class)**
If you cannot use constructor injection (e.g., inside a non-Angular class), use the **Injector** class.

```typescript
import { Injector } from '@angular/core';

export class ExampleClass {
  private myService: MyService;

  constructor(private injector: Injector) {
    this.myService = this.injector.get(MyService);
  }

  getData() {
    return this.myService.getData();
  }
}
```

🔹 **Use Case:** When manually creating an instance of a service.
---

### **5. Using `@Optional()` for Conditional Injection**
This is useful if the service may or may not be provided.

```typescript
constructor(@Optional() private myService: MyService) {
  if (this.myService) {
    console.log('Service Available');
  } else {
    console.log('Service Not Available');
  }
}
```

🔹 **Use Case:** When a service is optional.

---

## **Best way to handle relative paths - Path mapping from tsconfig.json**

Angular has a way to avoid long relative import paths by using **TypeScript path mapping** in your `tsconfig.json` file. This allows you to create custom aliases for your directories, making your imports cleaner and easier to maintain.

### Steps to Set Up Path Mapping in Angular

1. **Update `tsconfig.json`**:
   
   You need to modify the `compilerOptions` section of your `tsconfig.json` file to define paths.

   Here's how you can set it up:

   ```json
   {
     "compilerOptions": {
       "baseUrl": "./",
       "paths": {
         "@core/*": ["src/app/core/*"],
         "@shared/*": ["src/app/shared/*"],
         "@services/*": ["src/app/core/services/*"],
         "@models/*": ["src/app/models/*"]
       }
     }
   }
   ```

2. **Use the Aliases in Your Imports**:
   
   After setting up the paths, you can use these aliases to simplify your imports.

   **Before Path Mapping:**

   ```typescript
   import { SystemService } from '../../../../core/services/system-services.service';
   ```

   **After Path Mapping:**

   ```typescript
   import { SystemService } from '@services/system-services.service';
   ```

   As you can see, the import path is now much shorter and more readable.

3. **Restart the Development Server**:
   
   After updating the `tsconfig.json` file, make sure to restart the Angular development server (`ng serve`) to reflect the changes.

### Advantages:
- **Cleaner Imports**: It eliminates the need for long relative paths.
- **Easier Refactoring**: If you move files around, you only need to update the paths in `tsconfig.json` instead of changing imports throughout your entire codebase.
- **Better Readability**: Your import statements become more readable and maintainable.
---


## **Use of Barrel Files to maintain imports**

You're referring to consolidating imports for services, enums, and models, so you don't have to import them repeatedly in every component or service.

You can create a central file (called Barrel file) to export all these common services, enums, and models, and then just import that file wherever you need them.

Here’s how you can do it:

### 1. **Create a Centralized Imports File**

You can create a file (e.g., `index.ts`) in a folder, like `src/app/shared/`, and export all your commonly used services, enums, and models from there.

#### `src/app/shared/index.ts` (or any name you prefer)

```typescript
// Services
export * from './services/user.service';
export * from './services/auth.service';
export * from './services/payment.service';

// Enums
export * from './enums/user-roles.enum';
export * from './enums/payment-status.enum';

// Models
export * from './models/user.model';
export * from './models/payment.model';
```

### 2. **Import the Centralized File in Your Components**

Now, in your components (or any other files), instead of importing each individual service, enum, or model, you just import from the `shared/index.ts` file.

#### Example Component: `example.component.ts`

```typescript
import { Component, OnInit } from '@angular/core';
import { UserService, AuthService, PaymentService } from '../shared';  // Import everything from index.ts
import { UserRole } from '../shared';  // Import Enums and Models from index.ts
import { PaymentStatus } from '../shared';
```
-------------

# **TypeScript level - good code practices**

At the **TypeScript level**, organizing and structuring your code efficiently also plays a crucial role in making your Angular application scalable, maintainable, and easy to test. Below are some **best practices** for organizing and structuring your TypeScript code:

### 1. **Use of Classes and Interfaces**

   - **Why**: TypeScript provides static types through classes and interfaces, which helps in maintaining type safety and makes the code predictable. Classes are used to define the structure and behavior of objects, and interfaces are used to define the shape of the data.
   - **How**: 
     - Use **classes** to represent entities that will contain both properties and methods.
     - Use **interfaces** to define the shape of the objects passed around in the application.

   Example:
   ```typescript
   // user.interface.ts
   export interface User {
     id: number;
     name: string;
     email: string;
   }

   // product.class.ts
   export class Product {
     constructor(public name: string, public price: number) {}
   }
   ```

### 2. **Abstract Classes**

   - **Why**: Abstract classes allow us to define common functionality that can be shared by multiple child classes. It also enforces structure on the child classes while providing flexibility.
   - **How**: Create abstract classes when you want to define a common base class but prevent the instantiation of the class itself. This ensures the child classes implement specific methods.

   Example:
   ```typescript
   // base.model.ts
   export abstract class BaseModel {
     constructor(public id: number, public createdAt: Date) {}

     abstract displayDetails(): void;
   }

   // user.model.ts
   export class UserModel extends BaseModel {
     constructor(id: number, createdAt: Date, public name: string) {
       super(id, createdAt);
     }

     displayDetails(): void {
       console.log(`${this.name} - ${this.id}`);
     }
   }
   ```

### 3. **Use Common Interfaces for Shared Data Structures**

   - **Why**: Define common interfaces for data models and responses that will be used across different services or components. This helps in standardizing data structures and allows reusability.
   - **How**: Create a set of common interfaces for shared data structures across the application, like API response models or user data.

   Example:
   ```typescript
   // api-response.interface.ts
   export interface ApiResponse<T> {
     success: boolean;
     data: T;
     error?: string;
   }

   // product.service.ts
   import { ApiResponse } from './api-response.interface';

   export class ProductService {
     getProduct(id: number): Observable<ApiResponse<Product>> {
       return this.http.get<ApiResponse<Product>>(`/api/product/${id}`);
     }
   }
   ```

### 4. **Use Dependency Injection for Services**

   - **Why**: Services in Angular are a perfect candidate to use **dependency injection** (DI). DI helps to provide classes or values (like services) into components, making it easier to manage and test. 
   - **How**: Ensure your services are injected through the constructor, and avoid manually creating instances of services inside components or other classes.

   Example:
   ```typescript
   // auth.service.ts
   @Injectable({
     providedIn: 'root',
   })
   export class AuthService {
     constructor(private http: HttpClient) {}

     login(credentials: { username: string; password: string }): Observable<User> {
       return this.http.post<User>('/api/login', credentials);
     }
   }

   // user.component.ts
   @Component({
     selector: 'app-user',
     templateUrl: './user.component.html',
   })
   export class UserComponent {
     constructor(private authService: AuthService) {}
   }
   ```

### 5. **Use Enums for Constant Values**

   - **Why**: Enums provide a better structure for constant values like statuses, roles, or types. They provide readable names to values that might otherwise be hard to understand.
   - **How**: Use enums for common constant values, such as API statuses, user roles, etc.

   Example:
   ```typescript
   // order-status.enum.ts
   export enum OrderStatus {
     Pending = 'PENDING',
     Shipped = 'SHIPPED',
     Delivered = 'DELIVERED',
   }
   ```

### 6. **Base Service Classes for Common Logic**

   - **Why**: Base service classes can be used to define common logic that can be inherited by other services. This reduces duplication of code and improves maintainability.
   - **How**: Create a base service class that implements common functionalities like making HTTP calls, handling API responses, etc., and then extend it in specific services.

   Example:
   ```typescript
   // base.service.ts
   export abstract class BaseService {
     constructor(private http: HttpClient) {}

     protected get<T>(url: string): Observable<T> {
       return this.http.get<T>(url);
     }

     protected post<T>(url: string, body: any): Observable<T> {
       return this.http.post<T>(url, body);
     }
   }

   // product.service.ts
   export class ProductService extends BaseService {
     getProductById(id: number): Observable<Product> {
       return this.get<Product>(`/api/product/${id}`);
     }
   }
   ```

### 7. **Avoid Magic Strings and Numbers**

   - **Why**: Magic strings or numbers make the code difficult to understand, debug, and maintain. It's a good practice to define them in a central place, like an enum or constant file, to improve readability.
   - **How**: Use enums, constants, or configuration files to store values like status codes, error messages, etc.

   Example:
   ```typescript
   // constants.ts
   export const ERROR_MESSAGES = {
     SERVER_ERROR: 'An error occurred on the server.',
     NOT_FOUND: 'The requested resource could not be found.',
   };

   export const API_ENDPOINTS = {
     LOGIN: '/api/login',
     REGISTER: '/api/register',
   };
   ```

### 8. **Use Type Aliases and Interfaces for Complex Types**

   - **Why**: Use **type aliases** and **interfaces** to define complex data types instead of using `any` or object types. This improves code readability and reduces errors.
   - **How**: Create specific types or interfaces for complex objects.

   Example:
   ```typescript
   // user.types.ts
   export type User = {
     id: string;
     name: string;
     email: string;
     role: 'user' | 'admin';
   };

   // cart.types.ts
   export interface CartItem {
     productId: string;
     quantity: number;
   }
   ```

### 9. **Use Interfaces to Enforce Contracts for Services**

   - **Why**: Use **interfaces** to define the shape of data and ensure that all services are consistent. This enforces contracts for how data should be structured across different parts of the app.
   - **How**: Define services that adhere to the same contract using interfaces. This ensures that services are interchangeable and easy to test.

   Example:
   ```typescript
   // user.service.ts
   export interface IUserService {
     getUser(id: string): Observable<User>;
     saveUser(user: User): Observable<boolean>;
   }

   export class UserService implements IUserService {
     getUser(id: string): Observable<User> {
       return this.http.get<User>(`/api/users/${id}`);
     }

     saveUser(user: User): Observable<boolean> {
       return this.http.post<boolean>('/api/users', user);
     }
   }
   ```

### 10. **Implement Utility Functions and Helper Classes**

   - **Why**: For commonly used functionality, create utility functions or helper classes. This avoids repetitive code and keeps business logic clean.
   - **How**: Group commonly used functions into utility classes or functions, and call them when needed across your application.

   Example:
   ```typescript
   // date-utils.ts
   export class DateUtils {
     static formatDate(date: Date): string {
       return date.toISOString().split('T')[0];
     }

     static addDays(date: Date, days: number): Date {
       const newDate = new Date(date);
       newDate.setDate(newDate.getDate() + days);
       return newDate;
     }
   }
   ```

-----------

## **Difference Between `type` and `interface` in TypeScript**

Both `type` and `interface` are used to define custom types in TypeScript, but they have key differences.

---

### **1. Syntax & Basic Usage**
#### **Using `interface`**
```typescript
interface User {
  name: string;
  age: number;
}
```

#### **Using `type`**
```typescript
type User = {
  name: string;
  age: number;
};
```

---

### **2. Extensibility**
#### **Interfaces Can Be Extended (Merging)**
Interfaces support **declaration merging**, allowing multiple definitions to be merged into one.

```typescript
interface User {
  name: string;
}

interface User {
  age: number;
}

const user: User = { name: "John", age: 30 }; // Merges both
```

#### **Types Cannot Be Merged**
Types do not support merging; redefining a `type` with the same name causes an error.

```typescript
type User = { name: string };
type User = { age: number }; // ❌ Error: Duplicate identifier 'User'
```

However, `type` can extend multiple types using intersection (`&`).

```typescript
type Person = { name: string };
type Age = { age: number };

type User = Person & Age;

const user: User = { name: "John", age: 30 };
```

---

### **3. Extending & Implementing**
#### **Extending an `interface`**
Interfaces can extend other interfaces using `extends`.

```typescript
interface Person {
  name: string;
}

interface Employee extends Person {
  salary: number;
}

const emp: Employee = { name: "John", salary: 50000 };
```

#### **Extending a `type`**
Types use `&` (intersection) to achieve a similar result.

```typescript
type Person = { name: string };
type Employee = Person & { salary: number };

const emp: Employee = { name: "John", salary: 50000 };
```

#### **Classes Can Implement Interfaces but Not Types**
```typescript
interface User {
  name: string;
}

class Person implements User {
  name = "John";
}
```
`type` **cannot be implemented** by a class.

```typescript
type User = { name: string };

class Person implements User { // ❌ Error
  name = "John";
}
```

---

### **4. Use Cases**
| Feature                 | `interface`               | `type`                        |
|-------------------------|-------------------------|------------------------------|
| Object shape definition | ✅ Yes                  | ✅ Yes                        |
| Extending other types   | ✅ `extends` keyword    | ✅ Using `&` (intersection)  |
| Declaration merging     | ✅ Yes                  | ❌ No                         |
| Implementing in class   | ✅ Yes                  | ❌ No                         |
| Can define functions    | ✅ Yes                  | ✅ Yes                        |
| Can define unions       | ❌ No                   | ✅ Yes                        |
| Can define primitives   | ❌ No                   | ✅ Yes (`type Age = number`) |

---

### **5. When to Use `interface` vs `type`**
✅ **Use `interface` when:**
- Defining object structures.
- Extensibility and merging are needed.
- Implementing in a class.

✅ **Use `type` when:**
- Defining primitive types (`string | number`).
- Using union (`type A = B | C`).
- Creating tuple or function types.

---
