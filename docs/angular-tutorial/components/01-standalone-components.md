# Standalone Components in Angular

## What are Standalone Components?

Standalone components are a modern approach in Angular (v14+) that **don't require NgModules**. They can be used independently and import their own dependencies directly.

### Traditional vs Standalone

#### Traditional Component (Module-based)

```typescript
// user.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-user',
  template: `<h1>User Component</h1>`
})
export class UserComponent {}

// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { UserComponent } from './user.component';

@NgModule({
  declarations: [UserComponent],  // Must declare here
  imports: [BrowserModule],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

#### Standalone Component (Modern Approach)

```typescript
// user.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-user',
  standalone: true,  // ← Key difference
  template: `<h1>User Component</h1>`
})
export class UserComponent {}

// Can be used directly without module declaration!
```

## Creating Standalone Components

### Using Angular CLI

```bash
# Generate standalone component
ng generate component components/product --standalone

# Or shorthand
ng g c components/product --standalone
```

### Manual Creation

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-product',
  standalone: true,
  imports: [CommonModule],  // Import dependencies directly
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.css']
})
export class ProductComponent {
  productName = 'Laptop';
  price = 999;
}
```

## Importing Dependencies

### Importing Other Modules

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { HttpClientModule } from '@angular/common/http';

@Component({
  selector: 'app-user-form',
  standalone: true,
  imports: [
    CommonModule,      // *ngIf, *ngFor, pipes
    FormsModule,       // ngModel, forms
    HttpClientModule   // HTTP client
  ],
  template: `
    <div *ngIf="isVisible">
      <input [(ngModel)]="username" placeholder="Username">
      <p>Hello, {{ username }}!</p>
    </div>
  `
})
export class UserFormComponent {
  isVisible = true;
  username = '';
}
```

### Importing Other Components

```typescript
// child.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-child',
  standalone: true,
  template: `<p>Child Component</p>`
})
export class ChildComponent {}

// parent.component.ts
import { Component } from '@angular/core';
import { ChildComponent } from './child.component';

@Component({
  selector: 'app-parent',
  standalone: true,
  imports: [ChildComponent],  // Import child component
  template: `
    <div>
      <h2>Parent Component</h2>
      <app-child></app-child>
    </div>
  `
})
export class ParentComponent {}
```

## Bootstrapping Standalone Components

### main.ts Configuration

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http';
import { routes } from './app/app.routes';

bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient()
  ]
}).catch(err => console.error(err));
```

### App Component as Standalone

```typescript
// app.component.ts
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    RouterOutlet,
    CommonModule
  ],
  template: `
    <header>
      <h1>{{ title }}</h1>
    </header>
    <router-outlet></router-outlet>
  `,
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'My Standalone App';
}
```

## Complete Example: Todo App with Standalone Components

### Todo Component

```typescript
// todo.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

@Component({
  selector: 'app-todo',
  standalone: true,
  imports: [CommonModule, FormsModule],
  template: `
    <div class="todo-container">
      <h2>Todo List</h2>
      
      <!-- Add Todo Form -->
      <div class="add-todo">
        <input 
          [(ngModel)]="newTodoTitle" 
          (keyup.enter)="addTodo()"
          placeholder="What needs to be done?">
        <button (click)="addTodo()">Add</button>
      </div>
      
      <!-- Todo List -->
      <div class="todo-list">
        <div 
          *ngFor="let todo of todos" 
          class="todo-item"
          [class.completed]="todo.completed">
          <input 
            type="checkbox" 
            [(ngModel)]="todo.completed">
          <span>{{ todo.title }}</span>
          <button (click)="deleteTodo(todo.id)">Delete</button>
        </div>
      </div>
      
      <!-- Stats -->
      <div class="stats">
        <p>Total: {{ todos.length }}</p>
        <p>Active: {{ getActiveTodos() }}</p>
        <p>Completed: {{ getCompletedTodos() }}</p>
      </div>
    </div>
  `,
  styles: [`
    .todo-container {
      max-width: 600px;
      margin: 20px auto;
      padding: 20px;
    }
    
    .add-todo {
      display: flex;
      gap: 10px;
      margin-bottom: 20px;
    }
    
    .add-todo input {
      flex: 1;
      padding: 10px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    
    .add-todo button {
      padding: 10px 20px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    .todo-item {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 10px;
      border-bottom: 1px solid #eee;
    }
    
    .todo-item.completed span {
      text-decoration: line-through;
      color: #999;
    }
    
    .todo-item button {
      margin-left: auto;
      padding: 5px 10px;
      background: #dc3545;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    .stats {
      margin-top: 20px;
      padding: 10px;
      background: #f8f9fa;
      border-radius: 4px;
    }
    
    .stats p {
      margin: 5px 0;
    }
  `]
})
export class TodoComponent {
  todos: Todo[] = [
    { id: 1, title: 'Learn Angular', completed: false },
    { id: 2, title: 'Build a project', completed: false }
  ];
  
  newTodoTitle = '';
  nextId = 3;
  
  addTodo(): void {
    if (this.newTodoTitle.trim()) {
      this.todos.push({
        id: this.nextId++,
        title: this.newTodoTitle,
        completed: false
      });
      this.newTodoTitle = '';
    }
  }
  
  deleteTodo(id: number): void {
    this.todos = this.todos.filter(t => t.id !== id);
  }
  
  getActiveTodos(): number {
    return this.todos.filter(t => !t.completed).length;
  }
  
  getCompletedTodos(): number {
    return this.todos.filter(t => t.completed).length;
  }
}
```

## Standalone Components with Services

### Service Definition

```typescript
// data.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'  // Available app-wide
})
export class DataService {
  private data: string[] = ['Item 1', 'Item 2', 'Item 3'];
  
  getData(): string[] {
    return this.data;
  }
  
  addData(item: string): void {
    this.data.push(item);
  }
}
```

### Using Service in Standalone Component

```typescript
import { Component, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { DataService } from './data.service';

@Component({
  selector: 'app-data-display',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div>
      <h3>Data List</h3>
      <ul>
        <li *ngFor="let item of items">{{ item }}</li>
      </ul>
      <button (click)="addItem()">Add Item</button>
    </div>
  `
})
export class DataDisplayComponent {
  // New way: inject() function
  private dataService = inject(DataService);
  
  // Or traditional way still works:
  // constructor(private dataService: DataService) {}
  
  items = this.dataService.getData();
  
  addItem(): void {
    this.dataService.addData(`Item ${this.items.length + 1}`);
    this.items = this.dataService.getData();
  }
}
```

## Routing with Standalone Components

### app.routes.ts

```typescript
import { Routes } from '@angular/router';
import { HomeComponent } from './pages/home.component';
import { AboutComponent } from './pages/about.component';
import { ProductsComponent } from './pages/products.component';

export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'products', component: ProductsComponent },
  {
    path: 'admin',
    loadComponent: () => import('./pages/admin.component')
      .then(m => m.AdminComponent)  // Lazy loading
  }
];
```

### App Component with Router

```typescript
import { Component } from '@angular/core';
import { RouterOutlet, RouterLink, RouterLinkActive } from '@angular/router';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RouterLink, RouterLinkActive],
  template: `
    <nav>
      <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">
        Home
      </a>
      <a routerLink="/about" routerLinkActive="active">About</a>
      <a routerLink="/products" routerLinkActive="active">Products</a>
      <a routerLink="/admin" routerLinkActive="active">Admin</a>
    </nav>
    <router-outlet></router-outlet>
  `,
  styles: [`
    nav {
      display: flex;
      gap: 20px;
      padding: 20px;
      background: #333;
    }
    
    nav a {
      color: white;
      text-decoration: none;
      padding: 10px;
    }
    
    nav a.active {
      background: #007bff;
      border-radius: 4px;
    }
  `]
})
export class AppComponent {}
```

## Practical Exercise: Build a Product Catalog

### Product Model

```typescript
// models/product.model.ts
export interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
  imageUrl: string;
  inStock: boolean;
}
```

### Product Card Component

```typescript
// components/product-card.component.ts
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { CommonModule } from '@angular/common';
import { Product } from '../models/product.model';

@Component({
  selector: 'app-product-card',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="card">
      <img [src]="product.imageUrl" [alt]="product.name">
      <h3>{{ product.name }}</h3>
      <p>{{ product.description }}</p>
      <p class="price">{{ product.price | currency }}</p>
      <p [class.in-stock]="product.inStock" 
         [class.out-of-stock]="!product.inStock">
        {{ product.inStock ? 'In Stock' : 'Out of Stock' }}
      </p>
      <button 
        (click)="onAddToCart()" 
        [disabled]="!product.inStock">
        Add to Cart
      </button>
    </div>
  `,
  styles: [`
    .card {
      border: 1px solid #ddd;
      padding: 15px;
      border-radius: 8px;
      max-width: 300px;
    }
    
    .card img {
      width: 100%;
      height: 200px;
      object-fit: cover;
      border-radius: 4px;
    }
    
    .price {
      font-size: 1.5em;
      font-weight: bold;
      color: #28a745;
    }
    
    .in-stock {
      color: green;
    }
    
    .out-of-stock {
      color: red;
    }
    
    button {
      width: 100%;
      padding: 10px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    button:disabled {
      background: #ccc;
      cursor: not-allowed;
    }
  `]
})
export class ProductCardComponent {
  @Input() product!: Product;
  @Output() addToCart = new EventEmitter<Product>();
  
  onAddToCart(): void {
    this.addToCart.emit(this.product);
  }
}
```

### Product List Component

```typescript
// components/product-list.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ProductCardComponent } from './product-card.component';
import { Product } from '../models/product.model';

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [CommonModule, ProductCardComponent],
  template: `
    <div class="container">
      <h1>Product Catalog</h1>
      <div class="product-grid">
        <app-product-card 
          *ngFor="let product of products"
          [product]="product"
          (addToCart)="handleAddToCart($event)">
        </app-product-card>
      </div>
      
      <div class="cart-info" *ngIf="cartCount > 0">
        Cart: {{ cartCount }} items
      </div>
    </div>
  `,
  styles: [`
    .container {
      padding: 20px;
    }
    
    .product-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
      gap: 20px;
      margin-top: 20px;
    }
    
    .cart-info {
      position: fixed;
      bottom: 20px;
      right: 20px;
      padding: 15px;
      background: #28a745;
      color: white;
      border-radius: 8px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.2);
    }
  `]
})
export class ProductListComponent {
  cartCount = 0;
  
  products: Product[] = [
    {
      id: 1,
      name: 'Laptop',
      price: 999,
      description: 'High-performance laptop',
      imageUrl: 'https://via.placeholder.com/300',
      inStock: true
    },
    {
      id: 2,
      name: 'Mouse',
      price: 29,
      description: 'Wireless mouse',
      imageUrl: 'https://via.placeholder.com/300',
      inStock: true
    },
    {
      id: 3,
      name: 'Keyboard',
      price: 79,
      description: 'Mechanical keyboard',
      imageUrl: 'https://via.placeholder.com/300',
      inStock: false
    }
  ];
  
  handleAddToCart(product: Product): void {
    this.cartCount++;
    console.log('Added to cart:', product.name);
  }
}
```

## Benefits of Standalone Components

1. **Simpler Architecture**: No need for NgModules
2. **Better Tree-Shaking**: Unused code is eliminated
3. **Easier Testing**: Components are self-contained
4. **Faster Development**: Less boilerplate code
5. **Lazy Loading**: Easier to implement
6. **Better IDE Support**: Clearer dependencies

## Migration from Modules to Standalone

```bash
# Angular CLI can help migrate
ng generate @angular/core:standalone
```

## Summary

| Feature | Module-Based | Standalone |
|---------|-------------|------------|
| `standalone` flag | Not present | `true` |
| Declarations | In NgModule | Not needed |
| Imports | In NgModule | In component |
| Bootstrap | NgModule | `bootstrapApplication()` |
| Dependencies | Via module | Direct imports |

## Next Steps

- Learn about data binding techniques
- Explore directives in depth
- Master component communication
- Understand dependency injection
