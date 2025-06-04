# **Renderer2**

In Angular, `Renderer2` is a service that provides a way to manipulate the DOM in a safe and platform-agnostic way — meaning it's the preferred way to interact with elements, especially in SSR (server-side rendering) environments.

---

### ✅ Use Case Example: Add a CSS class on button click

Let’s say you want to add a class to a `<div>` when a button is clicked — using `Renderer2`.
You can do multiple manipulations like add class, remove class etc.. with renderer2 service. 

---

### 🧱 Step-by-Step Example

#### 1. **HTML (component template):**

```html
<!-- app.component.html -->
<div #myDiv>This is a div</div>
<button (click)="addClass()">Add Class</button>
```

---

#### 2. **TypeScript (component code):**

```ts
// app.component.ts
import { Component, ElementRef, Renderer2, ViewChild } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
})
export class AppComponent {
  @ViewChild('myDiv') myDiv!: ElementRef;

  constructor(private renderer: Renderer2) {}

  addClass() {
    this.renderer.addClass(this.myDiv.nativeElement, 'highlight');
  }
}
```

---

### 💡 Why use `Renderer2`?

- Safer than `nativeElement.style`, especially for SSR (e.g., Angular Universal).
- Prevents direct access to the DOM — Angular can optimize and secure rendering.
- Makes your code more portable and testable.

---