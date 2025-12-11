# Angular Routing

## Introduction to Routing

Angular Router enables navigation between different views/components in your application. It's a powerful feature that manages the browser's URL and application state.

## Setting Up Routing

### Generate App with Routing

```bash
# Create new app with routing
ng new my-app --routing

# Add routing to existing app
ng generate module app-routing --flat --module=app
```

### Basic Route Configuration

```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';
import { ContactComponent } from './contact/contact.component';
import { NotFoundComponent } from './not-found/not-found.component';

const routes: Routes = [
  { path: '', component: HomeComponent },                    // Default route
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent },
  { path: '**', component: NotFoundComponent }               // Wildcard route
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### Router Outlet

```typescript
// app.component.html
<nav>
  <a routerLink="/">Home</a>
  <a routerLink="/about">About</a>
  <a routerLink="/contact">Contact</a>
</nav>

<!-- Components load here -->
<router-outlet></router-outlet>
```

## Navigation

### RouterLink

```html
<!-- Basic navigation -->
<a routerLink="/about">About</a>
<a [routerLink]="['/about']">About</a>

<!-- With parameters -->
<a [routerLink]="['/user', userId]">View User</a>

<!-- With query parameters -->
<a [routerLink]="['/products']" [queryParams]="{category: 'electronics'}">
  Electronics
</a>

<!-- Active link styling -->
<a 
  routerLink="/about" 
  routerLinkActive="active"
  [routerLinkActiveOptions]="{exact: true}">
  About
</a>
```

### Programmatic Navigation

```typescript
import { Router } from '@angular/router';

@Component({
  selector: 'app-login',
  template: `
    <button (click)="goToHome()">Go Home</button>
    <button (click)="goToUser(123)">View User 123</button>
  `
})
export class LoginComponent {
  constructor(private router: Router) {}
  
  goToHome(): void {
    this.router.navigate(['/']);
  }
  
  goToUser(userId: number): void {
    this.router.navigate(['/user', userId]);
  }
  
  goToProducts(category: string): void {
    this.router.navigate(['/products'], {
      queryParams: { category }
    });
  }
  
  // Navigate relative to current route
  goToEdit(): void {
    this.router.navigate(['../edit'], { relativeTo: this.route });
  }
}
```

## Route Parameters

### Path Parameters

```typescript
// Routing configuration
const routes: Routes = [
  { path: 'user/:id', component: UserDetailComponent },
  { path: 'product/:id/reviews', component: ProductReviewsComponent }
];

// Component
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-user-detail',
  template: `
    <div *ngIf="user">
      <h2>{{ user.name }}</h2>
      <p>{{ user.email }}</p>
    </div>
  `
})
export class UserDetailComponent implements OnInit {
  user: User | null = null;
  
  constructor(
    private route: ActivatedRoute,
    private userService: UserService
  ) {}
  
  ngOnInit(): void {
    // Method 1: Snapshot (for components that won't be reused)
    const id = this.route.snapshot.paramMap.get('id');
    if (id) {
      this.loadUser(id);
    }
    
    // Method 2: Observable (for components that might be reused)
    this.route.paramMap.subscribe(params => {
      const id = params.get('id');
      if (id) {
        this.loadUser(id);
      }
    });
  }
  
  loadUser(id: string): void {
    this.userService.getUserById(id).subscribe(
      user => this.user = user
    );
  }
}
```

### Query Parameters

```typescript
// URL: /products?category=electronics&sort=price

@Component({
  selector: 'app-product-list',
  template: `
    <div>
      <p>Category: {{ category }}</p>
      <p>Sort: {{ sort }}</p>
    </div>
  `
})
export class ProductListComponent implements OnInit {
  category: string = '';
  sort: string = '';
  
  constructor(private route: ActivatedRoute) {}
  
  ngOnInit(): void {
    // Snapshot
    this.category = this.route.snapshot.queryParamMap.get('category') || '';
    this.sort = this.route.snapshot.queryParamMap.get('sort') || '';
    
    // Observable
    this.route.queryParamMap.subscribe(params => {
      this.category = params.get('category') || '';
      this.sort = params.get('sort') || '';
      this.loadProducts();
    });
  }
  
  loadProducts(): void {
    // Fetch products based on category and sort
  }
}
```

## Nested Routes (Child Routes)

```typescript
// app-routing.module.ts
const routes: Routes = [
  {
    path: 'products',
    component: ProductsComponent,
    children: [
      { path: '', component: ProductListComponent },
      { path: ':id', component: ProductDetailComponent },
      { path: ':id/edit', component: ProductEditComponent }
    ]
  }
];

// products.component.html
<div class="products-layout">
  <aside>
    <nav>
      <a [routerLink]="['./']">All Products</a>
      <!-- More navigation -->
    </nav>
  </aside>
  
  <main>
    <!-- Child components render here -->
    <router-outlet></router-outlet>
  </main>
</div>
```

## Route Guards

### CanActivate - Prevent Unauthorized Access

```typescript
import { Injectable } from '@angular/core';
import { 
  CanActivate, 
  ActivatedRouteSnapshot, 
  RouterStateSnapshot, 
  Router 
} from '@angular/router';
import { Observable } from 'rxjs';
import { AuthService } from './auth.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(
    private authService: AuthService,
    private router: Router
  ) {}
  
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): boolean | Observable<boolean> {
    if (this.authService.isAuthenticated()) {
      return true;
    }
    
    // Redirect to login with return URL
    this.router.navigate(['/login'], {
      queryParams: { returnUrl: state.url }
    });
    return false;
  }
}

// Apply to routes
const routes: Routes = [
  {
    path: 'dashboard',
    component: DashboardComponent,
    canActivate: [AuthGuard]
  }
];
```

### CanActivateChild - Protect Child Routes

```typescript
@Injectable({
  providedIn: 'root'
})
export class AdminGuard implements CanActivateChild {
  constructor(
    private authService: AuthService,
    private router: Router
  ) {}
  
  canActivateChild(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): boolean {
    const user = this.authService.currentUserValue;
    
    if (user && user.role === 'admin') {
      return true;
    }
    
    this.router.navigate(['/']);
    return false;
  }
}

// Apply to parent route
const routes: Routes = [
  {
    path: 'admin',
    component: AdminComponent,
    canActivateChild: [AdminGuard],
    children: [
      { path: 'users', component: UserManagementComponent },
      { path: 'settings', component: SettingsComponent }
    ]
  }
];
```

### CanDeactivate - Prevent Navigation Away

```typescript
export interface CanComponentDeactivate {
  canDeactivate: () => boolean | Observable<boolean>;
}

@Injectable({
  providedIn: 'root'
})
export class UnsavedChangesGuard implements CanDeactivate<CanComponentDeactivate> {
  canDeactivate(
    component: CanComponentDeactivate
  ): boolean | Observable<boolean> {
    return component.canDeactivate ? component.canDeactivate() : true;
  }
}

// Component implementation
@Component({
  selector: 'app-product-form',
  template: `
    <form [formGroup]="productForm">
      <!-- Form fields -->
    </form>
  `
})
export class ProductFormComponent implements CanComponentDeactivate {
  productForm!: FormGroup;
  
  canDeactivate(): boolean {
    if (this.productForm.dirty) {
      return confirm('You have unsaved changes. Do you really want to leave?');
    }
    return true;
  }
}

// Route configuration
const routes: Routes = [
  {
    path: 'product/new',
    component: ProductFormComponent,
    canDeactivate: [UnsavedChangesGuard]
  }
];
```

### Resolve - Pre-fetch Data

```typescript
@Injectable({
  providedIn: 'root'
})
export class ProductResolver implements Resolve<Product> {
  constructor(private productService: ProductService) {}
  
  resolve(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<Product> {
    const id = route.paramMap.get('id')!;
    return this.productService.getProduct(id);
  }
}

// Route configuration
const routes: Routes = [
  {
    path: 'product/:id',
    component: ProductDetailComponent,
    resolve: { product: ProductResolver }
  }
];

// Component usage
@Component({
  selector: 'app-product-detail',
  template: `
    <div *ngIf="product">
      <h2>{{ product.name }}</h2>
      <p>{{ product.description }}</p>
    </div>
  `
})
export class ProductDetailComponent implements OnInit {
  product!: Product;
  
  constructor(private route: ActivatedRoute) {}
  
  ngOnInit(): void {
    this.route.data.subscribe(data => {
      this.product = data['product'];
    });
  }
}
```

## Lazy Loading

Load modules only when needed to improve performance.

```typescript
// app-routing.module.ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  {
    path: 'products',
    loadChildren: () => import('./products/products.module')
      .then(m => m.ProductsModule)
  },
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.module')
      .then(m => m.AdminModule),
    canLoad: [AuthGuard]
  }
];

// products-routing.module.ts
const routes: Routes = [
  { path: '', component: ProductListComponent },
  { path: ':id', component: ProductDetailComponent }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ProductsRoutingModule { }
```

## Route Strategies

### PreloadAllModules

```typescript
import { PreloadAllModules } from '@angular/router';

@NgModule({
  imports: [RouterModule.forRoot(routes, {
    preloadingStrategy: PreloadAllModules
  })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### Custom Preload Strategy

```typescript
@Injectable({
  providedIn: 'root'
})
export class SelectivePreloadStrategy implements PreloadingStrategy {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    // Preload if route has data.preload = true
    if (route.data && route.data['preload']) {
      console.log('Preloading:', route.path);
      return load();
    }
    return of(null);
  }
}

// Usage
const routes: Routes = [
  {
    path: 'products',
    loadChildren: () => import('./products/products.module')
      .then(m => m.ProductsModule),
    data: { preload: true }
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {
    preloadingStrategy: SelectivePreloadStrategy
  })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## Real-World Example: E-commerce Routing

```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AuthGuard } from './core/guards/auth.guard';
import { AdminGuard } from './core/guards/admin.guard';

const routes: Routes = [
  // Public routes
  {
    path: '',
    loadChildren: () => import('./features/home/home.module')
      .then(m => m.HomeModule)
  },
  {
    path: 'products',
    loadChildren: () => import('./features/products/products.module')
      .then(m => m.ProductsModule),
    data: { preload: true }
  },
  {
    path: 'auth',
    loadChildren: () => import('./features/auth/auth.module')
      .then(m => m.AuthModule)
  },
  
  // Protected routes
  {
    path: 'cart',
    loadChildren: () => import('./features/cart/cart.module')
      .then(m => m.CartModule),
    canActivate: [AuthGuard]
  },
  {
    path: 'orders',
    loadChildren: () => import('./features/orders/orders.module')
      .then(m => m.OrdersModule),
    canActivate: [AuthGuard]
  },
  {
    path: 'profile',
    loadChildren: () => import('./features/profile/profile.module')
      .then(m => m.ProfileModule),
    canActivate: [AuthGuard]
  },
  
  // Admin routes
  {
    path: 'admin',
    loadChildren: () => import('./features/admin/admin.module')
      .then(m => m.AdminModule),
    canActivate: [AuthGuard, AdminGuard]
  },
  
  // Fallback
  {
    path: '**',
    redirectTo: '/404'
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {
    preloadingStrategy: SelectivePreloadStrategy,
    scrollPositionRestoration: 'enabled',
    anchorScrolling: 'enabled'
  })],
  exports: [RouterModule]
})
export class AppRoutingModule { }

// products-routing.module.ts
const routes: Routes = [
  {
    path: '',
    component: ProductsComponent,
    children: [
      { path: '', component: ProductListComponent },
      { path: 'category/:category', component: ProductListComponent },
      {
        path: ':id',
        component: ProductDetailComponent,
        resolve: { product: ProductResolver }
      },
      {
        path: ':id/reviews',
        component: ProductReviewsComponent
      }
    ]
  }
];

// admin-routing.module.ts
const routes: Routes = [
  {
    path: '',
    component: AdminLayoutComponent,
    canActivateChild: [AdminGuard],
    children: [
      { path: '', redirectTo: 'dashboard', pathMatch: 'full' },
      { path: 'dashboard', component: AdminDashboardComponent },
      { path: 'products', component: AdminProductsComponent },
      {
        path: 'products/new',
        component: AdminProductFormComponent,
        canDeactivate: [UnsavedChangesGuard]
      },
      {
        path: 'products/:id/edit',
        component: AdminProductFormComponent,
        canDeactivate: [UnsavedChangesGuard]
      },
      { path: 'orders', component: AdminOrdersComponent },
      { path: 'users', component: AdminUsersComponent }
    ]
  }
];
```

## Router Events

Monitor navigation lifecycle.

```typescript
import { Router, NavigationStart, NavigationEnd, NavigationError } from '@angular/router';
import { filter } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `
    <div *ngIf="loading" class="loading-indicator">
      Loading...
    </div>
    <router-outlet></router-outlet>
  `
})
export class AppComponent implements OnInit {
  loading = false;
  
  constructor(private router: Router) {}
  
  ngOnInit(): void {
    // Show loading on navigation start
    this.router.events.pipe(
      filter(event => event instanceof NavigationStart)
    ).subscribe(() => {
      this.loading = true;
    });
    
    // Hide loading on navigation end
    this.router.events.pipe(
      filter(event => event instanceof NavigationEnd)
    ).subscribe(() => {
      this.loading = false;
    });
    
    // Handle navigation errors
    this.router.events.pipe(
      filter(event => event instanceof NavigationError)
    ).subscribe((event: NavigationError) => {
      console.error('Navigation error:', event.error);
      this.loading = false;
    });
  }
}
```

## Best Practices

1. **Use lazy loading**: Improve initial load time
2. **Implement guards**: Protect routes appropriately
3. **Use resolvers**: Pre-fetch critical data
4. **Handle errors**: Provide fallback routes
5. **Organize routes**: Group related routes in feature modules
6. **Use route parameters**: Keep URLs clean and RESTful
7. **Implement proper redirects**: Handle legacy URLs
8. **Monitor performance**: Use router events for analytics

## Summary

| Feature | Purpose | Example |
|---------|---------|---------|
| RouterLink | Declarative navigation | `<a routerLink="/about">` |
| Router.navigate() | Programmatic navigation | `router.navigate(['/user', id])` |
| Route Parameters | Dynamic routes | `{ path: 'user/:id' }` |
| Guards | Access control | `canActivate: [AuthGuard]` |
| Lazy Loading | Code splitting | `loadChildren: () => import()` |
| Resolvers | Data pre-fetching | `resolve: { data: Resolver }` |

## Next Steps

- Learn about route animations
- Explore advanced guard patterns
- Master state preservation
- Study deep linking strategies
