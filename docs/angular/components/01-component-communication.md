# Component Communication in Angular

## Parent-Child Communication

### @Input() - Parent to Child

Pass data from parent to child component.

```typescript
// child.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `
    <div class="child">
      <h3>{{ title }}</h3>
      <p>{{ message }}</p>
      <p>Count: {{ count }}</p>
    </div>
  `
})
export class ChildComponent {
  @Input() title: string = '';
  @Input() message: string = '';
  @Input() count: number = 0;
}

// parent.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-parent',
  template: `
    <div class="parent">
      <h2>Parent Component</h2>
      <button (click)="incrementCounter()">Increment</button>
      
      <app-child 
        [title]="childTitle"
        [message]="childMessage"
        [count]="counter">
      </app-child>
    </div>
  `
})
export class ParentComponent {
  childTitle = 'Child Component';
  childMessage = 'Hello from Parent!';
  counter = 0;
  
  incrementCounter(): void {
    this.counter++;
  }
}
```

### @Input() with Getters/Setters

Intercept input value changes.

```typescript
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-user-profile',
  template: `
    <div>
      <h3>{{ displayName }}</h3>
      <p>Status: {{ userStatus }}</p>
    </div>
  `
})
export class UserProfileComponent {
  private _name: string = '';
  displayName: string = '';
  userStatus: string = 'inactive';
  
  @Input()
  set name(value: string) {
    this._name = value;
    this.displayName = value.trim().toUpperCase();
  }
  
  get name(): string {
    return this._name;
  }
  
  @Input()
  set isActive(value: boolean) {
    this.userStatus = value ? 'active' : 'inactive';
  }
}

// Usage
<app-user-profile 
  [name]="'john doe'" 
  [isActive]="true">
</app-user-profile>
```

### @Output() - Child to Parent

Emit events from child to parent component.

```typescript
// child.component.ts
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `
    <div class="child">
      <button (click)="sendMessage()">Send to Parent</button>
      <button (click)="sendData()">Send Data</button>
    </div>
  `
})
export class ChildComponent {
  @Output() messageEvent = new EventEmitter<string>();
  @Output() dataEvent = new EventEmitter<any>();
  
  sendMessage(): void {
    this.messageEvent.emit('Hello from Child!');
  }
  
  sendData(): void {
    this.dataEvent.emit({
      id: 1,
      name: 'John',
      timestamp: new Date()
    });
  }
}

// parent.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-parent',
  template: `
    <div class="parent">
      <h2>Parent Component</h2>
      <p>Message: {{ receivedMessage }}</p>
      <p *ngIf="receivedData">Data: {{ receivedData | json }}</p>
      
      <app-child 
        (messageEvent)="handleMessage($event)"
        (dataEvent)="handleData($event)">
      </app-child>
    </div>
  `
})
export class ParentComponent {
  receivedMessage: string = '';
  receivedData: any = null;
  
  handleMessage(message: string): void {
    this.receivedMessage = message;
  }
  
  handleData(data: any): void {
    this.receivedData = data;
    console.log('Received data:', data);
  }
}
```

## Template Reference Variables

Access child component from parent template.

```typescript
// child.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <div>
      <p>Count: {{ count }}</p>
      <button (click)="increment()">+</button>
      <button (click)="decrement()">-</button>
    </div>
  `
})
export class CounterComponent {
  count = 0;
  
  increment(): void {
    this.count++;
  }
  
  decrement(): void {
    this.count--;
  }
  
  reset(): void {
    this.count = 0;
  }
  
  setCount(value: number): void {
    this.count = value;
  }
}

// parent.component.ts
@Component({
  selector: 'app-parent',
  template: `
    <div>
      <app-counter #counterRef></app-counter>
      
      <!-- Access child methods -->
      <button (click)="counterRef.reset()">Reset from Parent</button>
      <button (click)="counterRef.setCount(100)">Set to 100</button>
      
      <!-- Access child properties -->
      <p>Current count in child: {{ counterRef.count }}</p>
    </div>
  `
})
export class ParentComponent {}
```

## @ViewChild - Programmatic Access

Access child component in parent TypeScript code.

```typescript
import { Component, ViewChild, AfterViewInit } from '@angular/core';
import { CounterComponent } from './counter.component';

@Component({
  selector: 'app-parent',
  template: `
    <div>
      <app-counter></app-counter>
      <button (click)="resetCounter()">Reset</button>
      <button (click)="incrementByTen()">+10</button>
    </div>
  `
})
export class ParentComponent implements AfterViewInit {
  @ViewChild(CounterComponent) counter!: CounterComponent;
  
  ngAfterViewInit(): void {
    // Access child after view initialization
    console.log('Initial count:', this.counter.count);
  }
  
  resetCounter(): void {
    this.counter.reset();
  }
  
  incrementByTen(): void {
    for (let i = 0; i < 10; i++) {
      this.counter.increment();
    }
  }
}
```

### @ViewChildren - Multiple Children

```typescript
import { Component, ViewChildren, QueryList, AfterViewInit } from '@angular/core';
import { ItemComponent } from './item.component';

@Component({
  selector: 'app-list',
  template: `
    <div>
      <app-item *ngFor="let item of items" [data]="item"></app-item>
      <button (click)="highlightAll()">Highlight All</button>
    </div>
  `
})
export class ListComponent implements AfterViewInit {
  @ViewChildren(ItemComponent) itemComponents!: QueryList<ItemComponent>;
  
  items = [
    { id: 1, name: 'Item 1' },
    { id: 2, name: 'Item 2' },
    { id: 3, name: 'Item 3' }
  ];
  
  ngAfterViewInit(): void {
    console.log('Number of items:', this.itemComponents.length);
  }
  
  highlightAll(): void {
    this.itemComponents.forEach(item => item.highlight());
  }
}
```

## Service-Based Communication

Share data between unrelated components using services.

```typescript
// message.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class MessageService {
  private messageSource = new BehaviorSubject<string>('');
  currentMessage: Observable<string> = this.messageSource.asObservable();
  
  private dataSource = new BehaviorSubject<any>(null);
  currentData: Observable<any> = this.dataSource.asObservable();
  
  sendMessage(message: string): void {
    this.messageSource.next(message);
  }
  
  sendData(data: any): void {
    this.dataSource.next(data);
  }
  
  clearMessage(): void {
    this.messageSource.next('');
  }
}

// sender.component.ts
import { Component } from '@angular/core';
import { MessageService } from './message.service';

@Component({
  selector: 'app-sender',
  template: `
    <div>
      <h3>Sender Component</h3>
      <input [(ngModel)]="message" placeholder="Enter message">
      <button (click)="send()">Send</button>
    </div>
  `
})
export class SenderComponent {
  message = '';
  
  constructor(private messageService: MessageService) {}
  
  send(): void {
    this.messageService.sendMessage(this.message);
    this.message = '';
  }
}

// receiver.component.ts
import { Component, OnInit, OnDestroy } from '@angular/core';
import { MessageService } from './message.service';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-receiver',
  template: `
    <div>
      <h3>Receiver Component</h3>
      <p *ngIf="receivedMessage">Message: {{ receivedMessage }}</p>
    </div>
  `
})
export class ReceiverComponent implements OnInit, OnDestroy {
  receivedMessage: string = '';
  private subscription!: Subscription;
  
  constructor(private messageService: MessageService) {}
  
  ngOnInit(): void {
    this.subscription = this.messageService.currentMessage.subscribe(
      message => this.receivedMessage = message
    );
  }
  
  ngOnDestroy(): void {
    this.subscription.unsubscribe();
  }
}
```

## Real-World Example: Product Management

### product.model.ts

```typescript
export interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
  inStock: boolean;
  imageUrl: string;
}

export interface CartItem {
  product: Product;
  quantity: number;
}
```

### product.service.ts

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';
import { Product, CartItem } from './product.model';

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private products: Product[] = [
    {
      id: 1,
      name: 'Laptop',
      price: 999,
      description: 'High-performance laptop',
      inStock: true,
      imageUrl: 'assets/laptop.jpg'
    },
    {
      id: 2,
      name: 'Mouse',
      price: 29,
      description: 'Wireless mouse',
      inStock: true,
      imageUrl: 'assets/mouse.jpg'
    },
    {
      id: 3,
      name: 'Keyboard',
      price: 79,
      description: 'Mechanical keyboard',
      inStock: false,
      imageUrl: 'assets/keyboard.jpg'
    }
  ];
  
  private cartSubject = new BehaviorSubject<CartItem[]>([]);
  cart$: Observable<CartItem[]> = this.cartSubject.asObservable();
  
  getProducts(): Product[] {
    return this.products;
  }
  
  getProduct(id: number): Product | undefined {
    return this.products.find(p => p.id === id);
  }
  
  addToCart(product: Product, quantity: number = 1): void {
    const cart = this.cartSubject.value;
    const existingItem = cart.find(item => item.product.id === product.id);
    
    if (existingItem) {
      existingItem.quantity += quantity;
    } else {
      cart.push({ product, quantity });
    }
    
    this.cartSubject.next([...cart]);
  }
  
  removeFromCart(productId: number): void {
    const cart = this.cartSubject.value.filter(
      item => item.product.id !== productId
    );
    this.cartSubject.next(cart);
  }
  
  updateQuantity(productId: number, quantity: number): void {
    const cart = this.cartSubject.value;
    const item = cart.find(i => i.product.id === productId);
    
    if (item) {
      item.quantity = quantity;
      this.cartSubject.next([...cart]);
    }
  }
  
  clearCart(): void {
    this.cartSubject.next([]);
  }
  
  getCartTotal(): number {
    return this.cartSubject.value.reduce(
      (total, item) => total + (item.product.price * item.quantity),
      0
    );
  }
}
```

### product-card.component.ts

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { Product } from '../product.model';

@Component({
  selector: 'app-product-card',
  templateUrl: './product-card.component.html',
  styleUrls: ['./product-card.component.css']
})
export class ProductCardComponent {
  @Input() product!: Product;
  @Output() addToCartEvent = new EventEmitter<Product>();
  
  quantity = 1;
  
  addToCart(): void {
    if (this.product.inStock) {
      this.addToCartEvent.emit(this.product);
    }
  }
  
  incrementQuantity(): void {
    this.quantity++;
  }
  
  decrementQuantity(): void {
    if (this.quantity > 1) {
      this.quantity--;
    }
  }
}
```

### product-card.component.html

```html
<div class="product-card">
  <img [src]="product.imageUrl" [alt]="product.name">
  <div class="product-info">
    <h3>{{ product.name }}</h3>
    <p class="description">{{ product.description }}</p>
    <p class="price">${{ product.price }}</p>
    
    <div class="stock-status">
      <span [class.in-stock]="product.inStock" 
            [class.out-of-stock]="!product.inStock">
        {{ product.inStock ? 'In Stock' : 'Out of Stock' }}
      </span>
    </div>
    
    <div class="quantity-selector" *ngIf="product.inStock">
      <button (click)="decrementQuantity()">-</button>
      <span>{{ quantity }}</span>
      <button (click)="incrementQuantity()">+</button>
    </div>
    
    <button 
      class="add-to-cart-btn"
      [disabled]="!product.inStock"
      (click)="addToCart()">
      Add to Cart
    </button>
  </div>
</div>
```

### product-list.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from '../product.service';
import { Product } from '../product.model';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent implements OnInit {
  products: Product[] = [];
  
  constructor(private productService: ProductService) {}
  
  ngOnInit(): void {
    this.products = this.productService.getProducts();
  }
  
  handleAddToCart(product: Product): void {
    this.productService.addToCart(product);
    console.log(`Added ${product.name} to cart`);
  }
}
```

### product-list.component.html

```html
<div class="product-list">
  <h2>Products</h2>
  
  <div class="products-grid">
    <app-product-card 
      *ngFor="let product of products"
      [product]="product"
      (addToCartEvent)="handleAddToCart($event)">
    </app-product-card>
  </div>
</div>
```

### shopping-cart.component.ts

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { ProductService } from '../product.service';
import { CartItem } from '../product.model';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-shopping-cart',
  templateUrl: './shopping-cart.component.html',
  styleUrls: ['./shopping-cart.component.css']
})
export class ShoppingCartComponent implements OnInit, OnDestroy {
  cartItems: CartItem[] = [];
  total = 0;
  private cartSubscription!: Subscription;
  
  constructor(private productService: ProductService) {}
  
  ngOnInit(): void {
    this.cartSubscription = this.productService.cart$.subscribe(items => {
      this.cartItems = items;
      this.total = this.productService.getCartTotal();
    });
  }
  
  ngOnDestroy(): void {
    this.cartSubscription.unsubscribe();
  }
  
  removeItem(productId: number): void {
    this.productService.removeFromCart(productId);
  }
  
  updateQuantity(productId: number, quantity: number): void {
    if (quantity > 0) {
      this.productService.updateQuantity(productId, quantity);
    }
  }
  
  clearCart(): void {
    this.productService.clearCart();
  }
  
  checkout(): void {
    console.log('Proceeding to checkout...');
    console.log('Cart items:', this.cartItems);
    console.log('Total:', this.total);
    // Implement checkout logic
  }
}
```

### shopping-cart.component.html

```html
<div class="shopping-cart">
  <h2>Shopping Cart</h2>
  
  <div *ngIf="cartItems.length === 0" class="empty-cart">
    <p>Your cart is empty</p>
  </div>
  
  <div *ngIf="cartItems.length > 0">
    <div class="cart-items">
      <div *ngFor="let item of cartItems" class="cart-item">
        <img [src]="item.product.imageUrl" [alt]="item.product.name">
        <div class="item-details">
          <h3>{{ item.product.name }}</h3>
          <p class="price">${{ item.product.price }}</p>
          
          <div class="quantity-controls">
            <label>Quantity:</label>
            <input 
              type="number" 
              [value]="item.quantity"
              (change)="updateQuantity(item.product.id, +$any($event.target).value)"
              min="1">
          </div>
          
          <p class="subtotal">
            Subtotal: ${{ item.product.price * item.quantity }}
          </p>
        </div>
        
        <button 
          class="remove-btn"
          (click)="removeItem(item.product.id)">
          Remove
        </button>
      </div>
    </div>
    
    <div class="cart-summary">
      <h3>Cart Total: ${{ total }}</h3>
      <button class="checkout-btn" (click)="checkout()">
        Proceed to Checkout
      </button>
      <button class="clear-btn" (click)="clearCart()">
        Clear Cart
      </button>
    </div>
  </div>
</div>
```

## Best Practices

### 1. Use @Input() and @Output() for Direct Parent-Child

```typescript
// Good - Clear parent-child relationship
<app-child 
  [data]="parentData"
  (dataChange)="handleChange($event)">
</app-child>
```

### 2. Use Services for Sibling/Distant Components

```typescript
// Good - Shared state management
@Injectable({ providedIn: 'root' })
export class StateService {
  private state$ = new BehaviorSubject<any>(null);
  // ...
}
```

### 3. Unsubscribe from Observables

```typescript
// Good - Prevent memory leaks
export class MyComponent implements OnDestroy {
  private subscription: Subscription;
  
  ngOnDestroy(): void {
    this.subscription.unsubscribe();
  }
}

// Better - Use async pipe in template
<div>{{ data$ | async }}</div>
```

### 4. Type Your Emissions

```typescript
// Good - Type safety
@Output() userSelected = new EventEmitter<User>();
@Output() statusChanged = new EventEmitter<'active' | 'inactive'>();
```

## Summary

| Method | Use Case | Direction |
|--------|----------|-----------|
| `@Input()` | Pass data to child | Parent → Child |
| `@Output()` | Emit events to parent | Child → Parent |
| Template Ref | Access child in template | Parent → Child |
| `@ViewChild` | Access child in code | Parent → Child |
| Service | Share data globally | Any → Any |

## Next Steps

- Learn about RxJS and Observables
- Understand state management patterns
- Explore NgRx for complex state
- Master async pipe and subscription management
