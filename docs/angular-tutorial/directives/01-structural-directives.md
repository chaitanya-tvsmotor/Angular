# Structural Directives in Angular

## What are Structural Directives?

Structural directives change the DOM structure by adding, removing, or manipulating elements. They are prefixed with an asterisk (*).

## Traditional Structural Directives

### 1. *ngIf - Conditional Rendering

Shows or hides elements based on a condition.

#### Basic Usage

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-ngif-demo',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div>
      <h2>*ngIf Examples</h2>
      
      <!-- Simple *ngIf -->
      <p *ngIf="isVisible">This text is visible!</p>
      
      <!-- *ngIf with else -->
      <div *ngIf="isLoggedIn; else loggedOut">
        <p>Welcome back, {{ username }}!</p>
        <button (click)="logout()">Logout</button>
      </div>
      <ng-template #loggedOut>
        <p>Please log in to continue</p>
        <button (click)="login()">Login</button>
      </ng-template>
      
      <!-- *ngIf with then and else -->
      <div *ngIf="user; then userInfo; else noUser"></div>
      <ng-template #userInfo>
        <p>User: {{ user?.name }}</p>
        <p>Email: {{ user?.email }}</p>
      </ng-template>
      <ng-template #noUser>
        <p>No user data available</p>
      </ng-template>
      
      <!-- *ngIf with as (storing result) -->
      <div *ngIf="getUserData() as userData">
        <p>{{ userData.name }} ({{ userData.role }})</p>
      </div>
    </div>
  `,
  styles: [`
    button {
      padding: 10px 20px;
      margin: 5px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
  `]
})
export class NgIfDemoComponent {
  isVisible = true;
  isLoggedIn = false;
  username = 'JohnDoe';
  user = null;
  
  login(): void {
    this.isLoggedIn = true;
    this.user = { name: 'John Doe', email: 'john@example.com' };
  }
  
  logout(): void {
    this.isLoggedIn = false;
    this.user = null;
  }
  
  getUserData() {
    return { name: 'Jane Smith', role: 'Admin' };
  }
}
```

### 2. *ngFor - Loops

Repeats elements for each item in an array.

```typescript
@Component({
  selector: 'app-ngfor-demo',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div>
      <h2>*ngFor Examples</h2>
      
      <!-- Basic *ngFor -->
      <h3>Simple List</h3>
      <ul>
        <li *ngFor="let item of items">{{ item }}</li>
      </ul>
      
      <!-- *ngFor with index -->
      <h3>With Index</h3>
      <ul>
        <li *ngFor="let item of items; let i = index">
          {{ i + 1 }}. {{ item }}
        </li>
      </ul>
      
      <!-- *ngFor with first, last, even, odd -->
      <h3>With Position Variables</h3>
      <ul>
        <li *ngFor="let item of items; 
                    let i = index;
                    let first = first;
                    let last = last;
                    let even = even;
                    let odd = odd"
            [class.first]="first"
            [class.last]="last"
            [class.even]="even"
            [class.odd]="odd">
          {{ i }}. {{ item }}
        </li>
      </ul>
      
      <!-- *ngFor with objects -->
      <h3>User List</h3>
      <div class="user-card" *ngFor="let user of users; trackBy: trackByUserId">
        <h4>{{ user.name }}</h4>
        <p>{{ user.email }}</p>
        <p>Age: {{ user.age }}</p>
      </div>
      
      <!-- Nested *ngFor -->
      <h3>Nested Loop</h3>
      <div *ngFor="let category of categories">
        <h4>{{ category.name }}</h4>
        <ul>
          <li *ngFor="let product of category.products">
            {{ product.name }} - ${{ product.price }}
          </li>
        </ul>
      </div>
    </div>
  `,
  styles: [`
    .first { font-weight: bold; color: green; }
    .last { font-style: italic; color: red; }
    .even { background: #f0f0f0; }
    .odd { background: #fff; }
    
    .user-card {
      border: 1px solid #ddd;
      padding: 15px;
      margin: 10px 0;
      border-radius: 4px;
    }
  `]
})
export class NgForDemoComponent {
  items = ['Apple', 'Banana', 'Cherry', 'Date', 'Elderberry'];
  
  users = [
    { id: 1, name: 'John Doe', email: 'john@example.com', age: 30 },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com', age: 25 },
    { id: 3, name: 'Bob Johnson', email: 'bob@example.com', age: 35 }
  ];
  
  categories = [
    {
      name: 'Electronics',
      products: [
        { name: 'Laptop', price: 999 },
        { name: 'Phone', price: 699 }
      ]
    },
    {
      name: 'Clothing',
      products: [
        { name: 'T-Shirt', price: 25 },
        { name: 'Jeans', price: 60 }
      ]
    }
  ];
  
  // TrackBy function for performance
  trackByUserId(index: number, user: any): number {
    return user.id;
  }
}
```

### 3. *ngSwitch - Multiple Conditions

Choose one element from multiple options based on a value.

```typescript
@Component({
  selector: 'app-ngswitch-demo',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div>
      <h2>*ngSwitch Examples</h2>
      
      <!-- Select option -->
      <select [(ngModel)]="selectedView">
        <option value="home">Home</option>
        <option value="profile">Profile</option>
        <option value="settings">Settings</option>
        <option value="about">About</option>
      </select>
      
      <!-- Switch based on selection -->
      <div [ngSwitch]="selectedView">
        <div *ngSwitchCase="'home'">
          <h3>Home View</h3>
          <p>Welcome to the home page!</p>
        </div>
        
        <div *ngSwitchCase="'profile'">
          <h3>Profile View</h3>
          <p>Your profile information</p>
        </div>
        
        <div *ngSwitchCase="'settings'">
          <h3>Settings View</h3>
          <p>Manage your settings</p>
        </div>
        
        <div *ngSwitchCase="'about'">
          <h3>About View</h3>
          <p>Learn more about us</p>
        </div>
        
        <div *ngSwitchDefault>
          <h3>Default View</h3>
          <p>Page not found</p>
        </div>
      </div>
      
      <!-- User status example -->
      <h3>User Status</h3>
      <button (click)="setStatus('active')">Active</button>
      <button (click)="setStatus('inactive')">Inactive</button>
      <button (click)="setStatus('banned')">Banned</button>
      
      <div [ngSwitch]="userStatus" class="status-card">
        <div *ngSwitchCase="'active'" class="status-active">
          <h4>✓ Active</h4>
          <p>Your account is active</p>
        </div>
        
        <div *ngSwitchCase="'inactive'" class="status-inactive">
          <h4>⚠ Inactive</h4>
          <p>Your account is currently inactive</p>
        </div>
        
        <div *ngSwitchCase="'banned'" class="status-banned">
          <h4>✗ Banned</h4>
          <p>Your account has been banned</p>
        </div>
        
        <div *ngSwitchDefault>
          <h4>Unknown Status</h4>
        </div>
      </div>
    </div>
  `,
  styles: [`
    select, button {
      padding: 10px;
      margin: 5px;
    }
    
    .status-card {
      margin: 20px 0;
      padding: 20px;
      border-radius: 4px;
    }
    
    .status-active {
      background: #d4edda;
      color: #155724;
    }
    
    .status-inactive {
      background: #fff3cd;
      color: #856404;
    }
    
    .status-banned {
      background: #f8d7da;
      color: #721c24;
    }
  `]
})
export class NgSwitchDemoComponent {
  selectedView = 'home';
  userStatus = 'active';
  
  setStatus(status: string): void {
    this.userStatus = status;
  }
}
```

## New Control Flow (@if, @for, @switch) - Angular 17+

Angular 17 introduced new built-in control flow syntax that doesn't require CommonModule.

### @if - New Conditional Syntax

```typescript
@Component({
  selector: 'app-new-if',
  standalone: true,
  template: `
    <div>
      <h2>@if Examples (New Syntax)</h2>
      
      <!-- Simple @if -->
      @if (isVisible) {
        <p>This is visible!</p>
      }
      
      <!-- @if with @else -->
      @if (isLoggedIn) {
        <div>
          <p>Welcome, {{ username }}!</p>
          <button (click)="logout()">Logout</button>
        </div>
      } @else {
        <div>
          <p>Please log in</p>
          <button (click)="login()">Login</button>
        </div>
      }
      
      <!-- @if with @else if -->
      @if (score >= 90) {
        <p class="grade-a">Grade: A (Excellent!)</p>
      } @else if (score >= 80) {
        <p class="grade-b">Grade: B (Good!)</p>
      } @else if (score >= 70) {
        <p class="grade-c">Grade: C (Average)</p>
      } @else {
        <p class="grade-f">Grade: F (Need Improvement)</p>
      }
      
      <!-- @if with as (storing value) -->
      @if (getUserProfile(); as profile) {
        <div class="profile">
          <h3>{{ profile.name }}</h3>
          <p>{{ profile.email }}</p>
          <p>Role: {{ profile.role }}</p>
        </div>
      }
    </div>
  `,
  styles: [`
    .grade-a { color: green; font-weight: bold; }
    .grade-b { color: blue; }
    .grade-c { color: orange; }
    .grade-f { color: red; }
    
    .profile {
      border: 1px solid #ddd;
      padding: 15px;
      border-radius: 4px;
      margin: 10px 0;
    }
  `]
})
export class NewIfComponent {
  isVisible = true;
  isLoggedIn = false;
  username = 'JohnDoe';
  score = 85;
  
  login(): void {
    this.isLoggedIn = true;
  }
  
  logout(): void {
    this.isLoggedIn = false;
  }
  
  getUserProfile() {
    return {
      name: 'Jane Smith',
      email: 'jane@example.com',
      role: 'Developer'
    };
  }
}
```

### @for - New Loop Syntax

```typescript
@Component({
  selector: 'app-new-for',
  standalone: true,
  template: `
    <div>
      <h2>@for Examples (New Syntax)</h2>
      
      <!-- Basic @for -->
      <h3>Simple List</h3>
      <ul>
        @for (item of items; track item) {
          <li>{{ item }}</li>
        }
      </ul>
      
      <!-- @for with index -->
      <h3>With Index</h3>
      <ul>
        @for (item of items; track item; let i = $index) {
          <li>{{ i + 1 }}. {{ item }}</li>
        }
      </ul>
      
      <!-- @for with position variables -->
      <h3>With Position Variables</h3>
      <ul>
        @for (item of items; track item; 
              let i = $index;
              let first = $first;
              let last = $last;
              let even = $even;
              let odd = $odd) {
          <li [class.first]="first"
              [class.last]="last"
              [class.even]="even"
              [class.odd]="odd">
            {{ i }}. {{ item }}
          </li>
        }
      </ul>
      
      <!-- @for with objects -->
      <h3>User Cards</h3>
      @for (user of users; track user.id) {
        <div class="user-card">
          <h4>{{ user.name }}</h4>
          <p>{{ user.email }}</p>
          <p>Age: {{ user.age }}</p>
        </div>
      }
      
      <!-- @for with @empty -->
      <h3>Products</h3>
      @for (product of products; track product.id) {
        <div class="product">{{ product.name }}</div>
      } @empty {
        <p>No products available</p>
      }
      
      <!-- Nested @for -->
      <h3>Categories</h3>
      @for (category of categories; track category.name) {
        <div class="category">
          <h4>{{ category.name }}</h4>
          <ul>
            @for (product of category.products; track product.name) {
              <li>{{ product.name }} - ${{ product.price }}</li>
            }
          </ul>
        </div>
      }
    </div>
  `,
  styles: [`
    .first { font-weight: bold; color: green; }
    .last { font-style: italic; color: red; }
    .even { background: #f0f0f0; }
    .odd { background: #fff; }
    
    .user-card, .product {
      border: 1px solid #ddd;
      padding: 15px;
      margin: 10px 0;
      border-radius: 4px;
    }
    
    .category {
      margin: 15px 0;
      padding: 10px;
      background: #f8f9fa;
      border-radius: 4px;
    }
  `]
})
export class NewForComponent {
  items = ['Apple', 'Banana', 'Cherry', 'Date'];
  
  users = [
    { id: 1, name: 'John Doe', email: 'john@example.com', age: 30 },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com', age: 25 }
  ];
  
  products: any[] = [];  // Empty for @empty demo
  
  categories = [
    {
      name: 'Electronics',
      products: [
        { name: 'Laptop', price: 999 },
        { name: 'Phone', price: 699 }
      ]
    }
  ];
}
```

### @switch - New Switch Syntax

```typescript
@Component({
  selector: 'app-new-switch',
  standalone: true,
  template: `
    <div>
      <h2>@switch Examples (New Syntax)</h2>
      
      <!-- View selector -->
      <div class="controls">
        <button (click)="selectedView = 'home'">Home</button>
        <button (click)="selectedView = 'profile'">Profile</button>
        <button (click)="selectedView = 'settings'">Settings</button>
        <button (click)="selectedView = 'about'">About</button>
      </div>
      
      <!-- Switch views -->
      @switch (selectedView) {
        @case ('home') {
          <div class="view">
            <h3>Home View</h3>
            <p>Welcome to the home page!</p>
          </div>
        }
        @case ('profile') {
          <div class="view">
            <h3>Profile View</h3>
            <p>Your profile information</p>
          </div>
        }
        @case ('settings') {
          <div class="view">
            <h3>Settings View</h3>
            <p>Manage your settings</p>
          </div>
        }
        @case ('about') {
          <div class="view">
            <h3>About View</h3>
            <p>Learn more about us</p>
          </div>
        }
        @default {
          <div class="view">
            <h3>404 - Not Found</h3>
            <p>Page not found</p>
          </div>
        }
      }
      
      <!-- Status indicator -->
      <h3>Account Status</h3>
      <div class="controls">
        <button (click)="status = 'active'">Active</button>
        <button (click)="status = 'pending'">Pending</button>
        <button (click)="status = 'suspended'">Suspended</button>
      </div>
      
      @switch (status) {
        @case ('active') {
          <div class="status-box status-active">
            <h4>✓ Account Active</h4>
            <p>Your account is fully active</p>
          </div>
        }
        @case ('pending') {
          <div class="status-box status-pending">
            <h4>⏳ Pending Verification</h4>
            <p>Please verify your email</p>
          </div>
        }
        @case ('suspended') {
          <div class="status-box status-suspended">
            <h4>⚠ Account Suspended</h4>
            <p>Contact support for assistance</p>
          </div>
        }
        @default {
          <div class="status-box">
            <h4>Unknown Status</h4>
          </div>
        }
      }
    </div>
  `,
  styles: [`
    .controls {
      margin: 15px 0;
    }
    
    button {
      padding: 10px 20px;
      margin: 5px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    .view {
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 4px;
      margin: 15px 0;
    }
    
    .status-box {
      padding: 20px;
      border-radius: 4px;
      margin: 15px 0;
    }
    
    .status-active {
      background: #d4edda;
      color: #155724;
    }
    
    .status-pending {
      background: #fff3cd;
      color: #856404;
    }
    
    .status-suspended {
      background: #f8d7da;
      color: #721c24;
    }
  `]
})
export class NewSwitchComponent {
  selectedView = 'home';
  status = 'active';
}
```

## Comparison: Old vs New Syntax

| Feature | Old Syntax | New Syntax |
|---------|-----------|------------|
| Conditional | `*ngIf` | `@if` |
| Loop | `*ngFor` | `@for` |
| Switch | `*ngSwitch` | `@switch` |
| Import Required | `CommonModule` | None (built-in) |
| Track By | `trackBy: fn` | `track expr` |
| Empty State | Separate `*ngIf` | `@empty` block |

## Performance Tips

1. **Always use trackBy** with *ngFor or track with @for
2. **Avoid complex expressions** in conditions
3. **Use @empty** for empty states with @for
4. **Prefer @if over *ngIf** in new projects
5. **Cache results** when using function calls in conditions

## Summary

Both syntaxes are valid and supported. The new control flow (@if, @for, @switch) offers:
- **Better performance**
- **Simpler syntax**
- **No imports needed**
- **Built-in empty state handling**

## Next Steps

- Learn about attribute directives (ngClass, ngStyle)
- Understand custom directives
- Master Angular Signals
- Explore template reference variables
