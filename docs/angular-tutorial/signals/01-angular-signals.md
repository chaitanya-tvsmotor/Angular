# Angular Signals - Reactive State Management

## What are Signals?

Signals are Angular's new reactive primitive (Angular 16+) that provides fine-grained reactivity for managing state changes. They automatically track dependencies and notify consumers when values change.

## Why Signals?

- **Fine-grained reactivity**: Only update what changed
- **Better performance**: Reduced change detection cycles
- **Simpler mental model**: Explicit dependencies
- **Better DX**: TypeScript-friendly, easier debugging

## Creating Signals

### Basic Signal Creation

```typescript
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-signals-basic',
  standalone: true,
  template: `
    <div>
      <h2>Basic Signals</h2>
      
      <!-- Reading signal values -->
      <p>Count: {{ count() }}</p>
      <p>Name: {{ name() }}</p>
      <p>Is Active: {{ isActive() }}</p>
      
      <!-- Updating signals -->
      <button (click)="increment()">Increment</button>
      <button (click)="decrement()">Decrement</button>
      <button (click)="reset()">Reset</button>
      
      <input 
        [value]="name()"
        (input)="updateName($event)"
        placeholder="Enter name">
      
      <button (click)="toggleActive()">
        {{ isActive() ? 'Deactivate' : 'Activate' }}
      </button>
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
    
    input {
      padding: 10px;
      margin: 10px 0;
      width: 300px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
  `]
})
export class SignalsBasicComponent {
  // Creating signals
  count = signal(0);
  name = signal('John Doe');
  isActive = signal(true);
  
  // Updating signals
  increment(): void {
    this.count.set(this.count() + 1);  // set() - replace value
  }
  
  decrement(): void {
    this.count.update(value => value - 1);  // update() - transform value
  }
  
  reset(): void {
    this.count.set(0);
  }
  
  updateName(event: Event): void {
    const target = event.target as HTMLInputElement;
    this.name.set(target.value);
  }
  
  toggleActive(): void {
    this.isActive.update(value => !value);
  }
}
```

## Signal Methods

### set() - Replace Value

```typescript
const count = signal(0);
count.set(10);  // count is now 10
```

### update() - Transform Value

```typescript
const count = signal(0);
count.update(value => value + 1);  // count is now 1
count.update(value => value * 2);  // count is now 2
```

### mutate() - Modify Objects/Arrays

```typescript
const user = signal({ name: 'John', age: 30 });

// Don't do this (breaks reactivity)
user().age = 31;

// Do this instead
user.update(u => ({ ...u, age: 31 }));

// Or for arrays
const items = signal([1, 2, 3]);
items.update(arr => [...arr, 4]);
```

## Computed Signals

Derived values that automatically update when dependencies change.

```typescript
import { Component, signal, computed } from '@angular/core';

@Component({
  selector: 'app-computed-signals',
  standalone: true,
  template: `
    <div>
      <h2>Computed Signals</h2>
      
      <!-- Shopping Cart Example -->
      <div class="cart">
        <h3>Shopping Cart</h3>
        
        <div *ngFor="let item of items()" class="item">
          <span>{{ item.name }} - ${{ item.price }}</span>
          <input 
            type="number" 
            [value]="item.quantity"
            (input)="updateQuantity(item.id, $event)"
            min="0">
          <button (click)="removeItem(item.id)">Remove</button>
        </div>
        
        <button (click)="addItem()">Add Item</button>
        
        <div class="summary">
          <p>Total Items: {{ totalItems() }}</p>
          <p>Subtotal: ${{ subtotal() }}</p>
          <p>Tax (10%): ${{ tax() }}</p>
          <p><strong>Total: ${{ total() }}</strong></p>
        </div>
      </div>
    </div>
  `,
  styles: [`
    .cart {
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 8px;
      max-width: 500px;
    }
    
    .item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px;
      margin: 10px 0;
      background: #f8f9fa;
      border-radius: 4px;
    }
    
    .summary {
      margin-top: 20px;
      padding: 15px;
      background: #e9ecef;
      border-radius: 4px;
    }
    
    button {
      padding: 8px 16px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    input {
      width: 60px;
      padding: 5px;
    }
  `]
})
export class ComputedSignalsComponent {
  items = signal([
    { id: 1, name: 'Laptop', price: 999, quantity: 1 },
    { id: 2, name: 'Mouse', price: 29, quantity: 2 },
    { id: 3, name: 'Keyboard', price: 79, quantity: 1 }
  ]);
  
  // Computed signals automatically update
  totalItems = computed(() => {
    return this.items().reduce((sum, item) => sum + item.quantity, 0);
  });
  
  subtotal = computed(() => {
    return this.items().reduce((sum, item) => 
      sum + (item.price * item.quantity), 0
    );
  });
  
  tax = computed(() => {
    return Number((this.subtotal() * 0.1).toFixed(2));
  });
  
  total = computed(() => {
    return Number((this.subtotal() + this.tax()).toFixed(2));
  });
  
  updateQuantity(id: number, event: Event): void {
    const target = event.target as HTMLInputElement;
    const quantity = parseInt(target.value) || 0;
    
    this.items.update(items =>
      items.map(item =>
        item.id === id ? { ...item, quantity } : item
      )
    );
  }
  
  removeItem(id: number): void {
    this.items.update(items => items.filter(item => item.id !== id));
  }
  
  addItem(): void {
    const newId = Math.max(...this.items().map(i => i.id)) + 1;
    this.items.update(items => [
      ...items,
      { id: newId, name: `Item ${newId}`, price: 50, quantity: 1 }
    ]);
  }
}
```

## Effects

Side effects that run when signal dependencies change.

```typescript
import { Component, signal, effect } from '@angular/core';

@Component({
  selector: 'app-effects-demo',
  standalone: true,
  template: `
    <div>
      <h2>Effects Example</h2>
      
      <input 
        [(ngModel)]="searchTerm"
        (ngModelChange)="searchSignal.set($event)"
        placeholder="Search...">
      
      <p>Search term: {{ searchSignal() }}</p>
      <p>Character count: {{ searchSignal().length }}</p>
      
      <!-- Theme switcher -->
      <button (click)="toggleTheme()">
        Current Theme: {{ theme() }}
      </button>
      
      <!-- Counter with effect -->
      <div>
        <p>Count: {{ counter() }}</p>
        <button (click)="counter.update(v => v + 1)">Increment</button>
        <p>Effect ran {{ effectRunCount }} times</p>
      </div>
    </div>
  `
})
export class EffectsDemoComponent {
  searchSignal = signal('');
  searchTerm = '';
  theme = signal<'light' | 'dark'>('light');
  counter = signal(0);
  effectRunCount = 0;
  
  constructor() {
    // Effect runs whenever searchSignal changes
    effect(() => {
      const search = this.searchSignal();
      console.log('Search changed:', search);
      // Could trigger API call here
    });
    
    // Effect for theme changes
    effect(() => {
      const currentTheme = this.theme();
      document.body.className = currentTheme;
      localStorage.setItem('theme', currentTheme);
    });
    
    // Effect for counter
    effect(() => {
      const count = this.counter();
      this.effectRunCount++;
      console.log('Counter changed:', count);
    });
  }
  
  toggleTheme(): void {
    this.theme.update(t => t === 'light' ? 'dark' : 'light');
  }
}
```

## linkedSignal() - Angular 19+

Create signals that are linked to other signals with transformation logic.

```typescript
import { Component, signal, linkedSignal } from '@angular/core';

@Component({
  selector: 'app-linked-signals',
  standalone: true,
  template: `
    <div>
      <h2>Linked Signals</h2>
      
      <!-- Temperature Converter -->
      <div class="converter">
        <h3>Temperature Converter</h3>
        
        <div>
          <label>Celsius:</label>
          <input 
            type="number"
            [value]="celsius()"
            (input)="setCelsius($event)">
        </div>
        
        <div>
          <label>Fahrenheit:</label>
          <input 
            type="number"
            [value]="fahrenheit()"
            (input)="setFahrenheit($event)">
        </div>
        
        <div>
          <label>Kelvin:</label>
          <input 
            type="number"
            [value]="kelvin()"
            (input)="setKelvin($event)">
        </div>
      </div>
      
      <!-- User Profile with derived data -->
      <div class="profile">
        <h3>User Profile</h3>
        
        <input 
          [value]="firstName()"
          (input)="setFirstName($event)"
          placeholder="First Name">
        
        <input 
          [value]="lastName()"
          (input)="setLastName($event)"
          placeholder="Last Name">
        
        <p>Full Name: {{ fullName() }}</p>
        <p>Initials: {{ initials() }}</p>
        <p>Username: {{ username() }}</p>
      </div>
    </div>
  `,
  styles: [`
    .converter, .profile {
      padding: 20px;
      margin: 20px 0;
      border: 1px solid #ddd;
      border-radius: 8px;
    }
    
    label {
      display: inline-block;
      width: 100px;
      font-weight: bold;
    }
    
    input {
      width: 200px;
      padding: 8px;
      margin: 10px 0;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
  `]
})
export class LinkedSignalsComponent {
  // Temperature signals
  celsius = signal(0);
  
  // Linked signals automatically update
  fahrenheit = linkedSignal(() => this.celsius() * 9/5 + 32);
  kelvin = linkedSignal(() => this.celsius() + 273.15);
  
  setCelsius(event: Event): void {
    const value = (event.target as HTMLInputElement).value;
    this.celsius.set(parseFloat(value) || 0);
  }
  
  setFahrenheit(event: Event): void {
    const value = (event.target as HTMLInputElement).value;
    const f = parseFloat(value) || 0;
    this.celsius.set((f - 32) * 5/9);
  }
  
  setKelvin(event: Event): void {
    const value = (event.target as HTMLInputElement).value;
    const k = parseFloat(value) || 0;
    this.celsius.set(k - 273.15);
  }
  
  // User profile signals
  firstName = signal('John');
  lastName = signal('Doe');
  
  fullName = linkedSignal(() => `${this.firstName()} ${this.lastName()}`);
  
  initials = linkedSignal(() => {
    return `${this.firstName()[0]}${this.lastName()[0]}`.toUpperCase();
  });
  
  username = linkedSignal(() => {
    return `${this.firstName().toLowerCase()}.${this.lastName().toLowerCase()}`;
  });
  
  setFirstName(event: Event): void {
    this.firstName.set((event.target as HTMLInputElement).value);
  }
  
  setLastName(event: Event): void {
    this.lastName.set((event.target as HTMLInputElement).value);
  }
}
```

## Complete Example: Todo App with Signals

```typescript
import { Component, signal, computed } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

interface Todo {
  id: number;
  text: string;
  completed: boolean;
  createdAt: Date;
}

@Component({
  selector: 'app-todo-signals',
  standalone: true,
  imports: [CommonModule, FormsModule],
  template: `
    <div class="todo-app">
      <h1>Todo App with Signals</h1>
      
      <!-- Add Todo -->
      <div class="add-todo">
        <input 
          [(ngModel)]="newTodoText"
          (keyup.enter)="addTodo()"
          placeholder="What needs to be done?">
        <button (click)="addTodo()">Add</button>
      </div>
      
      <!-- Filter -->
      <div class="filters">
        <button 
          [class.active]="filter() === 'all'"
          (click)="filter.set('all')">
          All ({{ todos().length }})
        </button>
        <button 
          [class.active]="filter() === 'active'"
          (click)="filter.set('active')">
          Active ({{ activeTodosCount() }})
        </button>
        <button 
          [class.active]="filter() === 'completed'"
          (click)="filter.set('completed')">
          Completed ({{ completedTodosCount() }})
        </button>
      </div>
      
      <!-- Todo List -->
      <div class="todo-list">
        @for (todo of filteredTodos(); track todo.id) {
          <div class="todo-item" [class.completed]="todo.completed">
            <input 
              type="checkbox"
              [checked]="todo.completed"
              (change)="toggleTodo(todo.id)">
            <span class="todo-text">{{ todo.text }}</span>
            <button class="delete-btn" (click)="deleteTodo(todo.id)">×</button>
          </div>
        } @empty {
          <p class="empty-message">No todos to display</p>
        }
      </div>
      
      <!-- Stats -->
      <div class="stats">
        <p>{{ activeTodosCount() }} {{ activeTodosCount() === 1 ? 'item' : 'items' }} left</p>
        @if (completedTodosCount() > 0) {
          <button (click)="clearCompleted()">Clear completed</button>
        }
      </div>
    </div>
  `,
  styles: [`
    .todo-app {
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
      padding: 12px;
      border: 1px solid #ddd;
      border-radius: 4px;
      font-size: 16px;
    }
    
    button {
      padding: 10px 20px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    button.active {
      background: #0056b3;
    }
    
    .filters {
      display: flex;
      gap: 10px;
      margin-bottom: 20px;
    }
    
    .todo-item {
      display: flex;
      align-items: center;
      padding: 12px;
      border-bottom: 1px solid #eee;
      transition: background 0.3s;
    }
    
    .todo-item:hover {
      background: #f8f9fa;
    }
    
    .todo-item.completed .todo-text {
      text-decoration: line-through;
      color: #999;
    }
    
    .todo-text {
      flex: 1;
      margin: 0 15px;
    }
    
    .delete-btn {
      background: #dc3545;
      width: 30px;
      height: 30px;
      border-radius: 50%;
      font-size: 20px;
      line-height: 1;
      padding: 0;
    }
    
    .empty-message {
      text-align: center;
      color: #999;
      padding: 40px;
    }
    
    .stats {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 20px;
      padding-top: 20px;
      border-top: 1px solid #eee;
    }
  `]
})
export class TodoSignalsComponent {
  // State
  todos = signal<Todo[]>([
    { id: 1, text: 'Learn Angular Signals', completed: false, createdAt: new Date() },
    { id: 2, text: 'Build a project', completed: false, createdAt: new Date() }
  ]);
  
  filter = signal<'all' | 'active' | 'completed'>('all');
  newTodoText = '';
  nextId = 3;
  
  // Computed values
  activeTodosCount = computed(() => 
    this.todos().filter(t => !t.completed).length
  );
  
  completedTodosCount = computed(() =>
    this.todos().filter(t => t.completed).length
  );
  
  filteredTodos = computed(() => {
    const filterValue = this.filter();
    const allTodos = this.todos();
    
    switch (filterValue) {
      case 'active':
        return allTodos.filter(t => !t.completed);
      case 'completed':
        return allTodos.filter(t => t.completed);
      default:
        return allTodos;
    }
  });
  
  // Actions
  addTodo(): void {
    if (this.newTodoText.trim()) {
      this.todos.update(todos => [
        ...todos,
        {
          id: this.nextId++,
          text: this.newTodoText,
          completed: false,
          createdAt: new Date()
        }
      ]);
      this.newTodoText = '';
    }
  }
  
  toggleTodo(id: number): void {
    this.todos.update(todos =>
      todos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  }
  
  deleteTodo(id: number): void {
    this.todos.update(todos => todos.filter(t => t.id !== id));
  }
  
  clearCompleted(): void {
    this.todos.update(todos => todos.filter(t => !t.completed));
  }
}
```

## Signals vs RxJS

| Feature | Signals | RxJS Observables |
|---------|---------|------------------|
| Learning Curve | Easier | Steeper |
| Performance | Better (fine-grained) | Good |
| Async | No (sync only) | Yes (async) |
| Composition | `computed()` | Operators |
| Side Effects | `effect()` | `tap()`, `subscribe()` |
| Use Case | Simple state | Complex async flows |

## Best Practices

1. **Use signals for local state** management
2. **Use computed()** for derived values
3. **Use effect()** sparingly (for side effects only)
4. **Don't mutate** signal values directly
5. **Prefer signals over RxJS** for simple state
6. **Use RxJS** for complex async operations
7. **Keep signals focused** on single concerns

## Summary

Signals provide a simple, performant way to manage reactive state in Angular applications. They're especially useful for:
- Local component state
- Derived/computed values
- Simple state management
- Replacing simple RxJS observables

## Next Steps

- Learn about routing and navigation
- Master lifecycle hooks
- Explore advanced state management patterns
- Combine signals with RxJS when needed
