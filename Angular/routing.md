# **Angular Router Lifecycle**

The Angular Router lifecycle describes the sequence of events and hooks that are triggered during navigation, allowing developers to manage behaviors such as preloading data, guarding routes, and cleaning up resources. Here’s a detailed explanation:

---

### **1. Navigation Start**
- **Triggered**: When navigation begins.
- **Purpose**: Marks the start of a route change process.
- **Event**: `NavigationStart`
- **Use Case**: Show a loading indicator or track navigation metrics.

---

### **2. Route Recognized**
- **Triggered**: After the URL is parsed and matched against configured routes.
- **Event**: `RoutesRecognized`
- **Use Case**: Access the resolved routes and their parameters.

---

### **3. Guards Check**
- **Triggered**: Before activating a route or deactivating a current one.
- **Hooks**:
  - **`canActivate`**: Determines if the route can be activated.
  - **`canActivateChild`**: Checks if child routes can be activated.
  - **`canDeactivate`**: Ensures the current route can be exited.
  - **`canLoad`**: Determines if a lazy-loaded module can be loaded.
- **Use Case**: Control access to routes based on authentication, authorization, or unsaved changes.

---

### **4. Resolve Guards**
- **Triggered**: Once route guards pass.
- **Hook**: `resolve`
- **Purpose**: Fetch data required by the route before it activates.
- **Use Case**: Preload data to ensure the component has the necessary information.

---

### **5. Activation**
- **Triggered**: When the new component and its child components are initialized.
- **Hooks**:
  - **`ngOnInit`**: Called for initializing the component.
  - **`ngAfterViewInit`**: Called after the view and child views are fully initialized.
- **Use Case**: Set up component logic, bind data, or fetch additional data.

---

### **6. Navigation End**
- **Triggered**: After successful navigation to the new route.
- **Event**: `NavigationEnd`
- **Use Case**: Hide loading indicators, track successful navigation.

---

### **7. Navigation Cancel/Errors (Optional)**
- **Triggered**: If navigation is canceled or encounters an error.
- **Events**:
  - **`NavigationCancel`**: If a guard prevents navigation or the user cancels it.
  - **`NavigationError`**: If an error occurs during navigation.
- **Use Case**: Handle navigation errors or display error messages.

------

# Lifecycle methods and events of Angular Router

---

### **1. `canActivate` - Prevent Route Activation**
```typescript
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';

@Injectable({
  providedIn: 'root',
})
export class AuthGuard implements CanActivate {
  constructor(private router: Router) {}

  canActivate(): boolean {
    const isLoggedIn = !!localStorage.getItem('token');
    if (!isLoggedIn) {
      this.router.navigate(['/login']);
      return false;
    }
    return true;
  }
}
```

---

### **2. `canActivateChild` - Guard Child Routes**
```typescript
import { Injectable } from '@angular/core';
import { CanActivateChild } from '@angular/router';

@Injectable({
  providedIn: 'root',
})
export class ChildGuard implements CanActivateChild {
  canActivateChild(): boolean {
    console.log('Child route guard executed.');
    return true;
  }
}
```

---

### **3. `canDeactivate` - Prevent Navigation Away**
```typescript
import { Injectable } from '@angular/core';
import { CanDeactivate } from '@angular/router';
import { Observable } from 'rxjs';

export interface CanComponentDeactivate {
  canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean;
}

@Injectable({
  providedIn: 'root',
})
export class DeactivateGuard implements CanDeactivate<CanComponentDeactivate> {
  canDeactivate(component: CanComponentDeactivate): boolean {
    return component.canDeactivate ? component.canDeactivate() : true;
  }
}
```

---

### **4. `canLoad` - Control Lazy Loading**
```typescript
import { Injectable } from '@angular/core';
import { CanLoad, Route, UrlSegment, Router } from '@angular/router';

@Injectable({
  providedIn: 'root',
})
export class CanLoadGuard implements CanLoad {
  constructor(private router: Router) {}

  canLoad(route: Route, segments: UrlSegment[]): boolean {
    const hasPermission = true; // Add your logic
    if (!hasPermission) {
      this.router.navigate(['/not-authorized']);
      return false;
    }
    return true;
  }
}
```

---

### **5. `resolve` - Preload Data**
```typescript
import { Injectable } from '@angular/core';
import { Resolve } from '@angular/router';
import { Observable, of } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class DataResolver implements Resolve<any> {
  resolve(): Observable<any> {
    return of({ data: 'Resolved Data' }); // Simulate API call
  }
}
```

---

### **6. Handling Router Events**
```typescript
import { Component, OnInit } from '@angular/core';
import { Router, NavigationStart, NavigationEnd, NavigationCancel, NavigationError } from '@angular/router';

@Component({
  selector: 'app-root',
  template: `<router-outlet></router-outlet>`,
})
export class AppComponent implements OnInit {
  constructor(private router: Router) {}

  ngOnInit(): void {
    this.router.events.subscribe((event) => {
      if (event instanceof NavigationStart) {
        console.log('Navigation started.');
      } else if (event instanceof NavigationEnd) {
        console.log('Navigation ended.');
      } else if (event instanceof NavigationCancel) {
        console.log('Navigation canceled.');
      } else if (event instanceof NavigationError) {
        console.error('Navigation error:', event.error);
      }
    });
  }
}
```

---

### **7. Adding Guards and Resolver in Routes**
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AuthGuard } from './guards/auth.guard';
import { ChildGuard } from './guards/child.guard';
import { DeactivateGuard } from './guards/deactivate.guard';
import { CanLoadGuard } from './guards/can-load.guard';
import { DataResolver } from './resolvers/data.resolver';
import { HomeComponent } from './home/home.component';
import { LoginComponent } from './login/login.component';

const routes: Routes = [
  { path: 'login', component: LoginComponent },
  {
    path: 'home',
    component: HomeComponent,
    canActivate: [AuthGuard],
    canActivateChild: [ChildGuard],
    canDeactivate: [DeactivateGuard],
    resolve: { data: DataResolver },
  },
  {
    path: 'lazy',
    loadChildren: () => import('./lazy/lazy.module').then((m) => m.LazyModule),
    canLoad: [CanLoadGuard],
  },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

---

### **8. Implementing `canDeactivate` in a Component**
```typescript
import { Component } from '@angular/core';
import { CanComponentDeactivate } from './guards/deactivate.guard';

@Component({
  selector: 'app-unsaved',
  template: `<button (click)="save()">Save</button>`,
})
export class UnsavedComponent implements CanComponentDeactivate {
  unsavedChanges = true;

  canDeactivate(): boolean {
    if (this.unsavedChanges) {
      return confirm('You have unsaved changes. Do you want to leave?');
    }
    return true;
  }

  save(): void {
    this.unsavedChanges = false;
  }
}
```
-----------------

# **Adding Routes Dynamically and Managing Redirections**

In Angular, routes can be added dynamically and managed for redirection with or without parameters and query parameters. Here's how you can achieve these functionalities.

---

### **1. Add Routes Dynamically**
Angular allows you to add routes to the `Router` configuration at runtime using the `resetConfig` method.

```typescript
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-dynamic-route',
  template: `<button (click)="addDynamicRoute()">Add Route</button>`,
})
export class DynamicRouteComponent {
  constructor(private router: Router) {}

  addDynamicRoute() {
    const dynamicRoutes = [
      { path: 'dynamic', component: DynamicComponent },
    ];

    const currentRoutes = this.router.config;
    this.router.resetConfig([...currentRoutes, ...dynamicRoutes]);

    console.log('Route added:', dynamicRoutes);
  }
}
```

---

### **2. Redirect to Routes with Parameters**

#### **Without Parameters**
```typescript
this.router.navigate(['/home']);
```

#### **With Path Parameters**
```typescript
this.router.navigate(['/user', 123]); // Example: Navigates to '/user/123'
```

Define the route in the routing module:
```typescript
{ path: 'user/:id', component: UserComponent },
```

In the component:
```typescript
import { ActivatedRoute } from '@angular/router';

constructor(private route: ActivatedRoute) {}

ngOnInit() {
  const userId = this.route.snapshot.paramMap.get('id');
  console.log('User ID:', userId);
}
```

---

### **3. Redirect with Query Parameters**

#### **Navigating with Query Parameters**
```typescript
this.router.navigate(['/search'], { queryParams: { q: 'angular', page: 2 } });
```

Define the route:
```typescript
{ path: 'search', component: SearchComponent },
```

In the component:
```typescript
import { ActivatedRoute } from '@angular/router';

constructor(private route: ActivatedRoute) {}

ngOnInit() {
  this.route.queryParams.subscribe((params) => {
    console.log('Query Params:', params);
  });
}
```

#### **Setting Query Params Programmatically**
To merge or update query parameters:
```typescript
this.router.navigate([], {
  relativeTo: this.route,
  queryParams: { sort: 'desc' },
  queryParamsHandling: 'merge', // Merges with existing query params
});
```

---

### **4. Redirect with Optional Parameters**

#### **Define a Route with Optional Parameters**
```typescript
{ path: 'product/:id/:name?', component: ProductComponent },
```

#### **Navigate with Optional Parameters**
```typescript
this.router.navigate(['/product', 101, 'Laptop']); // Pass both parameters
this.router.navigate(['/product', 101]); // Pass only required parameter
```

In the component:
```typescript
ngOnInit() {
  const id = this.route.snapshot.paramMap.get('id');
  const name = this.route.snapshot.paramMap.get('name');
  console.log('Product ID:', id, 'Product Name:', name || 'Not provided');
}
```

---

### **5. Redirect to a Route Based on Conditions**

#### **Example: Redirecting in Guards**
In a guard, you can conditionally redirect:
```typescript
canActivate(): boolean {
  const isAuthenticated = this.authService.isAuthenticated();
  if (!isAuthenticated) {
    this.router.navigate(['/login']);
    return false;
  }
  return true;
}
```

#### **Example: Redirecting on Component Logic**
```typescript
ngOnInit() {
  if (!this.isAuthorized) {
    this.router.navigate(['/unauthorized']);
  }
}
```

---

### **6. Using `routerLink` for Navigation in Templates**

#### **Without Parameters**
```html
<a routerLink="/home">Home</a>
```

#### **With Path Parameters**
```html
<a [routerLink]="['/user', 123]">User Profile</a>
```

#### **With Query Parameters**
```html
<a [routerLink]="['/search']" [queryParams]="{ q: 'angular', page: 2 }">Search</a>
```

---

### **7. Redirect Routes in Routing Module**

#### **Static Redirect**
```typescript
{ path: '', redirectTo: '/home', pathMatch: 'full' },
```

---

# **Pass sensitive data with Route Params**

To pass parameters with a route without showing them in the URL, Angular provides a mechanism called **route state navigation** using the `state` property in the `Router`. Here's how it works:

---

### **Passing Params with Route (Not Shown in URL)**

Use the `state` property of the `navigate` method to pass parameters.

#### **Example: Passing Parameters**
```typescript
this.router.navigate(['/target-route'], {
  state: { data: { id: 101, name: 'Angular' } },
});
```

In this case, the route `/target-route` will not display `id` or `name` in the URL, as these are passed in the route state.

---

### **Getting Params Passed in Route State**

You can retrieve the `state` parameters using the `getCurrentNavigation` method of the `Router`.

#### **Example: Retrieving Parameters**
```typescript
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-target',
  template: `<p>Target Route</p>`,
})
export class TargetComponent implements OnInit {
  constructor(private router: Router) {}

  ngOnInit(): void {
    const navigation = this.router.getCurrentNavigation();
    if (navigation?.extras.state) {
      const data = navigation.extras.state.data;
      console.log('Received Data:', data); // { id: 101, name: 'Angular' }
    }
  }
}
```

---

### **Key Points**

1. **Why Use `state`?**
   - Keeps sensitive or complex data hidden from the URL.
   - Avoids exposing unnecessary information.
   - Reduces the need for long query strings or path parameters.

2. **When to Use?**
   - Passing non-serializable data like objects, arrays, or large sets of data.
   - Avoiding clutter in URLs for better readability.

3. **Limitations**:
   - `state` parameters are not persisted across refreshes or browser reloads because the state is only available during navigation.

---

# **Router Module important configs**

The `RouterModule` in Angular is a core module that provides navigation and routing functionalities for single-page applications (SPAs). It enables the configuration of application routes, navigation between views, and additional features like guards, resolvers, lazy loading, and more.

---

### **1. Importing and Configuring RouterModule**

To use routing in an Angular application, the `RouterModule` must be imported and configured in the app's root or feature modules.

#### **Basic Setup**
```typescript

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

---

### **2. Key Configurations of `RouterModule.forRoot()`**

#### **a. Routes**
Defines the array of route configurations for the application.
```typescript
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
];
```

---

#### **b. `enableTracing`**
Enables detailed logging of all router events for debugging purposes.
```typescript
RouterModule.forRoot(routes, { enableTracing: true });
```

---

#### **c. `useHash`**
Adds a `#` (hash) to URLs to enable older browsers or servers to handle routing correctly.
```typescript
RouterModule.forRoot(routes, { useHash: true });
```

---

#### **d. `onSameUrlNavigation`**
Specifies how the router should handle navigation to the same URL:
- `reload`: Reload the route and trigger lifecycle hooks.
- `ignore`: Do nothing if the URL is the same.

```typescript
RouterModule.forRoot(routes, { onSameUrlNavigation: 'reload' });
```

---

#### **e. `scrollPositionRestoration`**
Restores the scroll position after navigation:
- `disabled`: No restoration.
- `enabled`: Restores the scroll position.
- `top`: Scrolls to the top of the page.

```typescript
RouterModule.forRoot(routes, { scrollPositionRestoration: 'enabled' });
```

---

#### **h. `errorHandler`**
Specifies a custom error handler for navigation errors.
```typescript
RouterModule.forRoot(routes, { errorHandler: (error) => console.error(error) });
```

---

#### **i. `urlUpdateStrategy`**
Determines when the browser's URL is updated:
- `deferred`: Updates after navigation is confirmed (default).
- `eager`: Updates immediately.

```typescript
RouterModule.forRoot(routes, { urlUpdateStrategy: 'eager' });
```

---

#### **j. `preloadingStrategy`**
Configures how lazy-loaded modules are preloaded:
- `PreloadAllModules`: Preloads all modules.
- Custom preloading strategies can also be implemented.

```typescript
import { PreloadAllModules } from '@angular/router';

RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules });
```

---

### **3. Route Configuration Properties**

Each route object in the `Routes` array can have several properties:

#### **a. `path`**
Defines the URL segment for the route.

#### **b. `component`**
Specifies the component to render for the route.

#### **c. `redirectTo`**
Redirects to another route.
```typescript
{ path: '', redirectTo: '/home', pathMatch: 'full' }
```

#### **d. `pathMatch`**
Determines how the path is matched:
- `full`: Matches the entire URL.
- `prefix`: Matches partially.

#### **e. `children`**
Defines child routes for nested navigation.

#### **f. `outlet`**
Specifies the named outlet for the route.

#### **g. `resolve`**
Loads data before navigating to the route using a resolver.

#### **h. `canActivate`, `canDeactivate`, `canLoad`**
Guards to control access to the route.

#### **i. `data`**
Passes custom data to the route.

---

### **4. Advanced Features**

#### **Lazy Loading**
Load feature modules dynamically to improve performance.
```typescript
{ path: 'admin', loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule) }
```

#### **Auxiliary Routes (Named Outlets)**
Define and navigate to multiple routes simultaneously using outlets.
Auxiliary routes allow multiple outlets in a single view.

Example:
```html
<router-outlet></router-outlet>
<router-outlet name="aux"></router-outlet>
```
```typescript
this.router.navigate(['/main', { outlets: { aux: 'settings' } }]);
```

---

## **Lazy Loading**

Lazy loading is an Angular feature that allows you to load modules or components on demand instead of including them in the main application bundle. This improves the performance and loading speed of your application, especially for large-scale apps.

---

## **1. Lazy Loading Modules**

Lazy loading modules defers the loading of certain parts of your application until they are needed, such as when a user navigates to a specific route.

### **Steps to Implement Lazy Loading for Modules**

#### **a. Create a Feature Module**
```bash
ng generate module feature --route=feature --module=app.module
```

#### **b. Define Routes in the Feature Module**
In `feature-routing.module.ts`:
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { FeatureComponent } from './feature.component';

const routes: Routes = [
  { path: '', component: FeatureComponent },
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule],
})
export class FeatureRoutingModule {}
```

#### **c. Load Module Lazily in the Root Router**
In `app-routing.module.ts`:
```typescript
const routes: Routes = [
  { path: 'feature', loadChildren: () => import('./feature/feature.module').then(m => m.FeatureModule) },
];
```

---

## **2. Lazy Loading Components**

Angular doesn’t provide native support for lazy loading individual components, but you can achieve this using dynamic imports.

### **Steps to Lazy Load a Component**

#### **a. Use `loadComponent` with Angular Standalone Components**
Angular 14+ introduced standalone components, enabling lazy loading at the component level.

In `app-routing.module.ts`:
```typescript
const routes: Routes = [
  {
    path: 'lazy-component',
    loadComponent: () => import('./lazy/lazy.component').then(m => m.LazyComponent),
  },
];
```

This approach is highly efficient for standalone components but is generally less commonly used than lazy loading entire modules.

---

## **3. Preloading Strategies**

Preloading strategies allow you to improve performance by preloading certain modules in the background while keeping others lazy-loaded.

### **Built-in Strategies**

#### **a. `NoPreloading` (Default)**
Does not preload any modules.
```typescript
RouterModule.forRoot(routes, { preloadingStrategy: NoPreloading });
```

#### **b. `PreloadAllModules`**
Preloads all lazy-loaded modules after the initial load.
```typescript
RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules });
```

---

## **Custom Preloading Strategy**

You can create your own preloading strategy for finer control.

#### **Steps to Create a Custom Preloading Strategy**

1. **Create the Strategy:**
```typescript
import { Injectable } from '@angular/core';
import { Route, PreloadingStrategy } from '@angular/router';
import { Observable, of } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class CustomPreloadingStrategy implements PreloadingStrategy {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    return route.data && route.data['preload'] ? load() : of(null);
  }
}
```

2. **Update Route Configurations:**
```typescript
const routes: Routes = [
  { path: 'feature', loadChildren: () => import('./feature/feature.module').then(m => m.FeatureModule), data: { preload: true } },
];
```

3. **Provide the Strategy in `RouterModule`:**
```typescript
RouterModule.forRoot(routes, { preloadingStrategy: CustomPreloadingStrategy });
```


---
