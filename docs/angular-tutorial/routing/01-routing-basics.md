# Angular Routing - Complete Guide

> Timestamps: **2:45:19** - router-outlet, routerLink & routerLinkActive | **3:07:25** - navigate() & navigateByUrl()

## Table of Contents
1. [Introduction](#introduction)
2. [Basic Routing Setup](#basic-routing-setup)
3. [Router Outlet](#router-outlet)
4. [RouterLink & RouterLinkActive](#routerlink--routerlinkactive)
5. [Programmatic Navigation](#programmatic-navigation)
6. [Route Parameters](#route-parameters)
7. [Child Routes](#child-routes)
8. [Route Guards](#route-guards)
9. [Complete Example](#complete-example)

---

## Introduction

Angular's Router enables navigation between different views/components in your application. It provides:
- Client-side navigation without page reloads
- URL management and browser history
- Route parameters and query parameters
- Navigation guards for protecting routes
- Lazy loading for performance optimization

---

## Basic Routing Setup

### 1. Standalone Components Routing (Modern Approach)

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes)]
};

// app.routes.ts
import { Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';
import { ContactComponent } from './contact/contact.component';

export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent },
  { path: '**', redirectTo: '' }
];

// main.ts
import { bootstrapApplication } from '@angular/platform-browser';
import { appConfig } from './app/app.config';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, appConfig);
```

---

## Router Outlet

The `<router-outlet>` directive marks where the router should display views.

```typescript
// app.component.ts
import { Component } from '@angular/core';
import { RouterOutlet, RouterLink, RouterLinkActive } from '@angular/router';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RouterLink, RouterLinkActive],
  template: `
    <div class="app-container">
      <header>
        <h1>My Angular App</h1>
        <nav>
          <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">Home</a>
          <a routerLink="/about" routerLinkActive="active">About</a>
          <a routerLink="/contact" routerLinkActive="active">Contact</a>
        </nav>
      </header>
      
      <main>
        <!-- Component views will be displayed here -->
        <router-outlet></router-outlet>
      </main>
      
      <footer>
        <p>&copy; 2024 My App</p>
      </footer>
    </div>
  `,
  styles: [`
    .app-container {
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }
    
    header {
      background: #1976d2;
      color: white;
      padding: 1rem;
    }
    
    nav {
      display: flex;
      gap: 1rem;
      margin-top: 1rem;
    }
    
    nav a {
      color: white;
      text-decoration: none;
      padding: 0.5rem 1rem;
      border-radius: 4px;
    }
    
    nav a:hover {
      background: rgba(255,255,255,0.1);
    }
    
    nav a.active {
      background: rgba(255,255,255,0.2);
      font-weight: bold;
    }
    
    main {
      flex: 1;
      padding: 2rem;
    }
    
    footer {
      background: #f5f5f5;
      padding: 1rem;
      text-align: center;
    }
  `]
})
export class AppComponent { }
```

---

## RouterLink & RouterLinkActive

### routerLink Directive

```typescript
import { Component } from '@angular/core';
import { RouterLink } from '@angular/router';

@Component({
  selector: 'app-home',
  standalone: true,
  imports: [RouterLink],
  template: `
    <div class="home">
      <h2>Welcome Home!</h2>
      
      <!-- Simple string path -->
      <a routerLink="/about">About Us</a>
      
      <!-- Array syntax for complex paths -->
      <a [routerLink]="['/products', productId]">View Product</a>
      
      <!-- With query parameters -->
      <a 
        [routerLink]="['/search']" 
        [queryParams]="{q: 'angular', category: 'tutorial'}"
      >
        Search Angular Tutorials
      </a>
    </div>
  `
})
export class HomeComponent {
  productId = 123;
}
```

### routerLinkActive Directive

Automatically adds CSS classes to active routes.

```typescript
@Component({
  selector: 'app-navigation',
  standalone: true,
  imports: [RouterLink, RouterLinkActive],
  template: `
    <nav class="navbar">
      <a 
        routerLink="/" 
        routerLinkActive="active"
        [routerLinkActiveOptions]="{exact: true}"
      >
        Home
      </a>
      
      <a routerLink="/products" routerLinkActive="active">Products</a>
      <a routerLink="/about" routerLinkActive="active">About</a>
    </nav>
  `,
  styles: [`
    .navbar a.active {
      background: #1976d2;
      font-weight: bold;
    }
  `]
})
export class NavigationComponent { }
```

---

## Programmatic Navigation

### Using Router Service

```typescript
import { Component, inject } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-products',
  standalone: true,
  template: `
    <div class="products">
      <h2>Products</h2>
      <button (click)="viewProduct(1)">View Product 1</button>
      <button (click)="goToHome()">Back to Home</button>
      <button (click)="goToSearch()">Search Products</button>
    </div>
  `
})
export class ProductsComponent {
  private router = inject(Router);
  
  // navigate() - relative navigation, supports arrays
  viewProduct(id: number): void {
    this.router.navigate(['/products', id]);
  }
  
  // navigateByUrl() - absolute navigation with string
  goToHome(): void {
    this.router.navigateByUrl('/');
  }
  
  // With query parameters
  goToSearch(): void {
    this.router.navigate(['/search'], {
      queryParams: { category: 'electronics', sort: 'price' }
    });
  }
}
```

### navigate() vs navigateByUrl()

| Method | Use Case | Example |
|--------|----------|---------|
| `navigate()` | Array syntax, relative paths, NavigationExtras | `router.navigate(['products', id])` |
| `navigateByUrl()` | Absolute URL string, simpler and faster | `router.navigateByUrl('/products/123')` |

---

## Route Parameters

### Accessing Parameters

```typescript
import { Component, OnInit, inject } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-product-detail',
  standalone: true,
  template: `
    <div class="product-detail">
      <h2>Product {{ productId }}</h2>
      <p>Category: {{ category }}</p>
    </div>
  `
})
export class ProductDetailComponent implements OnInit {
  private route = inject(ActivatedRoute);
  
  productId: string = '';
  category: string = '';
  
  ngOnInit(): void {
    // Get route parameters
    this.route.params.subscribe(params => {
      this.productId = params['id'];
    });
    
    // Get query parameters
    this.route.queryParams.subscribe(queryParams => {
      this.category = queryParams['category'] || 'all';
    });
    
    // Alternative: snapshot (one-time read)
    this.productId = this.route.snapshot.params['id'];
    this.category = this.route.snapshot.queryParams['category'] || 'all';
  }
}
```

### Using Signals with Route Parameters (Angular 16+)

```typescript
import { Component, computed, inject } from '@angular/core';
import { toSignal } from '@angular/core/rxjs-interop';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-product-detail',
  standalone: true,
  template: `
    <div class="product-detail">
      <h2>Product {{ productId() }}</h2>
      <p>Category: {{ category() }}</p>
    </div>
  `
})
export class ProductDetailComponent {
  private route = inject(ActivatedRoute);
  
  // Convert route params to signal
  params = toSignal(this.route.params, { initialValue: {} });
  productId = computed(() => this.params()['id']);
  
  // Query params as signal
  queryParams = toSignal(this.route.queryParams, { initialValue: {} });
  category = computed(() => this.queryParams()['category'] || 'all');
}
```

---

## Child Routes

```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: 'dashboard',
    component: DashboardComponent,
    children: [
      { path: '', redirectTo: 'overview', pathMatch: 'full' },
      { path: 'overview', component: OverviewComponent },
      { path: 'stats', component: StatsComponent },
      { path: 'settings', component: SettingsComponent }
    ]
  }
];

// dashboard.component.ts
@Component({
  selector: 'app-dashboard',
  standalone: true,
  imports: [RouterOutlet, RouterLink, RouterLinkActive],
  template: `
    <div class="dashboard">
      <aside class="sidebar">
        <h3>Dashboard</h3>
        <nav>
          <a routerLink="overview" routerLinkActive="active">Overview</a>
          <a routerLink="stats" routerLinkActive="active">Statistics</a>
          <a routerLink="settings" routerLinkActive="active">Settings</a>
        </nav>
      </aside>
      
      <main class="content">
        <router-outlet></router-outlet>
      </main>
    </div>
  `
})
export class DashboardComponent { }
```

---

## Route Guards

### CanActivate Guard

```typescript
// auth.guard.ts
import { inject } from '@angular/core';
import { Router, CanActivateFn } from '@angular/router';
import { AuthService } from './auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);
  
  if (authService.isAuthenticated()) {
    return true;
  }
  
  // Redirect to login
  return router.createUrlTree(['/login'], {
    queryParams: { returnUrl: state.url }
  });
};

// app.routes.ts
export const routes: Routes = [
  { path: 'login', component: LoginComponent },
  { 
    path: 'dashboard', 
    component: DashboardComponent,
    canActivate: [authGuard]
  }
];
```

---

## Complete Example: E-Commerce App

```typescript
// app.routes.ts
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'products', component: ProductsComponent },
  { path: 'products/:id', component: ProductDetailComponent },
  { path: 'cart', component: CartComponent },
  { path: 'login', component: LoginComponent },
  { 
    path: 'checkout', 
    component: CheckoutComponent,
    canActivate: [authGuard]
  },
  { path: '**', component: NotFoundComponent }
];

// app.component.ts
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RouterLink, RouterLinkActive],
  template: `
    <div class="app">
      <header>
        <h1 routerLink="/">ShopHub</h1>
        <nav>
          <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">Home</a>
          <a routerLink="/products" routerLinkActive="active">Products</a>
          <a routerLink="/cart" routerLinkActive="active">Cart ({{ cartCount }})</a>
          @if (isLoggedIn) {
            <button (click)="logout()">Logout</button>
          } @else {
            <a routerLink="/login" routerLinkActive="active">Login</a>
          }
        </nav>
      </header>
      
      <main>
        <router-outlet></router-outlet>
      </main>
      
      <footer>
        <p>&copy; 2024 ShopHub</p>
      </footer>
    </div>
  `,
  styles: [`
    .app {
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }
    
    header {
      background: #1976d2;
      color: white;
      padding: 1rem;
    }
    
    nav a.active {
      background: rgba(255,255,255,0.2);
      font-weight: bold;
    }
    
    main {
      flex: 1;
      padding: 2rem;
    }
  `]
})
export class AppComponent {
  cartCount = 0;
  isLoggedIn = false;
  
  logout(): void {
    this.isLoggedIn = false;
  }
}
```

---

## Best Practices

1. **Use Lazy Loading** for large modules
2. **Avoid Hardcoded URLs** - use route parameters
3. **Handle Parameters Properly** - subscribe for dynamic changes
4. **Preserve Query Params** when needed with `queryParamsHandling: 'merge'`
5. **Route Order Matters** - more specific routes first

---

## Common Pitfalls

1. **Forgetting to Import** RouterOutlet, RouterLink, RouterLinkActive
2. **Incorrect Path Matching** - use `pathMatch: 'full'` for empty paths
3. **Memory Leaks** - unsubscribe from route observables or use async pipe
4. **Route Order** - wildcard routes must be last

---

## Summary

✅ **Router Setup**: Use `provideRouter()` for standalone apps

✅ **Router Outlet**: Marks where routed components display

✅ **RouterLink**: Declarative navigation

✅ **RouterLinkActive**: Highlights active routes

✅ **Programmatic Navigation**: `router.navigate()` or `router.navigateByUrl()`

✅ **Route Parameters**: Access via `ActivatedRoute.params`

✅ **Route Guards**: Protect routes with `canActivate`, `canDeactivate`

✅ **Child Routes**: Nested routing with multiple outlet levels

Angular's routing provides powerful navigation for single-page applications!
