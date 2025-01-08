# Web Component and Its use in angular and react application

Here’s a step-by-step guide to creating a **Contact Us Form** as a **Web Component**, and using it in **Angular** and **React** applications.

---

### **Step 1: Create the Web Component**

The following is the code for a `contact-us` web component:

#### **contact-us.js**
```javascript
class ContactUsForm extends HTMLElement {
  constructor() {
    super();
    const shadowRoot = this.attachShadow({ mode: 'open' });
    shadowRoot.innerHTML = `
      <style>
        form {
          display: flex;
          flex-direction: column;
          width: 300px;
          font-family: Arial, sans-serif;
        }
        input, textarea, button {
          margin-bottom: 10px;
          padding: 8px;
          font-size: 16px;
        }
        button {
          background-color: #007BFF;
          color: white;
          border: none;
          cursor: pointer;
        }
        button:hover {
          background-color: #0056b3;
        }
      </style>
      <form id="contact-form">
        <input type="text" id="name" placeholder="Your Name" required />
        <input type="email" id="email" placeholder="Your Email" required />
        <textarea id="message" placeholder="Your Message" rows="4" required></textarea>
        <button type="submit">Submit</button>
      </form>
    `;
    const form = shadowRoot.querySelector('#contact-form');
    form.addEventListener('submit', (event) => {
      event.preventDefault();
      const name = shadowRoot.querySelector('#name').value;
      const email = shadowRoot.querySelector('#email').value;
      const message = shadowRoot.querySelector('#message').value;
      console.log({ name, email, message });
      alert('Form submitted successfully!');
    });
  }
}

// Define the custom element
customElements.define('contact-us', ContactUsForm);
```

- Save this as `contact-us.js`.

---

### **Step 2: Include the Web Component in Angular**

1. **Install Web Components Polyfills (Optional for Older Browsers):**
   ```bash
   npm install @webcomponents/webcomponentsjs --save
   ```

2. **Include the Web Component:**
   - Place the `contact-us.js` file in the `src/assets` folder.

3. **Load the Web Component in `angular.json`:**
   Add the script path to the `scripts` array:
   ```json
   "scripts": [
     "src/assets/contact-us.js"
   ]
   ```

4. **Use the Web Component in an Angular Component:**

   ```html
   <!-- src/app/app.component.html -->
   <h1>Contact Us</h1>
   <contact-us></contact-us>
   ```

   **Note**: Angular supports Web Components out of the box, but if you're using older Angular versions, you might need to modify `tsconfig.json`:
   ```json
   "angularCompilerOptions": {
     "enableIvy": true
   }
   ```

---

### **Step 3: Include the Web Component in React**

1. **Install Web Components Polyfills (Optional for Older Browsers):**
   ```bash
   npm install @webcomponents/webcomponentsjs --save
   ```

2. **Include the Web Component in `index.html`:**
   Add a `<script>` tag for the `contact-us.js` file:
   ```html
   <!-- public/index.html -->
   <script src="contact-us.js"></script>
   ```

3. **Use the Web Component in a React Component:**

   ```jsx
   // src/App.js
   import React from 'react';

   function App() {
     return (
       <div>
         <h1>Contact Us</h1>
         <contact-us></contact-us>
       </div>
     );
   }

   export default App;
   ```

---

# How to pass values to web components and how to emit values from web component.

To add **custom events** and allow passing **values to the web component** from the parent Angular or React application, follow the steps below:

---

### **Step 1: Update the Web Component**

#### **Updated contact-us.js**

```javascript
class ContactUsForm extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this.shadowRoot.innerHTML = `
      <style>
        form {
          display: flex;
          flex-direction: column;
          width: 300px;
          font-family: Arial, sans-serif;
        }
        input, textarea, button {
          margin-bottom: 10px;
          padding: 8px;
          font-size: 16px;
        }
        button {
          background-color: #007BFF;
          color: white;
          border: none;
          cursor: pointer;
        }
        button:hover {
          background-color: #0056b3;
        }
      </style>
      <form id="contact-form">
        <input type="text" id="name" placeholder="Your Name" required />
        <input type="email" id="email" placeholder="Your Email" required />
        <textarea id="message" placeholder="Your Message" rows="4" required></textarea>
        <button type="submit">Submit</button>
      </form>
    `;

    // Attach event listener to the form
    this.shadowRoot.querySelector('#contact-form').addEventListener('submit', this.handleSubmit.bind(this));
  }

  // Handle form submission
  handleSubmit(event) {
    event.preventDefault();

    const name = this.shadowRoot.querySelector('#name').value;
    const email = this.shadowRoot.querySelector('#email').value;
    const message = this.shadowRoot.querySelector('#message').value;

    // Emit a custom event with form data
    this.dispatchEvent(
      new CustomEvent('formSubmitted', {
        detail: { name, email, message },
        bubbles: true,
        composed: true,
      })
    );

    alert('Form submitted successfully!');
  }

  // Handle property updates
  static get observedAttributes() {
    return ['name', 'email', 'message'];
  }

  attributeChangedCallback(name, oldValue, newValue) {
    if (name === 'name') {
      this.shadowRoot.querySelector('#name').value = newValue;
    }
    if (name === 'email') {
      this.shadowRoot.querySelector('#email').value = newValue;
    }
    if (name === 'message') {
      this.shadowRoot.querySelector('#message').value = newValue;
    }
  }
}

// Define the custom element
customElements.define('contact-us', ContactUsForm);
```

---

### **Step 2: Using the Updated Web Component in Angular**

#### **Angular Component (app.component.ts)**

```typescript
import { Component, ElementRef, OnInit } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent implements OnInit {
  constructor(private elRef: ElementRef) {}

  ngOnInit() {
    // Access the custom web component
    const contactForm = this.elRef.nativeElement.querySelector('contact-us');

    // Set values dynamically
    contactForm.setAttribute('name', 'John Doe');
    contactForm.setAttribute('email', 'john.doe@example.com');

    // Listen to the custom event
    contactForm.addEventListener('formSubmitted', (event: any) => {
      console.log('Form Data:', event.detail);
    });
  }
}
```

#### **Angular Template (app.component.html)**

```html
<h1>Contact Us</h1>
<contact-us></contact-us>
```

---

### **Step 3: Using the Updated Web Component in React**

#### **React Component (App.js)**

```javascript
import React, { useEffect, useRef } from 'react';

function App() {
  const contactFormRef = useRef(null);

  useEffect(() => {
    const contactForm = contactFormRef.current;

    // Set values dynamically
    contactForm.setAttribute('name', 'Jane Doe');
    contactForm.setAttribute('email', 'jane.doe@example.com');

    // Listen to the custom event
    const handleFormSubmitted = (event) => {
      console.log('Form Data:', event.detail);
    };

    contactForm.addEventListener('formSubmitted', handleFormSubmitted);

    return () => {
      contactForm.removeEventListener('formSubmitted', handleFormSubmitted);
    };
  }, []);

  return (
    <div>
      <h1>Contact Us</h1>
      <contact-us ref={contactFormRef}></contact-us>
    </div>
  );
}

export default App;
```

---

### **How This Works**
1. **Custom Events**:
   - The `formSubmitted` event is emitted when the form is submitted, sending form data to the parent application.
   - The event is captured in both Angular and React using event listeners.

2. **Passing Values**:
   - Angular and React set attributes (`name`, `email`, `message`) on the web component. These attributes are observed by the `attributeChangedCallback` method in the web component.

---

This implementation ensures that the **web component** can communicate bi-directionally with Angular and React applications via **custom events** and **property binding**. Would you like further enhancements, like API integration?



# Web components and its life cycle

### **What are Web Components?**

**Web Components** are a set of modern web platform APIs that allow developers to create reusable, encapsulated, and customizable HTML elements. They enable the creation of self-contained components that work seamlessly with existing web technologies and frameworks.

---

### **Key Features of Web Components**
1. **Encapsulation**: Components' styles and behavior are isolated from the rest of the document.
2. **Reusability**: Components can be reused across projects and frameworks.
3. **Interoperability**: Components are framework-agnostic, meaning they can work in any environment.

---

### **Core Technologies of Web Components**

1. **Custom Elements**  
   - Define your own HTML elements with custom behavior using JavaScript.
   - Example:
     ```javascript
     class MyComponent extends HTMLElement {
       connectedCallback() {
         this.innerHTML = `<p>Hello, Web Components!</p>`;
       }
     }
     customElements.define('my-component', MyComponent);
     ```
   - Usage in HTML:
     ```html
     <my-component></my-component>
     ```

2. **Shadow DOM**  
   - Provides encapsulation for the DOM and styles of a component.
   - Example:
     ```javascript
     class ShadowComponent extends HTMLElement {
       constructor() {
         super();
         const shadowRoot = this.attachShadow({ mode: 'open' });
         shadowRoot.innerHTML = `
           <style>
             p { color: red; }
           </style>
           <p>Shadow DOM Content</p>
         `;
       }
     }
     customElements.define('shadow-component', ShadowComponent);
     ```
   - The styles inside the shadow DOM are scoped and do not leak outside.

3. **HTML Templates and `<template>` Element**  
   - Define HTML templates that can be cloned and reused without rendering initially.
   - Example:
     ```html
     <template id="my-template">
       <p>This is reusable template content.</p>
     </template>

     <script>
       const template = document.getElementById('my-template');
       document.body.appendChild(template.content.cloneNode(true));
     </script>
     ```

4. **HTML Imports (Deprecated)**  
   - Previously used to import HTML documents; now replaced by ES Modules.

---

### **Deep Dive into Web Component APIs**

#### **1. Custom Elements Lifecycle Callbacks**
Custom elements have lifecycle methods to hook into different phases of their existence:
- **`connectedCallback`**: Invoked when the element is added to the DOM.
- **`disconnectedCallback`**: Invoked when the element is removed from the DOM.
- **`attributeChangedCallback`**: Invoked when an observed attribute changes.
- **`adoptedCallback`**: Invoked when the element is moved to a new document.

Example:
```javascript
class LifecycleComponent extends HTMLElement {
  connectedCallback() {
    console.log('Added to the DOM');
  }
  disconnectedCallback() {
    console.log('Removed from the DOM');
  }
  attributeChangedCallback(name, oldValue, newValue) {
    console.log(`${name} attribute changed from ${oldValue} to ${newValue}`);
  }
  static get observedAttributes() {
    return ['data-value'];
  }
}
customElements.define('lifecycle-component', LifecycleComponent);
```

---

#### **2. Shadow DOM Modes**
- **Open**: Allows external scripts to access the shadow root using `element.shadowRoot`.
- **Closed**: Prevents external access to the shadow root.
- Example:
  ```javascript
  const shadowRoot = this.attachShadow({ mode: 'open' }); // or 'closed'
  ```

---

#### **3. Slots**
To pass custom HTML content to a web component from Angular, you can use slots in the web component's Shadow DOM. Slots allow you to define placeholders for external content that can be dynamically inserted into the web component.
- Example:
  ```javascript
  class SlotComponent extends HTMLElement {
    constructor() {
      super();
      const shadowRoot = this.attachShadow({ mode: 'open' });
      shadowRoot.innerHTML = `
        <slot name="header"></slot>
        <p>Default content</p>
      `;
    }
  }
  customElements.define('slot-component', SlotComponent);
  ```
  Usage:
  ```html
  <slot-component>
    <div slot="header">Custom Header Content</div>
  </slot-component>
  ```

---

#### **4. Scoped Styles**
Styles within a shadow DOM are scoped to the component and do not leak to the global DOM.
Example:
```javascript
const shadowRoot = this.attachShadow({ mode: 'open' });
shadowRoot.innerHTML = `
  <style>
    p { color: blue; }
  </style>
  <p>This style is scoped.</p>
`;
```

---

### **Advantages of Web Components**
1. **Encapsulation**: Ensures styles and scripts do not affect other parts of the application.
2. **Reusability**: Write once, use anywhere.
3. **Interoperability**: Works across all frameworks or none at all.
4. **Ease of Maintenance**: Component-based architecture simplifies updates.

---

# Shadow DOM Modes - Open and Close


### **Shadow DOM Modes**

#### **1. `mode: 'open'`**
- The shadow root is **accessible** from the outside using JavaScript.
- You can access it via the `shadowRoot` property of the element.

##### Example:
```javascript
class OpenShadowComponent extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this.shadowRoot.innerHTML = `
      <style>
        p { color: blue; }
      </style>
      <p>This is in the open Shadow DOM</p>
    `;
  }
}
customElements.define('open-shadow', OpenShadowComponent);

// Accessing the shadow root
const component = document.querySelector('open-shadow');
console.log(component.shadowRoot); // Logs the shadow root
```

##### Key Points:
1. Styles and DOM inside the shadow root are encapsulated.
2. You can manipulate or inspect the shadow DOM from outside using `element.shadowRoot`.

---

#### **2. `mode: 'closed'`**
- The shadow root is **not accessible** from the outside.
- The `shadowRoot` property of the element returns `null`.

##### Example:
```javascript
class ClosedShadowComponent extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'closed' });
    this.shadowRoot.innerHTML = `
      <style>
        p { color: red; }
      </style>
      <p>This is in the closed Shadow DOM</p>
    `;
  }
}
customElements.define('closed-shadow', ClosedShadowComponent);

// Attempting to access the shadow root
const component = document.querySelector('closed-shadow');
console.log(component.shadowRoot); // Logs: null
```

##### Key Points:
1. Styles and DOM inside the shadow root are still encapsulated.
2. External scripts cannot manipulate or inspect the shadow DOM.

---

### **When to Use `open` vs. `closed`**
1. **Use `open`**:
   - If you want developers or tools to access and manipulate the Shadow DOM.
   - Example: A library that provides Web Components and expects the user to customize them.

2. **Use `closed`**:
   - If you want to enforce strict encapsulation and prevent external access.
   - Example: Components where internal structure must remain private, like secure widgets.

---