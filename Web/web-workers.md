# Web workers, its life cycle and use

Web Workers are a JavaScript API that allows you to run scripts in the background, separate from the main browser thread. This feature is crucial for performance improvements in web applications, especially when handling computationally heavy tasks or tasks that need to be performed asynchronously without blocking the user interface (UI).

---

### **Key Concepts**

#### **1. What is a Web Worker?**
A **Web Worker** is a thread that runs independently of the main execution thread (UI thread). It provides a way to offload time-consuming tasks like:
- Data processing (e.g., parsing JSON, performing complex calculations).
- Image manipulation.
- Real-time data updates (e.g., WebSocket message handling).
- Running background timers.

---

#### **2. Types of Web Workers**
1. **Dedicated Web Workers**:
   - Each worker is dedicated to a single script or task.
   - Communication happens only between the worker and the script that created it.

2. **Shared Web Workers**:
   - Can be shared between multiple scripts or browser contexts (e.g., tabs or iframes).
   - Useful for applications that need shared state between tabs.

3. **Service Workers**:
   - Special type of web worker used for background tasks like caching and push notifications.
   - Typically used in Progressive Web Apps (PWAs).

---

#### **3. Web Worker Lifecycle**
1. **Creation**: A web worker is created using the `Worker` constructor.
2. **Communication**: Main thread and worker communicate via `postMessage` and `onmessage`.
3. **Termination**: Worker can terminate itself (`self.close()`) or be terminated by the main thread (`worker.terminate()`).

---

### **How Web Workers Work**

#### **Main Thread:**
The main thread creates a worker using the `Worker` constructor and specifies the script to be executed.

```javascript
// Create a new web worker
const worker = new Worker('worker.js');

// Listen for messages from the worker
worker.onmessage = function(event) {
  console.log('Message from worker:', event.data);
};

// Send a message to the worker
worker.postMessage('Hello, worker!');
```

#### **Worker Thread (`worker.js`):**
The worker script listens for messages from the main thread and sends responses back.

```javascript
// Listen for messages from the main thread
self.onmessage = function(event) {
  console.log('Message from main thread:', event.data);
  // Perform some computation
  const result = `Worker received: ${event.data}`;
  // Send the result back to the main thread
  self.postMessage(result);
};

// Optional: Terminate the worker
// self.close();
```

---

### **Features of Web Workers**

1. **Thread Isolation**:
   - Runs in a completely separate thread.
   - No access to the DOM or `window` object (but can use `self` for the worker's global context).

2. **Communication**:
   - Achieved through message-passing (`postMessage` and `onmessage`).
   - Can handle large data using transferable objects for efficient memory management.

3. **APIs Available in Workers**:
   - `XMLHttpRequest` or `fetch` for network requests.
   - `setTimeout` and `setInterval`.
   - Cryptography APIs (`crypto`).
   - WebSockets.

4. **Error Handling**:
   - Use `onerror` in the main thread to capture errors in the worker.

---

# **Example: Factorial Calculation Using Web Worker**

#### **worker.js** (Worker Script)

```javascript
self.onmessage = function(event) {
  const number = event.data;
  let factorial = 1;

  for (let i = 1; i <= number; i++) {
    factorial *= i;
  }

  // Send the result back to the main thread
  self.postMessage(factorial);
};
```

#### **app.component.ts** (Main Angular Component)

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent implements OnInit {
  result: number | null = null;
  inputNumber: number = 10000; // Example number for factorial

  constructor() {}

  ngOnInit() {
    this.calculateFactorial();
  }

  calculateFactorial() {
    const worker = new Worker('worker.js');

    // Send the number to the worker
    worker.postMessage(this.inputNumber);

      // Listen for messages from the worker
    worker.onmessage = (event: MessageEvent) => {
      this.result = event.data; // Store the result
    };

    // Terminate worker when done
    worker.terminate();
  }
}
```

# **Example: Uploading Large File Using Web Worker**

#### **worker.js** (Worker Script)

```javascript
self.onmessage = async function(event) {
  const { file, chunkSize, currentChunk, totalChunks } = event.data;

  if (currentChunk >= totalChunks) {
    // File upload completed
    self.postMessage({ done: true });
    return;
  }

  const start = currentChunk * chunkSize;
  const end = Math.min(start + chunkSize, file.size);
  const chunk = file.slice(start, end);

  const formData = new FormData();
  formData.append('file', chunk, file.name);

  try {
    const response = await fetch('/upload', {
      method: 'POST',
      body: formData,
    });

    if (response.ok) {
      // Proceed to next chunk
      self.postMessage({ currentChunk: currentChunk + 1, totalChunks });
    } else {
      self.postMessage({ error: 'Upload failed' });
    }
  } catch (error) {
    self.postMessage({ error: 'Upload failed' });
  }
};
```

---

#### **app.component.ts** (Main Angular Component)

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent {
  file: File | null = null;
  uploadProgress: number = 0;
  message: string = '';

  handleFileInput(event: any) {
    this.file = event.target.files[0];
  }

  async uploadFile() {
    if (!this.file) {
      this.message = 'Please select a file to upload.';
      return;
    }

    const chunkSize = 1 * 1024 * 1024; // 1MB chunks
    const totalChunks = Math.ceil(this.file.size / chunkSize);

    const worker = new Worker('worker.js');

    worker.onmessage = (event: any) => {
      if (event.data.done) {
        this.message = 'File uploaded successfully!';
        worker.terminate();
      } else if (event.data.error) {
        this.message = 'Upload failed!';
        worker.terminate();
      } else {
        this.uploadProgress = (event.data.currentChunk / totalChunks) * 100;
        worker.postMessage({
          file: this.file,
          chunkSize,
          currentChunk: event.data.currentChunk,
          totalChunks,
        });
      }
    };

    worker.postMessage({
      file: this.file,
      chunkSize,
      currentChunk: 0,
      totalChunks,
    });
  }
}
```

---

#### **app.component.html** (Main Angular Template)

```html
<h1>Upload Large File with Web Worker</h1>
<input type="file" (change)="handleFileInput($event)" />
<button (click)="uploadFile()">Upload</button>
<p *ngIf="uploadProgress > 0">Progress: {{ uploadProgress | number:'1.0-0' }}%</p>
<p>{{ message }}</p>
```

---

## Web Workers are not automatically terminated

When you navigate to another component in an Angular application, **Web Workers are not automatically terminated**. Workers continue running independently of the component lifecycle unless explicitly terminated.

---

### **What Happens When Routing to Another Component:**

1. **Web Worker Life Cycle:**
   - If the worker was created in a component and you navigate to another route (component), the worker will still be active.
   - The worker runs in the background and doesn't have a direct relationship with Angular components or routing.

2. **Termination Required:**
   - If you want the worker to stop when the component is destroyed or navigate away, you need to explicitly terminate the worker.
   
---

### **Example: Explicitly Terminate Worker on Component Destroy**

#### **app.component.ts (with clean-up logic)**

```typescript
export class AppComponent implements OnDestroy {
  
  ngOnDestroy() {
    // Explicitly terminate the worker in ngOnDestroy method
    if (this.worker) {
      this.worker.terminate();
    }
  }

}
```
---

### **Best Practices for Long-Term Worker Use:**
- If you expect the user to interact with multiple components, consider reusing workers (e.g., for background tasks).
- Only terminate workers when absolutely necessary or when the session is closing.

----

## **Steps to Terminate the Worker in Any Other Component:**

#### **Solution: Explicitly Terminate the Worker on Component Destroy**

1. **In the worker-consuming component, create a reference to the worker.**
2. **Ensure termination logic is applied in the `ngOnDestroy` lifecycle hook.**
3. **If navigating to another component, ensure the worker is properly terminated.**

---

#### **Another Component (Example with Worker Termination)**

```typescript
import { Component, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-other',
  templateUrl: './other.component.html',
  styleUrls: ['./other.component.css'],
})
export class OtherComponent implements OnDestroy {
  private worker: Worker | null = null;

  ngOnDestroy() {
    if (this.worker) {
      this.worker.terminate(); // Terminate worker on component destroy
    }
  }
}
```
---

#### **Explanation:**

1. **Worker Reference**: 
   - Store the worker reference (`this.worker`).
2. **Worker Termination on `ngOnDestroy`**: 
   - In both components (`AppComponent` and `OtherComponent`), terminate the worker when the component is destroyed.
3. **Worker Continuation**: 
   - Even when navigating to another component, the worker is not orphaned or left running unnecessarily.

---
