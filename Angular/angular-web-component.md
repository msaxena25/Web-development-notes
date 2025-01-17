# **Convert angular components into web components**

Let's create an Angular app with a **Contact Us** page and a **Registration** page, then convert the app into **Web Components** (using **Angular Elements**), and finally use it in another Angular app, here is the step-by-step process.

### **Step 1: Create the Angular Application**

First, create a new Angular app. Let's call it `micro-app` (This will contain the Contact Us and Registration pages).

```bash
ng new micro-app
cd micro-app
```

Now create two components — **ContactUsComponent** and **RegistrationComponent**.

```bash
ng generate component contact-us
ng generate component registration
```

### **Step 2: Set up the Angular App with Web Components (Angular Elements)**

In order to use Angular components as Web Components, we need to use **Angular Elements**. Let's install the necessary packages and set up the `AppModule` to export these components as Web Components.

1. **Install Angular Elements and Polyfills:**

```bash
ng add @angular/elements
npm install @webcomponents/custom-elements
```

2. **Modify `app.module.ts` to define Web Components:**

Update the `app.module.ts` to use `createCustomElement` and expose both `ContactUsComponent` and `RegistrationComponent` as custom elements.

```typescript
import { NgModule, Injector } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { ContactUsComponent } from './contact-us/contact-us.component';
import { RegistrationComponent } from './registration/registration.component';
import { createCustomElement } from '@angular/elements';

@NgModule({
  declarations: [
    AppComponent,
    ContactUsComponent,
    RegistrationComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [],
  entryComponents: [ContactUsComponent, RegistrationComponent]  // Define as entry components
})
export class AppModule {
  constructor(private injector: Injector) {}

  ngDoBootstrap() {
    // Convert components to Web Components
    const contactUsElement = createCustomElement(ContactUsComponent, { injector: this.injector });
    const registrationElement = createCustomElement(RegistrationComponent, { injector: this.injector });

    // Register them as custom elements
    customElements.define('contact-us', contactUsElement);
    customElements.define('registration-form', registrationElement);
  }
}
```

In the above code:
- We used `createCustomElement` to convert the `ContactUsComponent` and `RegistrationComponent` into Web Components.
- `customElements.define` is used to register the components as custom elements, i.e., `<contact-us></contact-us>` and `<registration-form></registration-form>`.

3. **Update the Components:**

Ensure both `ContactUsComponent` and `RegistrationComponent` have some basic HTML content.

### **Step 3: Build the Micro App**

Now build the app for production so that we can use the web components in another Angular app.

```bash
ng build --prod
```

This will generate the production-ready files in the `dist` folder.

## **Step 4: Use the Web Components in Another Angular App**

Now, let’s create a new **MainApp** (we will use the `micro-app` as a web component here).

1. **Create the MainApp:**

```bash
ng new main-app
cd main-app
```

2. **Copy the `dist` folder from `micro-app` to the `assets` folder of `main-app`:**

After building the `micro-app`, copy the content from the `dist/micro-app` directory and paste it into the `src/assets` folder of the `main-app`.

```bash
cp -r path/to/micro-app/dist/micro-app/* src/assets/micro-app/
```

3. **Include the Web Component in `index.html` of MainApp:**

In the **MainApp**, open the `src/index.html` file and include the script for the `micro-app` (which we just built) in the `<head>` section.

```html
<!-- src/index.html -->
<head>
  ...
  <script src="assets/micro-app/runtime.js"></script>
  <script src="assets/micro-app/polyfills.js"></script>
  <script src="assets/micro-app/main.js"></script>
</head>
```

4. **Use the Web Components in MainApp’s Template:**

Now, in the `MainApp` component template, you can use the web components like this:

```html
<!-- src/app/app.component.html -->
<h1>Welcome to MainApp</h1>

<h2>Micro App Components</h2>

<contact-us></contact-us>
<registration-form></registration-form>
```

5. **Run the MainApp:**

Finally, run the `main-app` to see the **Contact Us** and **Registration** components embedded from `micro-app`.

```bash
ng serve
```

### **Advantages of this Approach:**

- The micro-app is independent and can be developed, built, and maintained separately from the main app.
- Angular Elements (Web Components) allow reusability across different applications (Angular or non-Angular).
- It’s easier to integrate third-party applications or legacy systems with Angular by using web components.

-----------

# How Routing works of micro app into main-app

No, routing in the **micro-app** will **not automatically** work when the micro app is embedded as a Web Component inside the main app. Here's why:

### **Why Routing Doesn't Work in Micro App when Embedded as a Web Component:**

Once you build the **micro-app** into a Web Component, the routing information (`RouterModule` and associated router outlets) is not included. The Web Component is just a piece of HTML with encapsulated Angular functionality. It doesn't retain any Angular-specific routing behavior or contexts when used outside its original Angular context.

### **How to Handle Routing in a Web Component:**

If you want routing to work inside the **micro-app** after it’s embedded in the **main-app**, you have a few options:

1. **Implement Custom Events in Micro App and Handle in Main App:**
   - You can emit a **custom event** in the micro-app (Web Component) when the form is submitted, and the **main app** can listen to this event and handle the routing action accordingly.

   Example:
   - In **micro-app's registration component**, emit a custom event when the form is submitted:

   ```typescript
   // registration.component.ts in micro-app
   @Output() registrationSubmitted = new EventEmitter<void>();

   onSubmit() {
     this.registrationSubmitted.emit();  // Emit the event
   }
   ```

   - In the **main-app**, listen for this custom event and trigger the route change:

   ```html
   <!-- In the main app's template -->
   <registration-form (registrationSubmitted)="onRegistrationSubmit()"></registration-form>
   ```

   ```typescript
   // main-app.component.ts
   onRegistrationSubmit() {
     this.router.navigate(['/acknowledge']);
   }
   ```

2. **Consider Handling Internal Routing inside the Micro App (if you need internal routing):**
   - If internal routing is necessary within the micro-app itself, you could embed the micro-app in an iframe. The micro-app would then retain its own internal Angular routing within its context. However, using iframes comes with its own set of complexities, such as cross-origin issues, and it's generally not the most modern or preferred approach for Angular apps.

------
