Here’s how to create a session-timeout component in Angular using a Web Worker for detecting inactivity and logging out the user after a 10-minute timeout.

---

### Steps:

1. Create a Web Worker: The Web Worker will monitor inactivity.
2. Set Up the Component: Display the session timeout message and handle logout.
3. Communicate Between Worker and Component: Use postMessage and onmessage for interaction.

###  Folder Structure

```
/src
  /app
    /session-timeout
      session-timeout.component.ts
      session-timeout.component.html
      session-timeout.component.css
    /services
      web-worker.service.ts
  /workers
    inactivity.worker.ts
```

---

### **Web Worker: inactivity.worker.ts**
No changes are needed here. This worker tracks inactivity.

```typescript
// inactivity.worker.ts
let timeoutId: any = null;
let inactivityTime = 0;

self.onmessage = (message) => {
  if (message.data === 'start') {
    startInactivityTimer();
  } else if (message.data === 'reset') {
    resetInactivityTimer();
  }
};

function startInactivityTimer() {
  resetInactivityTimer();
  timeoutId = setInterval(() => {
    inactivityTime += 1;
    if (inactivityTime >= 10 * 60) {
      clearInterval(timeoutId);
      postMessage('logout');
    } else if (inactivityTime >= (10 * 60) - 60) {
      postMessage('warning');
    }
  }, 1000); // Check every second
}

function resetInactivityTimer() {
  inactivityTime = 0;
  if (timeoutId) {
    clearInterval(timeoutId);
  }
  startInactivityTimer();
}
```

---

### **WebWorkerService: web-worker.service.ts**

This service encapsulates all communication with the Web Worker.

```typescript
import { Injectable, NgZone } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class WebWorkerService {
  private worker: Worker | null = null;

  constructor(private ngZone: NgZone) {}

  initializeWorker(workerPath: string) {
    if (typeof Worker !== 'undefined') {
      this.worker = new Worker(workerPath);
    } else {
      console.error('Web Workers are not supported in this environment.');
    }
  }

  startWorker() {
    this.worker?.postMessage('start');
  }

  resetWorker() {
    this.worker?.postMessage('reset');
  }

  onMessage(callback: (data: any) => void) {
    if (this.worker) {
      this.worker.onmessage = ({ data }) => {
        this.ngZone.run(() => callback(data));
      };
    }
  }

  terminateWorker() {
    this.worker?.terminate();
    this.worker = null;
  }
}
```

---

### **SessionTimeout Component**

#### **session-timeout.component.ts**

This component now uses the `WebWorkerService` to handle worker interactions.

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { WebWorkerService } from '../services/web-worker.service';

@Component({
  selector: 'app-session-timeout',
  templateUrl: './session-timeout.component.html',
  styleUrls: ['./session-timeout.component.css']
})
export class SessionTimeoutComponent implements OnInit, OnDestroy {
  message: string = '';

  constructor(private webWorkerService: WebWorkerService) {}

  ngOnInit() {
    this.webWorkerService.initializeWorker(new URL('../../workers/inactivity.worker', import.meta.url));

    this.webWorkerService.startWorker();

    this.webWorkerService.onMessage((data) => {
      if (data === 'warning') {
        this.message = 'Your session will expire in the next few minutes due to inactivity.';
      } else if (data === 'logout') {
        this.logout();
      }
    });

    this.addUserActivityListeners();
  }

  ngOnDestroy() {
    this.webWorkerService.terminateWorker();
    this.removeUserActivityListeners();
  }

  addUserActivityListeners() {
    ['mousemove', 'keydown', 'click'].forEach(event => {
      window.addEventListener(event, this.resetTimer);
    });
  }

  removeUserActivityListeners() {
    ['mousemove', 'keydown', 'click'].forEach(event => {
      window.removeEventListener(event, this.resetTimer);
    });
  }

  resetTimer = () => {
    this.webWorkerService.resetWorker();
  };

  logout() {
    this.message = 'Your session has expired due to inactivity.';
    // Perform logout actions, such as redirecting to the login page
    console.log('User logged out.');
  }
}
```

---

#### **session-timeout.component.html**
No changes.

```html
<div *ngIf="message" class="session-timeout">
  {{ message }}
</div>
```

---

#### **session-timeout.component.css**
No changes.

```css
.session-timeout {
  background-color: #f8d7da;
  color: #721c24;
  padding: 15px;
  border: 1px solid #f5c6cb;
  border-radius: 5px;
  text-align: center;
  margin: 10px 0;
}
```

---

### **Benefits of the Updated Approach**
1. **Separation of Concerns**:
   - The Web Worker logic is encapsulated in the `WebWorkerService`.
   - The `SessionTimeoutComponent` is cleaner and only focuses on UI and user interaction.

2. **Reusability**:
   - The `WebWorkerService` can be reused in other components or modules if needed.

3. **Scalability**:
   - Additional worker messages or functionalities can be added to the `WebWorkerService` without modifying the component.

----

## what is ```import.meta.url``` ?

`import.meta.url` is a special property in JavaScript that provides the URL of the current module. It is part of the **ES Modules** specification and is used in environments that support ES modules, like modern browsers and Node.js. It can be used to dynamically resolve file paths relative to the current module.

----