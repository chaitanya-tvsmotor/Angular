# Angular Introduction

## What is Angular?

Angular is a **platform and framework** for building single-page client applications using HTML, CSS, and TypeScript. Developed and maintained by Google, Angular is a complete rewrite from AngularJS (Angular 1.x).

### Key Features

1. **Component-Based Architecture**: Build encapsulated components that manage their own state
2. **TypeScript**: Built with TypeScript for better tooling and type safety
3. **Dependency Injection**: Built-in DI system for better code organization
4. **Two-Way Data Binding**: Automatic synchronization between model and view
5. **RxJS Integration**: Reactive programming with Observables
6. **Powerful CLI**: Command-line interface for scaffolding and building
7. **Cross-Platform**: Build for web, mobile, and desktop

## Angular Architecture

```
┌─────────────────────────────────────┐
│          Angular Application        │
├─────────────────────────────────────┤
│                                     │
│  ┌──────────┐  ┌──────────┐        │
│  │Component │  │Component │        │
│  │          │  │          │        │
│  │ Template │  │ Template │        │
│  │  Styles  │  │  Styles  │        │
│  └────┬─────┘  └────┬─────┘        │
│       │             │              │
│       └──────┬──────┘              │
│              │                     │
│       ┌──────▼─────┐               │
│       │  Services  │               │
│       │            │               │
│       │ (Business  │               │
│       │   Logic)   │               │
│       └──────┬─────┘               │
│              │                     │
│       ┌──────▼─────┐               │
│       │   Modules  │               │
│       │            │               │
│       │(Organizing │               │
│       │   Units)   │               │
│       └────────────┘               │
│                                     │
└─────────────────────────────────────┘
```

## Setting Up Angular

### Prerequisites

```bash
# Install Node.js (v18 or later recommended)
# Download from: https://nodejs.org/

# Verify installation
node --version
npm --version
```

### Install Angular CLI

```bash
# Install Angular CLI globally
npm install -g @angular/cli

# Verify installation
ng version
```

### Create Your First Angular App

```bash
# Create new Angular application
ng new my-first-app

# Options:
# - Would you like to add Angular routing? (y/N): y
# - Which stylesheet format would you like to use? CSS/SCSS/SASS/LESS

# Navigate to project
cd my-first-app

# Serve the application
ng serve

# Open browser at http://localhost:4200
```

## Project Structure

```
my-first-app/
├── node_modules/          # Dependencies
├── src/                   # Source code
│   ├── app/              # Application code
│   │   ├── app.component.ts      # Root component
│   │   ├── app.component.html    # Root template
│   │   ├── app.component.css     # Root styles
│   │   ├── app.component.spec.ts # Tests
│   │   └── app.module.ts         # Root module
│   ├── assets/           # Static files
│   ├── environments/     # Environment configs
│   ├── index.html        # Main HTML file
│   ├── main.ts          # Application entry point
│   └── styles.css       # Global styles
├── angular.json          # Angular CLI configuration
├── package.json          # npm dependencies
├── tsconfig.json         # TypeScript configuration
└── README.md
```

## Your First Component

### app.component.ts

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',           // HTML tag to use this component
  templateUrl: './app.component.html',  // Template file
  styleUrls: ['./app.component.css']    // Style file(s)
})
export class AppComponent {
  // Component properties
  title = 'my-first-app';
  message = 'Welcome to Angular!';
  count = 0;
  
  // Component methods
  incrementCount(): void {
    this.count++;
  }
  
  resetCount(): void {
    this.count = 0;
  }
}
```

### app.component.html

```html
<div class="container">
  <h1>{{ title }}</h1>
  <p>{{ message }}</p>
  
  <div class="counter">
    <p>Count: {{ count }}</p>
    <button (click)="incrementCount()">Increment</button>
    <button (click)="resetCount()">Reset</button>
  </div>
</div>
```

### app.component.css

```css
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

.counter {
  margin-top: 20px;
}

button {
  margin: 0 10px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  border: none;
  border-radius: 4px;
  background-color: #3f51b5;
  color: white;
}

button:hover {
  background-color: #303f9f;
}
```

## Core Concepts

### 1. Components

The building blocks of Angular applications.

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-greeting',
  template: `
    <div>
      <h2>Hello, {{ userName }}!</h2>
      <button (click)="sayGoodbye()">Goodbye</button>
    </div>
  `,
  styles: [`
    h2 { color: #3f51b5; }
    button { margin-top: 10px; }
  `]
})
export class GreetingComponent {
  userName = 'Angular Developer';
  
  sayGoodbye(): void {
    alert(`Goodbye, ${this.userName}!`);
  }
}
```

### 2. Templates

HTML with Angular-specific syntax.

```html
<!-- Interpolation -->
<p>Welcome, {{ userName }}</p>

<!-- Property Binding -->
<img [src]="imageUrl" [alt]="imageAlt">

<!-- Event Binding -->
<button (click)="handleClick()">Click Me</button>

<!-- Two-Way Binding -->
<input [(ngModel)]="userName">

<!-- Structural Directives -->
<div *ngIf="isLoggedIn">Welcome back!</div>

<ul>
  <li *ngFor="let item of items">{{ item }}</li>
</ul>
```

### 3. Directives

Modify DOM behavior.

```typescript
// Built-in directives
<div *ngIf="showContent">Content visible</div>
<div *ngFor="let item of items">{{ item }}</div>
<div [ngClass]="{'active': isActive}">Styled element</div>
<div [ngStyle]="{'color': textColor}">Colored text</div>

// Custom directive example
import { Directive, ElementRef, HostListener } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(private el: ElementRef) {}
  
  @HostListener('mouseenter')
  onMouseEnter(): void {
    this.el.nativeElement.style.backgroundColor = 'yellow';
  }
  
  @HostListener('mouseleave')
  onMouseLeave(): void {
    this.el.nativeElement.style.backgroundColor = '';
  }
}

// Usage
<p appHighlight>Hover over me!</p>
```

### 4. Services

Share data and logic across components.

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'  // Available app-wide
})
export class DataService {
  private data: string[] = [];
  
  addData(item: string): void {
    this.data.push(item);
  }
  
  getData(): string[] {
    return this.data;
  }
  
  clearData(): void {
    this.data = [];
  }
}

// Using in a component
import { Component } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-data-display',
  template: `
    <ul>
      <li *ngFor="let item of items">{{ item }}</li>
    </ul>
  `
})
export class DataDisplayComponent {
  items: string[] = [];
  
  constructor(private dataService: DataService) {
    this.items = this.dataService.getData();
  }
}
```

### 5. Modules

Organize application into cohesive blocks.

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { GreetingComponent } from './greeting/greeting.component';
import { DataDisplayComponent } from './data-display/data-display.component';

@NgModule({
  declarations: [
    AppComponent,
    GreetingComponent,
    DataDisplayComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Data Binding

### Interpolation

```typescript
@Component({
  template: `<p>{{ message }}</p>`
})
export class MyComponent {
  message = 'Hello, Angular!';
}
```

### Property Binding

```typescript
@Component({
  template: `
    <img [src]="imageUrl">
    <button [disabled]="isDisabled">Click</button>
  `
})
export class MyComponent {
  imageUrl = 'assets/logo.png';
  isDisabled = false;
}
```

### Event Binding

```typescript
@Component({
  template: `
    <button (click)="handleClick()">Click Me</button>
    <input (input)="handleInput($event)">
  `
})
export class MyComponent {
  handleClick(): void {
    console.log('Button clicked!');
  }
  
  handleInput(event: Event): void {
    const target = event.target as HTMLInputElement;
    console.log(target.value);
  }
}
```

### Two-Way Binding

```typescript
import { FormsModule } from '@angular/forms';

@Component({
  template: `
    <input [(ngModel)]="userName">
    <p>Hello, {{ userName }}!</p>
  `
})
export class MyComponent {
  userName = '';
}
```

## Real-World Example: Todo App

### todo.model.ts

```typescript
export interface Todo {
  id: number;
  title: string;
  completed: boolean;
  createdAt: Date;
}
```

### todo.service.ts

```typescript
import { Injectable } from '@angular/core';
import { Todo } from './todo.model';

@Injectable({
  providedIn: 'root'
})
export class TodoService {
  private todos: Todo[] = [];
  private nextId = 1;
  
  getTodos(): Todo[] {
    return this.todos;
  }
  
  addTodo(title: string): void {
    const todo: Todo = {
      id: this.nextId++,
      title,
      completed: false,
      createdAt: new Date()
    };
    this.todos.push(todo);
  }
  
  toggleTodo(id: number): void {
    const todo = this.todos.find(t => t.id === id);
    if (todo) {
      todo.completed = !todo.completed;
    }
  }
  
  deleteTodo(id: number): void {
    this.todos = this.todos.filter(t => t.id !== id);
  }
  
  getActiveTodos(): Todo[] {
    return this.todos.filter(t => !t.completed);
  }
  
  getCompletedTodos(): Todo[] {
    return this.todos.filter(t => t.completed);
  }
}
```

### todo-list.component.ts

```typescript
import { Component, OnInit } from '@angular/core';
import { TodoService } from '../todo.service';
import { Todo } from '../todo.model';

@Component({
  selector: 'app-todo-list',
  templateUrl: './todo-list.component.html',
  styleUrls: ['./todo-list.component.css']
})
export class TodoListComponent implements OnInit {
  todos: Todo[] = [];
  newTodoTitle = '';
  filter: 'all' | 'active' | 'completed' = 'all';
  
  constructor(private todoService: TodoService) {}
  
  ngOnInit(): void {
    this.loadTodos();
  }
  
  loadTodos(): void {
    switch (this.filter) {
      case 'active':
        this.todos = this.todoService.getActiveTodos();
        break;
      case 'completed':
        this.todos = this.todoService.getCompletedTodos();
        break;
      default:
        this.todos = this.todoService.getTodos();
    }
  }
  
  addTodo(): void {
    if (this.newTodoTitle.trim()) {
      this.todoService.addTodo(this.newTodoTitle);
      this.newTodoTitle = '';
      this.loadTodos();
    }
  }
  
  toggleTodo(id: number): void {
    this.todoService.toggleTodo(id);
    this.loadTodos();
  }
  
  deleteTodo(id: number): void {
    this.todoService.deleteTodo(id);
    this.loadTodos();
  }
  
  setFilter(filter: 'all' | 'active' | 'completed'): void {
    this.filter = filter;
    this.loadTodos();
  }
}
```

### todo-list.component.html

```html
<div class="todo-app">
  <h1>Todo List</h1>
  
  <!-- Add Todo Form -->
  <div class="add-todo">
    <input 
      [(ngModel)]="newTodoTitle" 
      (keyup.enter)="addTodo()"
      placeholder="What needs to be done?">
    <button (click)="addTodo()">Add</button>
  </div>
  
  <!-- Filter Buttons -->
  <div class="filters">
    <button 
      [class.active]="filter === 'all'"
      (click)="setFilter('all')">All</button>
    <button 
      [class.active]="filter === 'active'"
      (click)="setFilter('active')">Active</button>
    <button 
      [class.active]="filter === 'completed'"
      (click)="setFilter('completed')">Completed</button>
  </div>
  
  <!-- Todo List -->
  <ul class="todo-list">
    <li *ngFor="let todo of todos" [class.completed]="todo.completed">
      <input 
        type="checkbox" 
        [checked]="todo.completed"
        (change)="toggleTodo(todo.id)">
      <span>{{ todo.title }}</span>
      <button (click)="deleteTodo(todo.id)">Delete</button>
    </li>
  </ul>
  
  <p *ngIf="todos.length === 0" class="empty-message">
    No todos to display
  </p>
</div>
```

## Angular CLI Commands

```bash
# Create new application
ng new app-name

# Generate components
ng generate component component-name
ng g c component-name  # shorthand

# Generate service
ng generate service service-name
ng g s service-name

# Generate module
ng generate module module-name
ng g m module-name

# Generate directive
ng generate directive directive-name

# Generate pipe
ng generate pipe pipe-name

# Serve application
ng serve
ng serve --open  # Opens browser automatically
ng serve --port 4300  # Custom port

# Build for production
ng build
ng build --configuration production

# Run tests
ng test

# Run end-to-end tests
ng e2e

# Lint code
ng lint
```

## Summary

| Concept | Purpose | Example |
|---------|---------|---------|
| Components | UI building blocks | `@Component` decorator |
| Templates | HTML with Angular syntax | `{{ }}`, `[]`, `()` |
| Directives | Modify DOM | `*ngIf`, `*ngFor` |
| Services | Business logic | `@Injectable` decorator |
| Modules | Organize code | `@NgModule` decorator |
| Data Binding | Sync data and UI | `[(ngModel)]` |

## Next Steps

- Learn about component communication (Input/Output)
- Understand Angular routing
- Master forms (Template-driven and Reactive)
- Explore HTTP client for API calls
- Study lifecycle hooks
- Learn about pipes and custom pipes
