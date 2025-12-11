# Angular Lifecycle Hooks - Complete Guide

> Timestamp: **3:16:42** - Life Cycle Hooks

## Table of Contents
1. [Introduction](#introduction)
2. [Component Lifecycle Phases](#component-lifecycle-phases)
3. [All Lifecycle Hooks](#all-lifecycle-hooks)
4. [ngOnInit](#ngoninit)
5. [ngOnChanges](#ngonchanges)
6. [ngDoCheck](#ngdocheck)
7. [ngAfterViewInit & ngAfterViewChecked](#ngafterviewinit--ngafterviewchecked)
8. [ngAfterContentInit & ngAfterContentChecked](#ngaftercontentinit--ngaftercontentchecked)
9. [ngOnDestroy](#ngondestroy)
10. [Complete Example](#complete-example)

---

## Introduction

Lifecycle hooks allow you to tap into key moments in a component's lifecycle, from creation to destruction. They help you:
- Initialize component data
- React to input changes
- Perform cleanup operations
- Interact with DOM elements
- Manage subscriptions and resources

---

## Component Lifecycle Phases

```
Constructor
    ↓
ngOnChanges (if @Input properties)
    ↓
ngOnInit (once)
    ↓
ngDoCheck
    ↓
ngAfterContentInit (once)
    ↓
ngAfterContentChecked
    ↓
ngAfterViewInit (once)
    ↓
ngAfterViewChecked
    ↓
[Change Detection Cycle Repeats]
    ↓
ngOnDestroy (cleanup)
```

---

## All Lifecycle Hooks

| Hook | Purpose | Frequency | Use Case |
|------|---------|-----------|----------|
| `ngOnChanges` | Responds to @Input changes | Before ngOnInit and when inputs change | React to parent data changes |
| `ngOnInit` | Initialize component | Once after first ngOnChanges | Fetch data, setup subscriptions |
| `ngDoCheck` | Custom change detection | Every change detection | Manual dirty checking |
| `ngAfterContentInit` | After content projection | Once | Access projected content |
| `ngAfterContentChecked` | After content checked | After every check | React to projected content changes |
| `ngAfterViewInit` | After view initialization | Once | Access view children, DOM manipulation |
| `ngAfterViewChecked` | After view checked | After every check | React to view changes |
| `ngOnDestroy` | Before component destruction | Once | Cleanup subscriptions, timers |

---

## ngOnInit

**Purpose**: Initialize component logic after Angular sets up input properties.

**When**: Once, after the first `ngOnChanges`

**Use Cases**:
- Fetch data from services
- Initialize properties
- Setup subscriptions
- Complex initialization logic

```typescript
import { Component, OnInit, inject } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-user-profile',
  standalone: true,
  template: `
    <div class="profile">
      @if (loading) {
        <p>Loading...</p>
      } @else if (user) {
        <h2>{{ user.name }}</h2>
        <p>{{ user.email }}</p>
      }
    </div>
  `
})
export class UserProfileComponent implements OnInit {
  private userService = inject(UserService);
  
  user: any = null;
  loading = true;
  
  constructor() {
    console.log('Constructor: Component instance created');
    // Avoid complex logic here
  }
  
  ngOnInit(): void {
    console.log('ngOnInit: Fetching user data');
    
    // Good place for data fetching
    this.userService.getCurrentUser().subscribe(data => {
      this.user = data;
      this.loading = false;
    });
  }
}
```

**Best Practice**: Use `ngOnInit` instead of `constructor` for initialization logic that depends on input properties or services.

---

## ngOnChanges

**Purpose**: Respond when Angular sets or resets data-bound input properties.

**When**: Before `ngOnInit` and whenever @Input properties change

**Use Cases**:
- React to @Input changes
- Transform input data
- Trigger side effects based on new inputs

```typescript
import { Component, Input, OnChanges, SimpleChanges } from '@angular/core';

interface Product {
  id: number;
  name: string;
  price: number;
}

@Component({
  selector: 'app-product-detail',
  standalone: true,
  template: `
    <div class="product">
      <h3>{{ product?.name }}</h3>
      <p>Price: {{ product?.price | currency }}</p>
      <p>Discount: {{ discount }}%</p>
      <p class="final-price">Final: {{ finalPrice | currency }}</p>
      <p class="change-log">Changes: {{ changeCount }}</p>
    </div>
  `,
  styles: [`
    .product {
      border: 1px solid #ddd;
      padding: 1rem;
      border-radius: 8px;
    }
    
    .final-price {
      font-size: 1.5rem;
      font-weight: bold;
      color: #4caf50;
    }
    
    .change-log {
      color: #666;
      font-size: 0.875rem;
    }
  `]
})
export class ProductDetailComponent implements OnChanges {
  @Input() product: Product | null = null;
  @Input() discount: number = 0;
  
  finalPrice: number = 0;
  changeCount: number = 0;
  
  ngOnChanges(changes: SimpleChanges): void {
    console.log('ngOnChanges called:', changes);
    this.changeCount++;
    
    // Check which input changed
    if (changes['product']) {
      const product = changes['product'];
      console.log('Product changed:',
        'Previous:', product.previousValue,
        'Current:', product.currentValue,
        'First change:', product.firstChange
      );
    }
    
    if (changes['discount']) {
      const discount = changes['discount'];
      console.log('Discount changed from', discount.previousValue, 'to', discount.currentValue);
    }
    
    // Recalculate based on new inputs
    this.calculateFinalPrice();
  }
  
  private calculateFinalPrice(): void {
    if (this.product) {
      this.finalPrice = this.product.price * (1 - this.discount / 100);
    }
  }
}

// Parent component
@Component({
  selector: 'app-shop',
  standalone: true,
  imports: [ProductDetailComponent],
  template: `
    <div class="shop">
      <h2>Product Shop</h2>
      <app-product-detail 
        [product]="selectedProduct" 
        [discount]="currentDiscount"
      ></app-product-detail>
      
      <button (click)="changeProduct()">Change Product</button>
      <button (click)="changeDiscount()">Increase Discount</button>
    </div>
  `
})
export class ShopComponent {
  products: Product[] = [
    { id: 1, name: 'Laptop', price: 999 },
    { id: 2, name: 'Phone', price: 599 }
  ];
  
  selectedProduct = this.products[0];
  currentDiscount = 0;
  
  changeProduct(): void {
    this.selectedProduct = this.products[1];
  }
  
  changeDiscount(): void {
    this.currentDiscount += 10;
  }
}
```

---

## ngDoCheck

**Purpose**: Detect and act upon changes that Angular can't detect on its own.

**When**: During every change detection run

**Warning**: Use sparingly - runs very frequently and can impact performance.

```typescript
import { Component, DoCheck, Input } from '@angular/core';

@Component({
  selector: 'app-custom-change-detection',
  standalone: true,
  template: `
    <div class="detector">
      <h3>Custom Change Detection</h3>
      <p>Items: {{ items.length }}</p>
      <p>Checks: {{ checkCount }}</p>
      <ul>
        @for (item of items; track item) {
          <li>{{ item }}</li>
        }
      </ul>
    </div>
  `
})
export class CustomChangeDetectionComponent implements DoCheck {
  @Input() items: string[] = [];
  
  private previousLength = 0;
  checkCount = 0;
  
  ngDoCheck(): void {
    this.checkCount++;
    
    // Manual check for array changes
    if (this.items.length !== this.previousLength) {
      console.log('Array length changed from', this.previousLength, 'to', this.items.length);
      this.previousLength = this.items.length;
    }
  }
}
```

---

## ngAfterViewInit & ngAfterViewChecked

**Purpose**: Respond after Angular initializes/checks component's views and child views.

**Use Cases**:
- Access ViewChild elements
- DOM manipulation
- Initialize third-party libraries

```typescript
import { Component, ViewChild, ElementRef, AfterViewInit, AfterViewChecked } from '@angular/core';

@Component({
  selector: 'app-focus-input',
  standalone: true,
  template: `
    <div class="input-container">
      <label>Username:</label>
      <input #usernameInput type="text" placeholder="Enter username">
      
      <div #messageBox class="message-box">
        <p>Welcome! Please enter your username.</p>
      </div>
      
      <p>View checked: {{ checkCount }} times</p>
    </div>
  `,
  styles: [`
    .input-container {
      padding: 1rem;
      border: 1px solid #ddd;
      border-radius: 8px;
    }
    
    input {
      display: block;
      width: 100%;
      padding: 0.5rem;
      margin: 0.5rem 0;
      border: 2px solid #1976d2;
      border-radius: 4px;
    }
    
    input:focus {
      outline: none;
      border-color: #4caf50;
    }
    
    .message-box {
      background: #e3f2fd;
      padding: 1rem;
      border-radius: 4px;
      margin-top: 1rem;
    }
  `]
})
export class FocusInputComponent implements AfterViewInit, AfterViewChecked {
  @ViewChild('usernameInput') usernameInput!: ElementRef<HTMLInputElement>;
  @ViewChild('messageBox') messageBox!: ElementRef<HTMLDivElement>;
  
  checkCount = 0;
  
  ngAfterViewInit(): void {
    console.log('ngAfterViewInit: View is initialized');
    
    // Safe to access ViewChild here
    this.usernameInput.nativeElement.focus();
    
    // DOM manipulation
    this.messageBox.nativeElement.style.animation = 'fadeIn 0.5s';
    
    console.log('Input element:', this.usernameInput.nativeElement);
  }
  
  ngAfterViewChecked(): void {
    this.checkCount++;
    // Runs after every change detection check
    // Be careful with expensive operations here
  }
}
```

---

## ngAfterContentInit & ngAfterContentChecked

**Purpose**: Respond after Angular projects external content into component's view.

**Use Cases**:
- Access projected content via ContentChild
- Initialize projected components

```typescript
import { Component, ContentChild, ElementRef, AfterContentInit } from '@angular/core';

@Component({
  selector: 'app-card',
  standalone: true,
  template: `
    <div class="card">
      <div class="card-header">
        <ng-content select="[card-title]"></ng-content>
      </div>
      <div class="card-body">
        <ng-content></ng-content>
      </div>
      <div class="card-footer">
        <ng-content select="[card-footer]"></ng-content>
      </div>
    </div>
  `,
  styles: [`
    .card {
      border: 1px solid #ddd;
      border-radius: 8px;
      overflow: hidden;
    }
    
    .card-header {
      background: #1976d2;
      color: white;
      padding: 1rem;
      font-weight: bold;
    }
    
    .card-body {
      padding: 1rem;
    }
    
    .card-footer {
      background: #f5f5f5;
      padding: 0.5rem 1rem;
    }
  `]
})
export class CardComponent implements AfterContentInit {
  @ContentChild('cardTitle') cardTitle?: ElementRef;
  
  ngAfterContentInit(): void {
    console.log('ngAfterContentInit: Projected content is initialized');
    if (this.cardTitle) {
      console.log('Card title:', this.cardTitle.nativeElement.textContent);
    }
  }
}

// Usage
@Component({
  selector: 'app-example',
  standalone: true,
  imports: [CardComponent],
  template: `
    <app-card>
      <div card-title #cardTitle>My Card Title</div>
      <p>This is the card content</p>
      <div card-footer>Card footer content</div>
    </app-card>
  `
})
export class ExampleComponent { }
```

---

## ngOnDestroy

**Purpose**: Cleanup before Angular destroys the component.

**Use Cases**:
- Unsubscribe from Observables
- Detach event handlers
- Stop timers
- Clean up resources

```typescript
import { Component, OnInit, OnDestroy, inject, DestroyRef } from '@angular/core';
import { interval, Subscription } from 'rxjs';
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';
import { DataService } from './data.service';

@Component({
  selector: 'app-timer',
  standalone: true,
  template: `
    <div class="timer">
      <h3>Timer Component</h3>
      <p>Seconds: {{ seconds }}</p>
      <p>Data updates: {{ dataCount }}</p>
      <button (click)="toggleTimer()">
        {{ isRunning ? 'Pause' : 'Start' }}
      </button>
    </div>
  `,
  styles: [`
    .timer {
      padding: 2rem;
      border: 2px solid #1976d2;
      border-radius: 8px;
      text-align: center;
    }
    
    .timer p {
      font-size: 2rem;
      font-weight: bold;
      color: #1976d2;
    }
  `]
})
export class TimerComponent implements OnInit, OnDestroy {
  private dataService = inject(DataService);
  private destroyRef = inject(DestroyRef);
  
  seconds = 0;
  dataCount = 0;
  isRunning = false;
  
  // Traditional subscription (requires manual cleanup)
  private timerSubscription?: Subscription;
  
  // Alternative: Modern approach with takeUntilDestroyed
  private timer$ = interval(1000);
  
  ngOnInit(): void {
    console.log('Timer component initialized');
    
    // Method 1: Traditional subscription
    this.timerSubscription = this.timer$.subscribe(() => {
      if (this.isRunning) {
        this.seconds++;
      }
    });
    
    // Method 2: Using takeUntilDestroyed (automatically cleaned up)
    this.dataService.getData()
      .pipe(takeUntilDestroyed(this.destroyRef))
      .subscribe(data => {
        this.dataCount++;
        console.log('Data received:', data);
      });
  }
  
  toggleTimer(): void {
    this.isRunning = !this.isRunning;
  }
  
  ngOnDestroy(): void {
    console.log('Timer component destroyed - cleaning up');
    
    // Unsubscribe from subscriptions
    if (this.timerSubscription) {
      this.timerSubscription.unsubscribe();
    }
    
    // Clear any intervals/timeouts
    // Remove event listeners
    // Cancel pending HTTP requests
    
    console.log('Cleanup complete');
  }
}
```

**Modern Approach** (Angular 16+):

```typescript
import { Component, inject, DestroyRef } from '@angular/core';
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

@Component({
  selector: 'app-modern-cleanup',
  standalone: true,
  template: `<p>Modern cleanup approach</p>`
})
export class ModernCleanupComponent {
  private destroyRef = inject(DestroyRef);
  private dataService = inject(DataService);
  
  constructor() {
    // Automatically cleaned up when component is destroyed
    this.dataService.getData()
      .pipe(takeUntilDestroyed(this.destroyRef))
      .subscribe(data => {
        console.log(data);
      });
    
    // Register cleanup callback
    this.destroyRef.onDestroy(() => {
      console.log('Component is being destroyed');
    });
  }
}
```

---

## Complete Example: Lifecycle Logger

```typescript
import { 
  Component, 
  Input, 
  OnInit, 
  OnChanges, 
  DoCheck, 
  AfterContentInit, 
  AfterContentChecked, 
  AfterViewInit, 
  AfterViewChecked, 
  OnDestroy,
  SimpleChanges 
} from '@angular/core';

@Component({
  selector: 'app-lifecycle-demo',
  standalone: true,
  template: `
    <div class="lifecycle-demo">
      <h3>Lifecycle Demo Component</h3>
      <p>Name: {{ name }}</p>
      <p>Age: {{ age }}</p>
      <div class="log">
        <h4>Lifecycle Log:</h4>
        <ul>
          @for (log of logs; track $index) {
            <li>{{ log }}</li>
          }
        </ul>
      </div>
    </div>
  `,
  styles: [`
    .lifecycle-demo {
      border: 2px solid #1976d2;
      padding: 1rem;
      border-radius: 8px;
      margin: 1rem 0;
    }
    
    .log {
      background: #f5f5f5;
      padding: 1rem;
      border-radius: 4px;
      margin-top: 1rem;
      max-height: 300px;
      overflow-y: auto;
    }
    
    .log ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }
    
    .log li {
      padding: 0.25rem;
      font-family: monospace;
      font-size: 0.875rem;
      border-bottom: 1px solid #ddd;
    }
  `]
})
export class LifecycleDemoComponent
  implements OnInit, OnChanges, DoCheck, AfterContentInit, 
             AfterContentChecked, AfterViewInit, AfterViewChecked, OnDestroy {
  
  @Input() name: string = '';
  @Input() age: number = 0;
  
  logs: string[] = [];
  private checkCount = 0;
  
  constructor() {
    this.addLog('Constructor called');
  }
  
  ngOnChanges(changes: SimpleChanges): void {
    this.addLog(`ngOnChanges - ${JSON.stringify(changes)}`);
  }
  
  ngOnInit(): void {
    this.addLog('ngOnInit');
  }
  
  ngDoCheck(): void {
    this.checkCount++;
    this.addLog(`ngDoCheck (count: ${this.checkCount})`);
  }
  
  ngAfterContentInit(): void {
    this.addLog('ngAfterContentInit');
  }
  
  ngAfterContentChecked(): void {
    this.addLog('ngAfterContentChecked');
  }
  
  ngAfterViewInit(): void {
    this.addLog('ngAfterViewInit');
  }
  
  ngAfterViewChecked(): void {
    this.addLog('ngAfterViewChecked');
  }
  
  ngOnDestroy(): void {
    this.addLog('ngOnDestroy');
    console.log('Component destroyed with logs:', this.logs);
  }
  
  private addLog(message: string): void {
    const timestamp = new Date().toLocaleTimeString();
    this.logs.push(`[${timestamp}] ${message}`);
    console.log(message);
  }
}

// Parent component to test lifecycle
@Component({
  selector: 'app-lifecycle-tester',
  standalone: true,
  imports: [LifecycleDemoComponent],
  template: `
    <div class="tester">
      <h2>Lifecycle Hooks Tester</h2>
      
      <div class="controls">
        <label>
          Name: <input [(ngModel)]="name" type="text">
        </label>
        <label>
          Age: <input [(ngModel)]="age" type="number">
        </label>
        <button (click)="showComponent = !showComponent">
          {{ showComponent ? 'Destroy' : 'Create' }} Component
        </button>
      </div>
      
      @if (showComponent) {
        <app-lifecycle-demo [name]="name" [age]="age"></app-lifecycle-demo>
      }
    </div>
  `,
  styles: [`
    .tester {
      padding: 2rem;
    }
    
    .controls {
      display: flex;
      gap: 1rem;
      margin: 1rem 0;
      padding: 1rem;
      background: #e3f2fd;
      border-radius: 8px;
    }
    
    .controls label {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
    }
    
    .controls input {
      padding: 0.5rem;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    
    button {
      padding: 0.5rem 1rem;
      background: #1976d2;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
  `]
})
export class LifecycleTesterComponent {
  name = 'John Doe';
  age = 25;
  showComponent = true;
}
```

---

## Best Practices

### 1. Use ngOnInit for Initialization
```typescript
// ✅ Good
ngOnInit(): void {
  this.fetchData();
}

// ❌ Bad - avoid heavy logic in constructor
constructor() {
  this.fetchData(); // May fail if dependencies aren't ready
}
```

### 2. Always Unsubscribe
```typescript
// ✅ Modern approach
private destroyRef = inject(DestroyRef);

constructor() {
  this.dataService.getData()
    .pipe(takeUntilDestroyed(this.destroyRef))
    .subscribe(data => this.data = data);
}

// ✅ Traditional approach
private subscription?: Subscription;

ngOnInit(): void {
  this.subscription = this.dataService.getData().subscribe();
}

ngOnDestroy(): void {
  this.subscription?.unsubscribe();
}
```

### 3. Avoid Heavy Operations in ngDoCheck/ngAfterViewChecked
```typescript
// ❌ Bad - runs on every change detection
ngAfterViewChecked(): void {
  this.expensiveCalculation(); // Performance issue!
}

// ✅ Good - use specific hooks
ngAfterViewInit(): void {
  this.expensiveCalculation(); // Runs once
}
```

### 4. Access ViewChild After ngAfterViewInit
```typescript
@ViewChild('input') input!: ElementRef;

ngAfterViewInit(): void {
  // ✅ Safe - view is initialized
  this.input.nativeElement.focus();
}

ngOnInit(): void {
  // ❌ Won't work - view not ready yet
  // this.input.nativeElement.focus();
}
```

---

## Common Pitfalls

1. **Memory Leaks**: Not unsubscribing from Observables
2. **ExpressionChangedAfterItHasBeenCheckedError**: Modifying data in AfterViewChecked
3. **Accessing ViewChild Too Early**: Before ngAfterViewInit
4. **Heavy Operations**: In frequently called hooks like ngDoCheck
5. **Constructor Initialization**: Using @Input values in constructor (not available yet)

---

## Summary

✅ **ngOnChanges**: React to @Input changes

✅ **ngOnInit**: Initialize component (API calls, subscriptions)

✅ **ngAfterViewInit**: Access ViewChild elements, DOM manipulation

✅ **ngOnDestroy**: Cleanup subscriptions, timers, listeners

✅ **Modern Approach**: Use `takeUntilDestroyed()` for automatic cleanup

✅ **Best Practice**: Use specific hooks for specific purposes

Understanding lifecycle hooks helps you write efficient, bug-free Angular applications!
