# **Angular Lifecycle Hooks**

Angular provides lifecycle hooks that allow you to tap into different stages of a component or directive's existence.

### **1. Creation Phase**
Occurs when Angular creates a component or directive and initializes its inputs and outputs.

#### **Hooks in Creation Phase:**
1. **`constructor()`**
   - Purpose: Initializes the component or directive.
   - Timing: Called when the component or directive is instantiated.
   - Usage: Set up dependencies injected via Angular's Dependency Injection.
   - **Note:** Avoid heavy logic or DOM manipulations here.

   ```typescript
   constructor(private service: DataService) {
       console.log('Component instantiated');
   }
   ```

2. **`ngOnChanges(changes: SimpleChanges)`**
   - Purpose: Responds to changes in input-bound properties.
   - Timing: Called before `ngOnInit` and whenever an input property changes.
   - Parameters:
     - `changes`: An object containing the current and previous values of the input properties.
   - Usage: React to updates in `@Input` properties.

   ```typescript
   ngOnChanges(changes: SimpleChanges): void {
       console.log('Input properties changed:', changes);
   }
   ```

3. **`ngOnInit()`**
   - Purpose: Perform component initialization and set up the initial state.
   - Timing: Called once after the first `ngOnChanges`.
   - Usage: Initialize properties, fetch initial data, or configure services.

   ```typescript
   ngOnInit(): void {
       console.log('Component initialized');
   }
   ```

---

### **2. Change Detection Phase**
Angular checks the bindings of components and directives for updates.

#### **Hooks in Change Detection Phase:**
4. **`ngDoCheck()`**
   - Purpose: Custom change detection logic.
   - Timing: Called during every change detection cycle.
   - Usage: Fine-tune performance or detect changes Angular doesn’t catch automatically.
   - **Caution:** Avoid heavy computations here to prevent performance bottlenecks.

   ```typescript
   ngDoCheck(): void {
       console.log('Custom change detection logic executed');
   }
   ```

5. **`ngAfterContentInit()`**
   - Purpose: Respond after content projection (i.e., `<ng-content>`) is initialized.
   - Timing: Called once after the component's projected content has been checked.
   - Usage: Access content children or handle operations requiring projected content.

   ```typescript
   ngAfterContentInit(): void {
       console.log('Content projection initialized');
   }
   ```

6. **`ngAfterContentChecked()`**
   - Purpose: Respond after content projection is checked.
   - Timing: Called after every change detection cycle for projected content.
   - Usage: Ensure content remains in sync.

   ```typescript
   ngAfterContentChecked(): void {
       console.log('Projected content checked');
   }
   ```

7. **`ngAfterViewInit()`**
   - Purpose: Respond after the component's view and child views are initialized.
   - Timing: Called once after Angular initializes the view.
   - Usage: Access child components or manipulate the DOM.

   ```typescript
   ngAfterViewInit(): void {
       console.log('Component view initialized');
   }
   ```

8. **`ngAfterViewChecked()`**
   - Purpose: Respond after the component's view and child views are checked.
   - Timing: Called after every change detection cycle for the view.
   - Usage: Perform DOM updates after Angular processes the view.

   ```typescript
   ngAfterViewChecked(): void {
       console.log('Component view checked');
   }
   ```

---

### **3. Destruction Phase**
Occurs when Angular removes the component or directive from the DOM.

#### **Hooks in Destruction Phase:**
9. **`ngOnDestroy()`**
   - Purpose: Clean up resources, subscriptions, or event listeners.
   - Timing: Called before the component or directive is destroyed.
   - Usage: Prevent memory leaks by unsubscribing from observables or detaching event listeners.

   ```typescript
   ngOnDestroy(): void {
       console.log('Component destroyed');
   }
   ```

---

### **Complete Lifecycle Flow**
1. `constructor`
2. `ngOnChanges` (if applicable)
3. `ngOnInit`
4. `ngDoCheck`
5. `ngAfterContentInit`
6. `ngAfterContentChecked`
7. `ngAfterViewInit`
8. `ngAfterViewChecked`
9. (Repeat `ngDoCheck`, `ngAfterContentChecked`, and `ngAfterViewChecked` during change detection)
10. `ngOnDestroy`

---

### **Key Concepts of Lifecycle Hooks**

1. **Parent vs. Child Component Lifecycle**:
   - The parent component's lifecycle hooks are executed before its child components’ hooks.
   - Example:
     - Parent’s `ngOnInit` is called before the child’s `ngOnInit`.

2. **Content vs. View Lifecycle**:
   - Content hooks (`ngAfterContentInit`, `ngAfterContentChecked`) deal with projected content (`<ng-content>`).
   - View hooks (`ngAfterViewInit`, `ngAfterViewChecked`) deal with the component's own template and its children.

3. **Change Detection**:
   - Angular’s change detection triggers hooks like `ngDoCheck`, `ngAfterContentChecked`, and `ngAfterViewChecked` after every cycle.
   - Hooks are useful for custom logic but should avoid heavy computations.

---

# **What is `ng-content`?**

The `ng-content` directive in Angular is used for **content projection**, allowing you to pass content from a parent component to a child component's specified placeholder.

#### **Key Points:**
1. It acts as a placeholder in a child component where the parent component can project its content.
2. Content passed into `ng-content` is not part of the child component's template. Instead, it is inserted dynamically into the component.
3. **Reusability**: `ng-content` enhances reusability by allowing the same component to render different content in different contexts.
4. **Dynamic Updates**: The parent-provided content updates dynamically, whereas a normal template requires direct changes to the child component.
---

### **Example**

#### **Parent Component Template:**
```html
<app-child>
  <p>This content is projected!</p>
</app-child>
```

#### **Child Component Template:**
```html
<div>
  <h2>This is the child component</h2>
  <ng-content></ng-content>
</div>
```

#### **Output:**
```html
<div>
  <h2>This is the child component</h2>
  <p>This content is projected!</p>
</div>
```

---

# **`ngOnChanges` Lifecycle Hook**

The `ngOnChanges` lifecycle hook is triggered **when an `@Input` property of a component or directive changes**. It allows you to handle changes to these inputs and react accordingly.

---

### **When `ngOnChanges` is Called**
1. **Input Property Changes**
   - Whenever a parent component updates a value bound to the child’s `@Input` property.
   - Example:
     ```typescript
     @Input() someValue: string;

     ngOnChanges(changes: SimpleChanges) {
       console.log('Input changed:', changes);
     }
     ```
   - If the `someValue` changes in the parent component, `ngOnChanges` is triggered in the child.

2. **During Component Initialization**
   - Called once at component initialization if the component has `@Input` properties.
   - Even if the initial value of an `@Input` is `undefined` or `null`, `ngOnChanges` is called with the initial value.

3. **Bound Data Updates**
   - Any changes to the reference of an object or array bound to an `@Input` property trigger `ngOnChanges`.

---

### **When `ngOnChanges` is Not Called**
1. **No `@Input` Properties**
   - If the component does not have any `@Input` properties, `ngOnChanges` is never called.

2. **Mutations to Objects or Arrays**
   - If the parent mutates the contents of an array or object but doesn’t change its reference, `ngOnChanges` is not called.
     ```typescript
     @Input() myArray: number[];

     // This won't trigger ngOnChanges because the reference remains the same
     this.myArray.push(4);
     ```

3. **No Change in Input Value**
   - If the input property is bound to the same value or reference repeatedly, `ngOnChanges` is not called.

4. **Direct Changes Within the Component**
   - If the input property is modified directly in the child component, `ngOnChanges` is not called because it monitors parent-to-child communication.

---

# **`ngAfterViewInit` Lifecycle Hook**

The `ngAfterViewInit` lifecycle hook in Angular is triggered **after the component’s view (and child views) has been initialized**. It is used to perform operations that depend on the DOM or child components being fully rendered.

---

### **When `ngAfterViewInit` is Called**

1. **Component Has a Template**
   - `ngAfterViewInit` is always called if the component has a template (HTML) because the view needs to be initialized.
   - Example:
     ```typescript
     ngAfterViewInit() {
       console.log('View initialized!');
     }
     ```

2. **Child Components/Directives in Template**
   - If the component contains child components or directives, `ngAfterViewInit` ensures that those child views have been initialized before it is called.

3. **Always Called After `ngOnInit`**
   - The hook is invoked after `ngOnInit`, ensuring the DOM is fully ready for any manipulation or interaction.

---

### **When `ngAfterViewInit` is Not Called**

1. **No Template (Headless Component)**
   - If the component does not have a template (e.g., it's a purely programmatic component without an associated HTML), `ngAfterViewInit` will not be called.
     ```typescript
     @Component({
       selector: 'app-headless',
       template: ''
     })
     export class HeadlessComponent {
       ngAfterViewInit() {
         console.log('This will not be called');
       }
     }
     ```

2. **Structural Directives Without Component**
   - Structural directives (like `*ngIf`, `*ngFor`) applied on the component itself may delay the rendering of a component, and `ngAfterViewInit` won’t be called until the component is fully added to the DOM.

3. **Detached or Destroyed Before Initialization**
   - If a component is dynamically created and destroyed (e.g., in cases where the lifecycle is interrupted), `ngAfterViewInit` may not be called.

---

# **`ngAfterViewChecked` Lifecycle Hook**

The `ngAfterViewChecked` lifecycle hook in Angular is called **after the component’s view (and its child views) have been checked by Angular’s change detection mechanism**. It is invoked every time change detection runs, whether triggered by Angular or manually.

---

### **When `ngAfterViewChecked` is Called**

1. **After Every Change Detection Cycle**
   - It is called after Angular performs its checks to ensure the view and child views are in sync with the component's data model.
   - Example:
     ```typescript
     ngAfterViewChecked() {
       console.log('ngAfterViewChecked called!');
     }
     ```

2. **After `ngAfterViewInit`**
   - It is called immediately after `ngAfterViewInit` and is invoked every subsequent time the view is checked.

3. **Triggered by Input Changes**
   - If an input property changes and triggers change detection, `ngAfterViewChecked` is called.

4. **Triggered by Events**
   - User interactions, such as button clicks or form inputs, that trigger change detection will also invoke this hook.

5. **Dynamic Updates**
   - When a component dynamically updates data that affects its view, this hook is called to re-check the view.

---

### **When `ngAfterViewChecked` is Not Called**

1. **No View Changes**
   - If no change detection is triggered, this hook will not be called.
   - For example, in a static application where no data-binding or events occur, this hook may only be called during initialization.

2. **Component Destroyed**
   - Once a component is destroyed (via `ngOnDestroy`), this hook is no longer called.

3. **No Template or Headless Component**
   - If the component has no associated template (e.g., a service-like component), `ngAfterViewChecked` is not invoked because there is no view to check.

---

# **How Angular Performs Change Detection Mechanism**

Angular’s change detection mechanism is the process by which the framework ensures that the application’s view (DOM) is always in sync with the underlying data model.

---

### **Change Detection Workflow**

1. **Triggering Change Detection**
   - Change detection is triggered by:
     - User interactions (e.g., clicks, input events).
     - Asynchronous operations (e.g., HTTP requests, `setTimeout`, Promises).
     - Explicit calls to `ChangeDetectorRef.detectChanges()`.

2. **Checking Views**
   - The **root view** is checked first, and Angular recursively traverses the component tree to check all child views.
   - Each component’s bindings and DOM updates are validated.

3. **Dirty Checking**
   - Angular performs **dirty checking** to compare the current value of each property bound to the template with its previous value.
   - If a change is detected, Angular updates the DOM.

4. **Life Cycle Hooks**
   - Angular invokes lifecycle hooks like `ngOnInit`, `ngDoCheck`, `ngAfterViewChecked`, and others during the change detection cycle.

5. **Zone.js Notification**
   - Zone.js ensures that all async events result in a notification to Angular to trigger the next change detection cycle.

---

### **Strategies for Change Detection**

1. **Default Strategy**
   - Change detection runs for the entire component tree, even if only a single property changes.
   - This is the default behavior in Angular.

2. **OnPush Strategy**
   - For components with the `ChangeDetectionStrategy.OnPush`, Angular skips change detection unless:
     - An input property of the component changes.
     - An event bound to the component is triggered.
     - The component or a child is explicitly marked for check using `markForCheck`.

---

### **Optimizations in Change Detection**

1. **Use `OnPush` Strategy**:
   - Ensures Angular only checks components with updated inputs, improving performance for large applications.

2. **Immutable Data Structures**:
   - Immutable data makes it easier for Angular to detect changes because new references indicate changes directly.

3. **Avoid Binding Complex Expressions**:
   - Avoid binding expressions like `methodCall()` in templates. Instead, pre-compute values in the component.

4. **Manual Triggering**:
   - Use `ChangeDetectorRef` for manual control over the detection cycle:
     - `markForCheck()` to trigger detection in specific parts of the tree.
     - `detach()` to skip detection for a component.

---
