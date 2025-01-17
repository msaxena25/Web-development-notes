# **Angular library**

To make your common components (like `login`, `contact`, `registration`) and services (like `MessageService`, `AuthService`) reusable in another Angular app, you can create an **Angular library**. Once you create a library, you can export these components and services, and then use them in any other Angular application.

Here’s a step-by-step guide on how to achieve this:

### 1. **Create an Angular Library**

1. **Generate the Library:**
   First, create a new Angular library using the Angular CLI.
   ```bash
   ng generate library common-library
   ```

2. **Add Components, Services, and Modules:**
   After creating the library, navigate to the `projects/common-library/src/lib` folder, where you can add your components, services, and modules.

   - Move your `login`, `contact`, `registration` components, and `MessageService`, `AuthService` to this folder. For example:
     - `src/lib/login/login.component.ts`
     - `src/lib/contact/contact.component.ts`
     - `src/lib/registration/registration.component.ts`
     - `src/lib/services/message.service.ts`
     - `src/lib/services/auth.service.ts`

3. **Export Components and Services:**
   In the `common-library.module.ts` (or a new module you create in the library), export the components and services you want to share. For example:

   ```typescript
   import { NgModule } from '@angular/core';
   import { CommonModule } from '@angular/common';
   import { LoginComponent } from './login/login.component';
   import { ContactComponent } from './contact/contact.component';
   import { RegistrationComponent } from './registration/registration.component';
   import { MessageService } from './services/message.service';
   import { AuthService } from './services/auth.service';

   @NgModule({
     imports: [CommonModule],
     declarations: [LoginComponent, ContactComponent, RegistrationComponent],
     providers: [MessageService, AuthService],
     exports: [LoginComponent, ContactComponent, RegistrationComponent]
   })
   export class CommonLibraryModule {}
   ```

### 2. **Build the Library**

1. **Build the Library:**
   After setting up your library, you need to build it to make it available for reuse. Run the following command:
   ```bash
   ng build common-library
   ```

2. **Publish the Library (Optional):**
   If you want to use this library across different Angular projects, you can either:
   - **Publish it to npm** if you want to share it publicly or privately via a registry.
   - Or, if you are only using it within your workspace or organization, you can use it locally.

   To publish it to npm (if desired):
   ```bash
   npm login
   npm publish dist/common-library
   ```

### 3. **Use the Library in Another Angular App**

1. **Install the Library (if published):**
   If you published the library to npm, you can install it in the consuming application like any other npm package:
   ```bash
   npm install your-library-name
   ```

   If you want to use it locally without publishing it to npm, you can add it as a dependency by linking or copying the built files from the `dist/` folder of the library.

2. **Import the Library Module:**
   In the consuming Angular application, import the `CommonLibraryModule` in the `app.module.ts` or any other module where you want to use the components/services.

   ```typescript
   import { CommonLibraryModule } from 'common-library';  // Adjust the import based on how you installed the library

   @NgModule({
     declarations: [],
     imports: [
       CommonLibraryModule,  // Add the library module here
     ],
     providers: [],
     bootstrap: [AppComponent]
   })
   export class AppModule {}
   ```

3. **Use the Components and Services:**
   After importing the `CommonLibraryModule`, you can directly use your `LoginComponent`, `ContactComponent`, and `RegistrationComponent` in the consuming app’s templates. Similarly, you can inject `MessageService` and `AuthService` into any component or service.

   **For example, in a component template:**

   ```html
   <app-login></app-login>  <!-- Using the login component -->
   ```

   **In a component's TypeScript file (using services):**

   ```typescript
   import { Component } from '@angular/core';
   import { MessageService } from 'common-library';  // Importing the service

   @Component({
     selector: 'app-root',
     templateUrl: './app.component.html',
     styleUrls: ['./app.component.css']
   })
   export class AppComponent {
     constructor(private messageService: MessageService) {}

     sendMessage() {
       this.messageService.sendMessage('Hello from the other app!');
     }
   }
   ```

### 4. **Versioning and Updates:**

Whenever you need to make changes to the library (e.g., add new components or services), update the version in the `package.json` of the library, build it, and either re-publish it to npm (if necessary) or link it again for local use in your other apps.

### Key Points:
- **Modularization:** Libraries help you modularize and reuse common components and services across multiple Angular applications.
- **NPM Publishing:** If you want to share your library with other teams or apps, consider publishing it to npm.
- **Local Development:** You can use local libraries via `npm link` or by directly copying the build artifacts.

This approach will help you create a **modular Angular app** that can easily be shared across different projects or teams.

-------

## Cannot use angular application as a library

No, the process of creating an **Angular library** using `ng generate library` is different from using a full Angular **application** directly as a library. Angular **applications** and **libraries** have different structures and purposes. 

While an **Angular library** is specifically designed to be reused across multiple applications, an **Angular application** is a self-contained project that is meant to be deployed as a standalone app.

### Key Differences Between Angular Application and Angular Library:

1. **Angular Library:**
   - Libraries are designed to be **reusable**. They typically contain only code that is meant to be used by other applications, like components, directives, pipes, and services.
   - Libraries are **bundled** for reuse with a specific format (`ng-package`), and you can export all or a subset of the module to make it available for other apps.
   - The library does not have a standalone entry point (like `main.ts` in an app).

2. **Angular Application:**
   - Applications are designed to be **standalone** and can have their own entry points (e.g., `main.ts`, routing, etc.).
   - Applications include everything needed to run the app, including bootstrapping, main modules, routing, etc.
   - You can **reuse individual components and services** from the app by refactoring them into an Angular library.

---------

# **Sharing data**

To pass data between an Angular app and an Angular library, you can use several mechanisms based on the use case. Libraries in Angular typically export components, services, directives, or modules that are consumed by an Angular application. Here’s how you can share data effectively:

---

### 1. **Input and Output Properties (Parent to Child Communication)**  
   If the library provides reusable **components**, you can pass data from the app to the library component using Angular’s `@Input` and `@Output`.

   #### Example:
   **Library Component (in the library):**
   ```typescript
   import { Component, Input, Output, EventEmitter } from '@angular/core';

   @Component({
     selector: 'lib-user-card',
     template: `
       <div>
         <p>User Name: {{ name }}</p>
         <button (click)="notify()">Notify Parent</button>
       </div>
     `,
   })
   export class UserCardComponent {
     @Input() name!: string; // Data received from the app
     @Output() notifyParent = new EventEmitter<string>();

     notify() {
       this.notifyParent.emit(`Hello from ${this.name}`);
     }
   }
   ```

   **App Component (in the app):**
   ```typescript
   <lib-user-card [name]="'John Doe'" (notifyParent)="onNotify($event)"></lib-user-card>
   ```

   **App Component TypeScript:**
   ```typescript
   onNotify(message: string) {
     console.log(message); // Logs: "Hello from John Doe"
   }
   ```

---

### 2. **Services (Application-Wide Communication)**  
   Services can act as a shared data source between the library and the app. By providing a service in the library and injecting it in both the app and library components, you can share data.

---

### 3. **Custom Events via EventDispatcher**  
   You can use the browser’s native `EventDispatcher` to emit custom events from the library and listen to them in the app.

   #### Example:
   **Library Code:**
   ```typescript
   const customEvent = new CustomEvent('library-event', { detail: { data: 'Hello' } });
   window.dispatchEvent(customEvent);
   ```

   **App Code:**
   ```typescript
   window.addEventListener('library-event', (event: any) => {
     console.log(event.detail.data); // Logs: "Hello"
   });
   ```

---

### 4. **Using Observables (Reactive Data Sharing)**  
   The library can provide a service with an `Observable` or `Subject` to share reactive data streams with the app.

   #### Example:
   **Library Service:**
   ```typescript
   private dataSubject = new BehaviorSubject<number>(0);
   data$ = this.dataSubject.asObservable();

   increment() {
     this.dataSubject.next(this.dataSubject.value + 1);
   }
   ```

   **App Component:**
   ```typescript
   this.libraryService.data$.subscribe((value) => {
     console.log('Value from Library:', value);
   });
   ```

---

## **`window.dispatchEvent` is not ideal way to share data in angular apps**

Using `window.dispatchEvent` to share data in Angular applications is not generally considered safe or recommended for several reasons. While it can be useful for certain scenarios, such as communication between completely independent applications or third-party libraries, it is not the ideal mechanism for data sharing in Angular applications that are already built around dependency injection and RxJS.

---

### **Why `window.dispatchEvent` is not ideal:**

1. **Global Scope**: 
   - Events dispatched on the `window` object are globally accessible, which can lead to unintended interference between different parts of the application or other scripts on the page.

2. **Debugging Complexity**:
   - Debugging issues with custom events can be challenging as they are not part of Angular's lifecycle or change detection mechanism.

3. **Lack of Type Safety**:
   - Events dispatched via `window.dispatchEvent` do not have built-in TypeScript support for type checking, increasing the likelihood of runtime errors.

4. **Bypassing Angular's Change Detection**:
   - Angular's change detection will not automatically pick up changes triggered by `window.dispatchEvent`. You may need to use `NgZone` to manually trigger change detection, which adds extra complexity.

---

### **When `window.dispatchEvent` might be appropriate:**

1. **Cross-Domain Communication**:
   - When you need to share data between two independent Angular or non-Angular applications running on the same page but built separately.

2. **Integration with Non-Angular Systems**:
   - If you're working with third-party libraries or frameworks that rely on custom events dispatched via `window`.

3. **Handling Browser-Specific Events**:
   - If your application needs to respond to or trigger browser-specific events that are not easily handled through Angular mechanisms.

---

### **If you must use `window.dispatchEvent`:**

- **Wrap it in Angular's Mechanism**:
   Use `NgZone` to ensure change detection is triggered:
   ```typescript
   constructor(private zone: NgZone) {
     window.addEventListener('custom-event', (event: any) => {
       this.zone.run(() => {
         console.log(event.detail);
       });
     });
   }
   ```

- **Ensure Namespacing**:
   Use unique event names to avoid collisions with other scripts:
   ```typescript
   const event = new CustomEvent('my-app.custom-event', { detail: { data: 'Hello' } });
   window.dispatchEvent(event);
   ```

---
