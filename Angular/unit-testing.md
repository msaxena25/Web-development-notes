# Jasmine Important methods - 

Jasmine provides a wide range of methods and utilities to help with unit testing. Below are some of the **important Jasmine methods** commonly used for unit testing in Angular and general JavaScript applications:

---

### **1. Basic Testing Methods**

#### **`describe`**
Defines a test suite (a group of related tests).
```typescript
describe('My Test Suite', () => {
  // Your tests go here
});
```

#### **`it`**
Defines a single test case inside a `describe`.
```typescript
it('should test a specific behavior', () => {
  expect(true).toBe(true);
});
```

#### **`expect`**
Used for assertions to compare actual and expected values.
```typescript
expect(actual).toEqual(expected);
expect(actual).toBeTruthy();
```

---

### **2. Matchers (Assertions)**

#### **Equality Matchers**
- **`toBe`**: Tests strict equality (`===`).
- **`toEqual`**: Tests value equality (deep equality for objects).
```typescript
expect(2 + 2).toBe(4);
expect({ a: 1 }).toEqual({ a: 1 });
```

#### **Truthy/Falsy Matchers**
- **`toBeTruthy`**: Checks if the value is truthy.
- **`toBeFalsy`**: Checks if the value is falsy.
```typescript
expect(true).toBeTruthy();
expect(false).toBeFalsy();
```

#### **Negation**
- **`not`**: Reverses a matcher.
```typescript
expect(true).not.toBeFalsy();
```

#### **Comparison Matchers**
- **`toBeGreaterThan`**: Tests if a value is greater than another.
- **`toBeLessThan`**: Tests if a value is less than another.
```typescript
expect(10).toBeGreaterThan(5);
expect(3).toBeLessThan(5);
```

#### **Other Useful Matchers**
- **`toContain`**: Checks if an array or string contains a value.
- **`toBeDefined` / `toBeUndefined`**: Checks if a variable is defined/undefined.
- **`toBeNull`**: Checks if a value is `null`.
- **`toMatch`**: Checks if a string matches a regular expression.
```typescript
expect([1, 2, 3]).toContain(2);
expect(undefined).toBeUndefined();
expect('Jasmine').toMatch(/Jas/);
```

---

### **3. Spying on Functions**

#### **`spyOn`**
Tracks and replaces a method of an object.
```typescript
const obj = { method: () => 'original' };
spyOn(obj, 'method').and.returnValue('mocked');

expect(obj.method()).toBe('mocked');
expect(obj.method).toHaveBeenCalled();
```

#### **Spy Utilities**
- **`and.returnValue`**: Makes the spy return a specific value.
- **`and.callFake`**: Replaces the method with a custom implementation.
- **`and.callThrough`**: Allows the method to execute its original implementation.
```typescript
spyOn(obj, 'method').and.returnValue('mocked');
spyOn(obj, 'method').and.callFake(() => 'custom implementation');
spyOn(obj, 'method').and.callThrough();
```

#### **Spy Matchers**
- **`toHaveBeenCalled`**: Checks if a spy was called.
- **`toHaveBeenCalledWith`**: Checks if a spy was called with specific arguments.
- **`toHaveBeenCalledTimes`**: Checks the number of times a spy was called.
```typescript
expect(obj.method).toHaveBeenCalled();
expect(obj.method).toHaveBeenCalledWith('arg1', 'arg2');
expect(obj.method).toHaveBeenCalledTimes(2);
```

---

### **4. Test Lifecycle Methods**

#### **`beforeEach`**
Runs before each test in the suite.
```typescript
beforeEach(() => {
  // Initialization code
});
```

#### **`afterEach`**
Runs after each test in the suite.
```typescript
afterEach(() => {
  // Cleanup code
});
```

#### **`beforeAll`**
Runs once before all tests in the suite.
```typescript
beforeAll(() => {
  // One-time setup
});
```

#### **`afterAll`**
Runs once after all tests in the suite.
```typescript
afterAll(() => {
  // One-time teardown
});
```

---

### **5. Asynchronous Testing**

#### **`done`**
Signals completion of an asynchronous test.
```typescript
it('should wait for async operation', (done) => {
  setTimeout(() => {
    expect(true).toBe(true);
    done();
  }, 1000);
});
```

#### **`fakeAsync` and `tick`**
Simulates the passage of time for testing asynchronous code.
```typescript
import { fakeAsync, tick } from '@angular/core/testing';

it('should simulate async behavior', fakeAsync(() => {
  let value = false;
  setTimeout(() => { value = true; }, 1000);

  tick(1000); // Simulates the passage of 1 second
  expect(value).toBeTrue();
}));
```

#### **`async`**
Used to test `Promise`-based code or `Observables`.
```typescript
import { async } from '@angular/core/testing';

it('should wait for async operations', async(() => {
  Promise.resolve('done').then((result) => {
    expect(result).toBe('done');
  });
}));
```

---

### **6. Mocking and Stubbing**

#### **`jasmine.createSpy`**
Creates a standalone spy function.
```typescript
const spy = jasmine.createSpy('mySpy');
spy('arg1');
expect(spy).toHaveBeenCalledWith('arg1');
```

#### **`jasmine.createSpyObj`**
Creates an object with multiple spy methods.
```typescript
const mockService = jasmine.createSpyObj('MockService', ['method1', 'method2']);
mockService.method1.and.returnValue('mocked');

expect(mockService.method1()).toBe('mocked');
```

---

### **7. Custom Matchers**
Create custom matchers for specific assertions.
```typescript
beforeEach(() => {
  jasmine.addMatchers({
    toBeEven: () => ({
      compare: (actual) => ({
        pass: actual % 2 === 0,
        message: `Expected ${actual} to be even`,
      }),
    }),
  });
});

it('should pass for even numbers', () => {
  expect(4).toBeEven();
});
```

---

### **8. Skipping Tests**

#### **`xdescribe`**
Skips the entire suite.
```typescript
xdescribe('Skipped Suite', () => {
  it('will not run', () => {});
});
```

#### **`xit`**
Skips a specific test.
```typescript
xit('will not run', () => {});
```

---

These methods and utilities make Jasmine a powerful framework for writing comprehensive unit tests for Angular and JavaScript applications.

-----------

# JavaScript Mocking ways - 

In Jasmine, there are several ways to mock JavaScript methods or calls to control their behavior during tests. These approaches allow you to isolate the unit being tested and avoid relying on real implementations. Below are the primary ways you can mock methods or calls in Jasmine:

---

### **1. Using `spyOn`**
The `spyOn` method is the most common way to mock methods on existing objects or modules.

#### Example: Mocking an Existing Method
```javascript
const obj = {
  method: () => 'original',
};

spyOn(obj, 'method').and.returnValue('mocked');

it('should use the mocked implementation', () => {
  expect(obj.method()).toBe('mocked');
  expect(obj.method).toHaveBeenCalled();
});
```

---

### **2. Using `jasmine.createSpy`**
`jasmine.createSpy` creates a standalone spy function that you can use in your tests.

#### Example:
```javascript
const mockFunction = jasmine.createSpy('mockFunction');
mockFunction.and.returnValue('mocked');

it('should call the spy function', () => {
  mockFunction();
  expect(mockFunction).toHaveBeenCalled();
  expect(mockFunction()).toBe('mocked');
});
```

---

### **3. Using `jasmine.createSpyObj`**
`jasmine.createSpyObj` creates an object with multiple spy methods.

#### Example:
```javascript
const mockObj = jasmine.createSpyObj('MockObj', ['method1', 'method2']);
mockObj.method1.and.returnValue('mocked1');
mockObj.method2.and.returnValue('mocked2');

it('should call the mocked methods', () => {
  expect(mockObj.method1()).toBe('mocked1');
  expect(mockObj.method2()).toBe('mocked2');
});
```

---

### **4. Using `and.returnValue`**
Sets a fixed return value for the mocked method.

#### Example:
```javascript
spyOn(obj, 'method').and.returnValue('mocked value');

it('should return mocked value', () => {
  expect(obj.method()).toBe('mocked value');
});
```

---

### **5. Using `and.callFake`**
Provides a custom implementation for the mocked method.

#### Example:
```javascript
spyOn(obj, 'method').and.callFake(() => 'custom implementation');

it('should use the custom implementation', () => {
  expect(obj.method()).toBe('custom implementation');
});
```

---

### **6. Using `and.callThrough`**
Calls the original implementation of the method but still tracks its calls.

#### Example:
```javascript
spyOn(obj, 'method').and.callThrough();

it('should call the original implementation', () => {
  expect(obj.method()).toBe('original');
  expect(obj.method).toHaveBeenCalled();
});
```

---

### **7. Mocking Global Functions**
Global functions (like `setTimeout`, `console.log`, etc.) can also be mocked using `spyOn`.

#### Example:
```javascript
spyOn(window, 'setTimeout').and.callFake((callback) => callback());

it('should mock setTimeout', () => {
  let value = false;
  setTimeout(() => { value = true; }, 1000);
  expect(value).toBe(true);
});
```

---

### **8. Mocking Class Methods**
Mock methods on classes using `spyOn`.

#### Example:
```javascript
class MyClass {
  method() {
    return 'original';
  }
}

spyOn(MyClass.prototype, 'method').and.returnValue('mocked');

it('should mock class method', () => {
  const instance = new MyClass();
  expect(instance.method()).toBe('mocked');
});
```

---

### **9. Mocking Object Properties**
Use `Object.defineProperty` to mock getters or setters.

#### Example:
```javascript
const obj = {};
Object.defineProperty(obj, 'property', {
  get: jasmine.createSpy('get').and.returnValue('mocked value'),
});

it('should mock property getter', () => {
  expect(obj.property).toBe('mocked value');
});
```

---

### **10. Mocking Imported Modules**
If you are using ES modules, you can mock dependencies manually or with tools like `spyOn`.

#### Example:
```javascript
import * as MyModule from './my-module';

spyOn(MyModule, 'myFunction').and.returnValue('mocked value');

it('should mock imported module function', () => {
  expect(MyModule.myFunction()).toBe('mocked value');
});
```

---

### **12. Using Custom Spies**
Create custom spies for complex scenarios.

#### Example:
```javascript
const customSpy = jasmine.createSpy('customSpy', (arg) => `processed ${arg}`).and.callThrough();

it('should use the custom spy', () => {
  expect(customSpy('input')).toBe('processed input');
});
```
--------------

# Mock HTML and access HTML elements

---

## **Mocking HTML Elements**

### **1. Create Mock HTML Elements**
You can create mock HTML elements programmatically in your test.

#### Example:
```typescript
const mockElement = document.createElement('div');
mockElement.id = 'testDiv';
mockElement.textContent = 'Hello, World!';
document.body.appendChild(mockElement);

it('should verify mock element', () => {
  const element = document.getElementById('testDiv');
  expect(element.textContent).toBe('Hello, World!');
});
```

---

### **2. Use Angular’s `DebugElement` (for Angular Projects)**
When testing Angular components, use `DebugElement` provided by Angular's `@angular/core/testing`.

#### Example:
```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';

it('should find the element using DebugElement', () => {
  const debugElement = fixture.debugElement.query(By.css('#testDiv'));
  expect(debugElement.nativeElement.textContent).toBe('Hello, World!');
});
```

---

## **Accessing HTML Elements**

### **1. Access by ID or Selector**
You can use standard DOM APIs like `document.getElementById` or `querySelector`.

#### Example:
```typescript
const element = document.getElementById('testDiv');
expect(element).not.toBeNull();
```

#### Example with `querySelector`:
```typescript
const element = document.querySelector('#testDiv');
expect(element.textContent).toBe('Hello, World!');
```

---

### **2. Access in Angular Test Bed**
In Angular unit tests, use `fixture.nativeElement` or `DebugElement` to access elements.

#### Example:
```typescript
const element = fixture.nativeElement.querySelector('#testDiv');
expect(element.textContent).toBe('Hello, World!');
```

---

## **Mocking HTML Events**

### **1. Dispatch Custom Events**
Use `dispatchEvent` to simulate events.

#### Example:
```typescript
const button = document.createElement('button');
button.addEventListener('click', () => {
  button.textContent = 'Clicked!';
});
button.dispatchEvent(new Event('click'));

it('should change text on click', () => {
  expect(button.textContent).toBe('Clicked!');
});
```

---

### **2. Use Angular Event Binding**
For Angular components, trigger events using the `DebugElement.triggerEventHandler`.

#### Example:
```typescript
import { By } from '@angular/platform-browser';

it('should handle click event', () => {
  const button = fixture.debugElement.query(By.css('button'));
  button.triggerEventHandler('click', null);
  fixture.detectChanges();
  
  expect(component.isClicked).toBeTrue();
});
```

---

### **3. Simulate Events with Jasmine Spies**
Mock the event handler function using Jasmine's `spyOn`.

#### Example:
```typescript
const button = document.createElement('button');
const clickHandler = jasmine.createSpy('clickHandler');
button.addEventListener('click', clickHandler);

button.dispatchEvent(new Event('click'));

it('should call clickHandler on click', () => {
  expect(clickHandler).toHaveBeenCalled();
});
```
---

## **Best Practices for Mocking HTML Elements and Events**
1. **Avoid Direct DOM Manipulation in Angular**: Use `DebugElement` or `fixture.nativeElement`.
2. **Use Jasmine Spies for Event Handlers**: This makes it easier to test interactions.
3. **Mock HTML Structure if Needed**: For non-Angular projects, create and manipulate DOM directly.
4. **Simulate Events Explicitly**: Use `dispatchEvent` or `triggerEventHandler` for simulating user interactions.
5. **Keep Tests Isolated**: Mock only the necessary elements and clean up after each test.

With these techniques, you can thoroughly test HTML element interactions and events in your web application.

---------------

# **What is `fixture.detectChanges()`?**

In Angular unit testing, `fixture.detectChanges()` is a method provided by the Angular TestBed framework. It applies changes to the component and its template by running the Angular change detection cycle. This ensures that the DOM reflects the latest state of the component after any updates.

- It processes bindings, directives, and components.
- Useful for testing dynamic updates (e.g., changes caused by property updates, event handlers).

---

# Test cases -  setTimeout, display: none, *ngIf show hide, focus event  

### **Component Code**
Assume you have a component with a `setTimeout` to focus on an input element:

```typescript
import { Component, ElementRef, ViewChild } from '@angular/core';

@Component({
  selector: 'app-my-component',
  template: `
    <div>
      <input #myInput type="text" style="display: none;" />
    </div>
  `,
})
export class MyComponent {
  @ViewChild('myInput', { static: false }) myInput!: ElementRef<HTMLInputElement>;

  showAndFocusElement() {
    setTimeout(() => {
      const input = this.myInput.nativeElement;
      input.style.display = 'block';
      input.focus();
    }, 2000);
  }
}
```

---

### **Test Case**
Here’s the test case for verifying the behavior:

```typescript
  it('should display the input and focus it after 2 seconds', fakeAsync(() => {
    // Trigger the function to show and focus the input
    component.showAndFocusElement();
    fixture.detectChanges();

    // The input should initially not be visible
    let input = fixture.debugElement.query(By.css('input')).nativeElement;
    expect(input.style.display).toBe('none');

    // Fast-forward 2 seconds
    tick(2000);
    fixture.detectChanges();

    // The input should now be visible and focused
    expect(input.style.display).toBe('block');
    // **`document.activeElement`**:  Verifies that the input element is currently focused.
    expect(document.activeElement).toBe(input);
  }));
```

---

### Here's how you can modify the component to use `*ngIf` for hiding the element and write the corresponding test case.

---

### **Component Code**

Updated component using `*ngIf`:

```typescript
import { Component, ElementRef, ViewChild } from '@angular/core';

@Component({
  selector: 'app-my-component',
  template: `
    <div>
      <input *ngIf="isInputVisible" #myInput type="text" />
    </div>
  `,
})
export class MyComponent {
  @ViewChild('myInput', { static: false }) myInput!: ElementRef<HTMLInputElement>;
  isInputVisible = false;

  showAndFocusElement() {
    setTimeout(() => {
      this.isInputVisible = true;
      setTimeout(() => {
        this.myInput?.nativeElement.focus();
      });
    }, 2000);
  }
}
```

---

### **Test Case**

Here’s the updated test case:

```typescript

  it('should display the input and focus it after 2 seconds using *ngIf', fakeAsync(() => {
    // Trigger the function to show and focus the input
    component.showAndFocusElement();
    fixture.detectChanges();

    // The input should not be in the DOM initially
    let input = fixture.debugElement.query(By.css('input'));
    expect(input).toBeNull();

    // Fast-forward 2 seconds
    tick(2000);
    fixture.detectChanges();

    // The input should now be in the DOM and visible
    input = fixture.debugElement.query(By.css('input')).nativeElement;
    expect(input).not.toBeNull();

    // Simulate the second `setTimeout` for focusing
    tick();
    expect(document.activeElement).toBe(input);
  }));
```

---

# setInterval method testing

Here’s how you can write a unit test case for a function with setInterval, which runs every 1 second and clears itself after 20 seconds.

---

```typescript
export class MyComponent {
  intervalId: any;
  counter = 0;

  startInterval() {
    this.counter = 0;
    this.intervalId = setInterval(() => {
      this.counter++;
      if (this.counter === 20) {
        clearInterval(this.intervalId);
      }
    }, 1000);
  }
}
```
---

### **Unit Test**

Here’s the test case for the `startInterval` function:

```typescript
  it('should increment counter every second and stop after 20 seconds', fakeAsync(() => {
    component.startInterval();

    // Initially, counter should be 0
    expect(component.counter).toBe(0);

    // Fast-forward 10 seconds
    tick(10000);
    expect(component.counter).toBe(10);

    // Fast-forward another 10 seconds (total 20 seconds)
    tick(10000);
    expect(component.counter).toBe(20);

    // Verify the interval is cleared (counter doesn't increase further)
    tick(1000);
    expect(component.counter).toBe(20);
  }));
```

---

# Unit testing of component that depend on service

Here’s how you can write a unit test for a component that depends on a service (`OtherService`) and subscribes to its `fetchData` method.

---

### **Component Code**

```typescript
import { Component, OnInit } from '@angular/core';
import { OtherService } from './other.service';

@Component({
  selector: 'app-my-component',
  template: `<p>{{ data }}</p>`,
})
export class MyComponent implements OnInit {
  data: string = '';

  constructor(private otherService: OtherService) {}

  ngOnInit(): void {
    this.otherService.fetchData().subscribe({
      next: (response) => {
        this.data = response.data;
      },
      error: (err) => {
        this.data = 'Error occurred';
      },
    });
  }
}
```
---

### **Unit Test**

Here’s the test case for the `MyComponent`:

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { of, throwError } from 'rxjs';
import { MyComponent } from './my.component';
import { OtherService } from './other.service';

describe('MyComponent', () => {
  let component: MyComponent;
  let fixture: ComponentFixture<MyComponent>;
  let otherServiceSpy: jasmine.SpyObj<OtherService>;

  beforeEach(() => {
    // Create a spy object for OtherService
    otherServiceSpy = jasmine.createSpyObj('OtherService', ['fetchData']);

    TestBed.configureTestingModule({
      declarations: [MyComponent],
      providers: [
        { provide: OtherService, useValue: otherServiceSpy },
      ],
    });

    fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
  });

  it('should display data on success', () => {
    const mockResponse = { data: 'Sample Data' };

    // Mock fetchData to return an observable with mockResponse
    otherServiceSpy.fetchData.and.returnValue(of(mockResponse));

    // This triggers the Angular lifecycle methods (ngOnInit) and allows the component to execute its logic
    fixture.detectChanges(); // Trigger ngOnInit

    expect(component.data).toBe('Sample Data'); // Verify component data
    expect(otherServiceSpy.fetchData).toHaveBeenCalledTimes(1); // Verify service call
  });

  it('should display error message on failure', () => {
    // Mock fetchData to return an observable that throws an error
    otherServiceSpy.fetchData.and.returnValue(throwError(() => new Error('Error occurred')));

    fixture.detectChanges(); // Trigger ngOnInit

    expect(component.data).toBe('Error occurred'); // Verify error handling
    expect(otherServiceSpy.fetchData).toHaveBeenCalledTimes(1); // Verify service call
  });
});
```

---

### Now write unit test of the above OtherService file

Here’s how you can write a unit test for a function that calls another service method, which returns an observable.

---

### **Example Scenario**

Assume you have two services:

1. **`MyService`**: The main service containing the function to be tested.
2. **`OtherService`**: Another service whose method is being called by `MyService`.

---

### **Service Code**

```typescript
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class OtherService {
  fetchData(): Observable<any> {
    // Simulate an API call
    return new Observable((observer) => {
      observer.next({ data: 'Sample Data' });
      observer.complete();
    });
  }
}

@Injectable({
  providedIn: 'root',
})
export class MyService {
  constructor(private otherService: OtherService) {}

  processData(): Observable<any> {
    return this.otherService.fetchData();
  }
}
```

---

### **Unit Test**

Here’s the test case for testing the `processData` function:

```typescript
import { TestBed } from '@angular/core/testing';
import { of } from 'rxjs';
import { MyService } from './my.service';
import { OtherService } from './other.service';

describe('MyService', () => {
  let myService: MyService;
  let otherServiceSpy: jasmine.SpyObj<OtherService>;

  beforeEach(() => {
    // Create a spy object for OtherService
    otherServiceSpy = jasmine.createSpyObj('OtherService', ['fetchData']);

    TestBed.configureTestingModule({
      providers: [
        MyService,
        { provide: OtherService, useValue: otherServiceSpy },
      ],
    });

    myService = TestBed.inject(MyService);
  });

  it('should call fetchData from OtherService and return its result', (done: DoneFn) => {
    const mockResponse = { data: 'Sample Data' };

    // Mock fetchData to return an observable with mockResponse
    otherServiceSpy.fetchData.and.returnValue(of(mockResponse));

    // Call processData and verify the result
    myService.processData().subscribe({
      next: (response) => {
        expect(response).toEqual(mockResponse);
        expect(otherServiceSpy.fetchData).toHaveBeenCalledTimes(1);
        done();
      },
      error: () => {
        fail('Expected success, but error was called');
      },
    });
  });
});
```

---


## Unit testing of HttpClient by mocking HttpTestingController

Here’s how you can write a unit test for an HTTP GET request using Jasmine's `createSpy` method, covering both success and error scenarios.

---

### **Service Code**

Here is a sample service with an HTTP GET request:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class MyService {
  private apiUrl = 'https://api.example.com/data';

  constructor(private http: HttpClient) {}

  getData(): Observable<any> {
    return this.http.get(this.apiUrl);
  }
}
```

---

### **Unit Test**

Here’s the test case for testing the `getData` method with both success and error responses:

```typescript
import { TestBed } from '@angular/core/testing';
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
import { MyService } from './my.service';

describe('MyService', () => {
  let service: MyService;
  let httpMock: HttpTestingController;
  let successSpy: jasmine.Spy;
  let errorSpy: jasmine.Spy;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [MyService],
    });

    service = TestBed.inject(MyService);
    httpMock = TestBed.inject(HttpTestingController);
    successSpy = jasmine.createSpy('successSpy');
    errorSpy = jasmine.createSpy('errorSpy');
  });

  afterEach(() => {
    httpMock.verify(); // Ensures no outstanding requests
  });

  it('should handle a successful HTTP GET request', () => {
    const mockResponse = { id: 1, name: 'Test Data' }; // Mock response

    service.getData().subscribe(successSpy, errorSpy);

    // Expect an HTTP GET request
    const req = httpMock.expectOne('https://api.example.com/data');
    expect(req.request.method).toBe('GET');

    // Respond with mock data
    req.flush(mockResponse);

    // Verify the success callback was called with the correct response
    expect(successSpy).toHaveBeenCalledWith(mockResponse);
    expect(errorSpy).not.toHaveBeenCalled();
  });

  it('should handle an error HTTP GET request', () => {
    const mockError = { status: 500, statusText: 'Internal Server Error' };

    service.getData().subscribe(successSpy, errorSpy);

    // Expect an HTTP GET request
    const req = httpMock.expectOne('https://api.example.com/data');
    expect(req.request.method).toBe('GET');

    // Respond with an error
    req.flush(null, mockError);

    // Verify the error callback was called
    expect(successSpy).not.toHaveBeenCalled();
    expect(errorSpy).toHaveBeenCalled();
  });
});
```

---

### **Explanation**

1. **Setup and Mocks**:
   - `HttpClientTestingModule`: Allows you to mock HTTP requests.
   - `HttpTestingController`: Provides methods to control and verify HTTP requests.

2. **Spies**:
   - `createSpy`: Creates mock functions (`successSpy`, `errorSpy`) to verify success and error callbacks.

3. **Success Test**:
   - Simulate an HTTP GET request using `httpMock.expectOne()`.
   - Respond with mock data using `req.flush()`.
   - Verify the success spy is called with the mock response and the error spy is not called.

4. **Error Test**:
   - Respond with an error using `req.flush(null, mockError)`.
   - Verify the error spy is called, and the success spy is not called.

5. **`httpMock.verify()`**:
   - Ensures there are no outstanding HTTP requests at the end of each test.

---

## Direct mock HttpClient without HttpTestingController

If you don't want to use `HttpTestingController`, you can directly mock the HTTP client using Jasmine's `createSpy` method. Here's how you can test the `getData` method by mocking the `HttpClient`:

---

### **Unit Test**

Here’s the test case using `createSpy` for mocking:

```typescript
import { TestBed } from '@angular/core/testing';
import { HttpClient } from '@angular/common/http';
import { of, throwError } from 'rxjs';
import { MyService } from './my.service';

describe('MyService', () => {
  let service: MyService;
  let httpClientSpy: jasmine.SpyObj<HttpClient>;

  beforeEach(() => {
    // Create a spy object for HttpClient
    httpClientSpy = jasmine.createSpyObj('HttpClient', ['get']);

    TestBed.configureTestingModule({
      providers: [
        MyService,
        { provide: HttpClient, useValue: httpClientSpy },
      ],
    });

    service = TestBed.inject(MyService);
  });

  it('should handle a successful HTTP GET request', (done: DoneFn) => {
    const mockResponse = { id: 1, name: 'Test Data' }; // Mock response

    // Mock the HttpClient.get method to return an observable of mockResponse
    httpClientSpy.get.and.returnValue(of(mockResponse));

    service.getData().subscribe({
      next: (response) => {
        expect(response).toEqual(mockResponse);
        done();
      },
      error: () => {
        fail('Expected success, but error was called');
      },
    });

    // Verify the get method was called with the correct URL
    expect(httpClientSpy.get).toHaveBeenCalledWith('https://api.example.com/data');
  });
});
```

---

### **Explanation**

1. **Mocking `HttpClient`**:
   - A spy object for `HttpClient` is created using `jasmine.createSpyObj`.
   - The `get` method of `HttpClient` is mocked.

2. **Mocking Responses**:
   - For success, `httpClientSpy.get.and.returnValue(of(mockResponse))` returns an observable with mock data.
   - For errors, `httpClientSpy.get.and.returnValue(throwError(() => mockError))` simulates an HTTP error.

3. **Assertions**:
   - Ensure the `get` method is called with the correct URL.
   - For success, verify the response matches the mock response.
   - For errors, verify the error matches the mock error.

4. **`DoneFn`**:
   - Use the `done` callback to signal the end of asynchronous tests, ensuring the test completes correctly.

---

# **`fakeAsync` vs. `async` in Jasmine**

1. **`fakeAsync`**:
   - Use when testing code that involves **timing functions** like `setTimeout`, `setInterval`, or promises.
   - Allows manual control over asynchronous operations using utilities like `tick()` or `flush()` to simulate time progression.
   - Best suited for scenarios where you want to test time-based behaviors deterministically.

   **Example**:
   ```typescript
   it('should handle setTimeout with fakeAsync', fakeAsync(() => {
     let value = false;
     setTimeout(() => {
       value = true;
     }, 1000);
     tick(1000); // Simulate 1 second passing
     expect(value).toBe(true);
   }));
   ```

2. **`async`**:
   - Use for testing **asynchronous operations** like promises, observables, or Angular's event loops.
   - Automatically waits for all async operations to complete, simplifying tests for components or services.
   - Best for scenarios involving Angular's lifecycle hooks or observables.

   **Example**:
   ```typescript
   it('should handle promises with async', async () => {
     let value = false;
     await Promise.resolve().then(() => {
       value = true;
     });
     expect(value).toBe(true);
   });
   ```

---

### **When to Use Which?**
- **Use `fakeAsync`**: For **precise control over time-based async operations** (e.g., `setTimeout`, `setInterval`).
- **Use `async`**: For testing **Angular components and services** where the test should naturally wait for asynchronous code to complete.

------

# **How `tick()` Works**

The `tick()` method in Jasmine's `fakeAsync` zone **does not actually wait for real time to pass**—it is a mock that simulates the passage of time instantly. This allows you to test time-based behaviors deterministically without delays.

- `tick(milliseconds)` advances the virtual clock by the specified amount of time (in milliseconds).
- All timers (e.g., `setTimeout`, `setInterval`) scheduled within the `fakeAsync` zone will execute synchronously when the virtual clock reaches their scheduled time.
- `tick()` is **instantaneous** and does not introduce real delays in tests.
- It helps simulate time-based functionality quickly and predictably, making tests faster and more reliable.

---


# **Router testing**
---

### **Unit Test Using `spyOn`**

In this test, we will use `spyOn` to track the call to `navigateByUrl` on the `Router` instance:

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { HomeComponent } from './home.component';

describe('HomeComponent', () => {
  let component: HomeComponent;
  let fixture: ComponentFixture<HomeComponent>;
  let router: Router;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [HomeComponent],
      providers: [Router],  // Provide the actual Router service
    });

    fixture = TestBed.createComponent(HomeComponent);
    component = fixture.componentInstance;
    router = TestBed.inject(Router);  // Inject the Router service
  });

  it('should navigate to details page on button click', () => {
    // Spy on the navigateByUrl method of the Router service
    const navigateSpy = spyOn(router, 'navigateByUrl');

    fixture.detectChanges(); // Trigger change detection

    // Find the button and trigger a click event
    const button = fixture.nativeElement.querySelector('button');
    button.click();

    // Check that navigateByUrl was called with the correct URL
    expect(navigateSpy).toHaveBeenCalledWith('/details');
  });
});
```

---

### **Unit Test Using `createSpyObj`**


In your unit test, you'll mock the `Router`'s `navigateByUrl` method to check if it's called correctly.

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { HomeComponent } from './home.component';

describe('HomeComponent', () => {
  let component: HomeComponent;
  let fixture: ComponentFixture<HomeComponent>;
  let routerSpy: jasmine.SpyObj<Router>;

  beforeEach(() => {
    // Create a spy for Router
    routerSpy = jasmine.createSpyObj('Router', ['navigateByUrl']);

    TestBed.configureTestingModule({
      declarations: [HomeComponent],
      providers: [
        { provide: Router, useValue: routerSpy },
      ],
    });

    fixture = TestBed.createComponent(HomeComponent);
    component = fixture.componentInstance;
  });

  it('should navigate to details page on button click', () => {
    fixture.detectChanges(); // Trigger change detection

    // Find the button and trigger a click event
    const button = fixture.nativeElement.querySelector('button');
    button.click();

    // Check that navigateByUrl was called with the correct URL
    expect(routerSpy.navigateByUrl).toHaveBeenCalledWith('/details');
  });
});
```

---

# **Lifecycle of Unit Testing in Angular**

1. **Test Setup**:  
   - The testing module is configured using `TestBed`.  
   - Dependencies like components, services, modules, or mocks are declared and provided.

2. **Component/Service Creation**:  
   - Instances of components, services, or directives are created using `TestBed`.  
   - The created instance is used to test functionality in isolation.

3. **Fixture Initialization**:  
   - A `ComponentFixture` is set up to access and interact with the DOM or component instance.  
   - Change detection is run to reflect initial component bindings.

4. **Execution of Tests**:  
   - Individual test cases (`it` blocks) are executed.  
   - Test cases check specific functionality, such as method calls, UI behavior, or service interactions.

5. **Handling Asynchronous Code**:  
   - Tests handle async operations using utilities like `fakeAsync`, `tick`, or `async` for controlling and waiting for time-based events or promises.

6. **Assertions**:  
   - Expectations (`expect` statements) are used to verify the component or service behavior matches the expected results.

7. **Teardown**:  
   - After all test cases, Angular automatically destroys components and cleans up the testing module environment.  
   - This prevents memory leaks and ensures test isolation for subsequent test runs.

---

### When `ngOnInit` and `ngAfterViewInit` called?


### **1. `ngOnInit` Execution**
- **When**: Executed automatically when the component instance is initialized and change detection is triggered.
- **How**: When `TestBed.createComponent()` is called, the component is instantiated, but `ngOnInit` only runs after calling `fixture.detectChanges()` for the first time.
- **Purpose**: Used for component initialization, such as fetching data or setting up state.

---

### **2. `ngAfterViewInit` Execution**
- **When**: Executed after Angular has fully initialized the component's view and child views.
- **How**: Automatically triggered after the first `fixture.detectChanges()` if the component has views or child components.
- **Purpose**: Used for DOM manipulations or interacting with child components.

---

# **`CUSTOM_ELEMENTS_SCHEMA` and `NO_ERRORS_SCHEMA` in Angular**


### **1. Custom Error Schema**
- **Definition**: A custom schema allows you to define specific elements, attributes, or properties that Angular should consider valid, even if they are not part of the standard Angular schema.
- **Purpose**: Useful for integrating third-party libraries or custom elements like web components that Angular doesn't recognize by default.

---

### **2. No Error Schema (`NO_ERRORS_SCHEMA`)**
- **Definition**: A predefined schema that tells Angular to ignore all template validation errors for unknown elements or attributes.
- **Purpose**: Useful for scenarios where template validation errors are unnecessary or would block development, such as rapid prototyping or working with legacy codebases.
- **Behavior**:
  - Disables validation for unknown HTML elements, attributes, and properties in templates.

---

### **Differences Between the Two**
| Aspect                 | Custom Error Schema (`CUSTOM_ELEMENTS_SCHEMA`)           | No Error Schema (`NO_ERRORS_SCHEMA`)           |
|------------------------|---------------------------------------------------------|-----------------------------------------------|
| **Validation Behavior**| Allows specific unknown elements (e.g., custom elements)| Ignores all unknown elements and attributes    |
| **Use Case**           | For defined custom or third-party components            | For ignoring all schema-related validation    |
| **Flexibility**        | Limited to custom elements                              | Complete bypass of schema validation          |
| **Risk Level**         | Lower risk (specific to certain elements)               | Higher risk (all validation skipped)          |

---

# **Best Practices for Writing Unit Tests**

1. **Follow the AAA Pattern**: Structure tests into Arrange, Act, and Assert phases for clarity.  
2. **Write Independent Tests**: Ensure tests don't rely on the state or outcome of other tests.  
3. **Test One Thing at a Time**: Focus each test case on a single functionality or behavior.  
4. **Use Mocks and Spies**: Replace real dependencies with mocks or spies for isolation.  
5. **Cover Edge Cases**: Test for all scenarios, including valid, invalid, and boundary conditions.  
6. **Avoid Testing Implementation Details**: Test functionality and outputs, not internal code.  
7. **Use Meaningful Test Names**: Name test cases descriptively to reflect their purpose.  
8. **Mock External Dependencies**: Simulate external systems like APIs and databases.  
9. **Keep Tests Small and Focused**: Write concise tests targeting specific functionality.  
10. **Use Test Setup Hooks**: Leverage `beforeEach` and `afterEach` for shared setup/cleanup.  
11. **Maintain High Coverage, but Don’t Obsess**: Prioritize meaningful tests over arbitrary coverage.  
12. **Use Assertions Effectively**: Ensure assertions directly validate the test’s purpose.  
13. **Avoid Overuse of `NO_ERRORS_SCHEMA`**: Define proper schemas instead of bypassing validation.  
14. **Write Tests for Bugs**: Add tests for bugs before fixing them to prevent regressions.  
15. **Regularly Refactor Tests**: Improve test maintainability by eliminating redundant logic.  
16. **Leverage Angular Testing Utilities**: Use tools like `TestBed` and `fixture.detectChanges`.  
17. **Clean Up After Tests**: Ensure no side effects or subscriptions remain post-tests.  
18. **Integrate Tests with CI/CD**: Automate test execution in your CI/CD pipeline.  
19. **Avoid Hardcoding Values**: Use constants or setup variables for reusable data.  
20. **Document Complex Tests**: Add comments to explain intricate setups or scenarios.  