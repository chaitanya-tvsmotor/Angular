# TypeScript & Angular Quick Reference Guide

## TypeScript Quick Reference

### Basic Types

```typescript
// Primitives
let str: string = "Hello";
let num: number = 42;
let bool: boolean = true;
let n: null = null;
let u: undefined = undefined;

// Arrays
let numbers: number[] = [1, 2, 3];
let strings: Array<string> = ["a", "b"];

// Tuples
let tuple: [string, number] = ["age", 30];

// Enums
enum Color { Red, Green, Blue }
let c: Color = Color.Green;

// Any, Unknown, Never
let any: any = "anything";
let unknown: unknown = "safe any";
let never: never; // never returns

// Type Assertions
let value: any = "string";
let length: number = (value as string).length;
```

### Functions

```typescript
// Function types
function add(x: number, y: number): number {
  return x + y;
}

// Optional parameters
function greet(name: string, greeting?: string): string {
  return `${greeting || "Hello"}, ${name}`;
}

// Default parameters
function log(message: string, level: string = "info"): void {
  console.log(`[${level}] ${message}`);
}

// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}

// Arrow functions
const multiply = (x: number, y: number): number => x * y;

// Async functions
async function fetchData(): Promise<Data> {
  return await api.get('/data');
}
```

### Interfaces & Types

```typescript
// Interface
interface User {
  id: number;
  name: string;
  email?: string;        // Optional
  readonly created: Date; // Readonly
}

// Type alias
type ID = string | number;
type Status = "active" | "inactive";

// Union types
let value: string | number;

// Intersection types
type Person = { name: string } & { age: number };

// Generic types
interface Container<T> {
  value: T;
}

// Utility types
type Partial<T> = { [P in keyof T]?: T[P] };
type Readonly<T> = { readonly [P in keyof T]: T[P] };
type Pick<T, K extends keyof T> = { [P in K]: T[P] };
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;
```

### Classes

```typescript
class Animal {
  private name: string;
  protected age: number;
  public species: string;
  
  constructor(name: string, age: number, species: string) {
    this.name = name;
    this.age = age;
    this.species = species;
  }
  
  // Method
  makeSound(): void {
    console.log("Some sound");
  }
  
  // Getter
  get animalName(): string {
    return this.name;
  }
  
  // Setter
  set animalName(name: string) {
    this.name = name;
  }
  
  // Static method
  static create(name: string): Animal {
    return new Animal(name, 0, "Unknown");
  }
}

// Inheritance
class Dog extends Animal {
  constructor(name: string, age: number) {
    super(name, age, "Dog");
  }
  
  makeSound(): void {
    console.log("Woof!");
  }
}

// Abstract class
abstract class Shape {
  abstract area(): number;
}

// Interface implementation
interface Printable {
  print(): void;
}

class Document implements Printable {
  print(): void {
    console.log("Printing...");
  }
}
```

## Angular Quick Reference

### Component

```typescript
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-example',
  templateUrl: './example.component.html',
  styleUrls: ['./example.component.css']
})
export class ExampleComponent implements OnInit {
  // Properties
  title = 'Example';
  count = 0;
  
  // Input property
  @Input() data: any;
  
  // Output event
  @Output() dataChange = new EventEmitter<any>();
  
  constructor(private service: DataService) {}
  
  ngOnInit(): void {
    // Initialization logic
  }
  
  // Method
  increment(): void {
    this.count++;
    this.dataChange.emit(this.count);
  }
}
```

### Template Syntax

```html
<!-- Interpolation -->
<p>{{ title }}</p>

<!-- Property binding -->
<img [src]="imageUrl">
<button [disabled]="isDisabled">Click</button>

<!-- Event binding -->
<button (click)="handleClick()">Click</button>
<input (input)="handleInput($event)">

<!-- Two-way binding -->
<input [(ngModel)]="name">

<!-- Structural directives -->
<div *ngIf="isVisible">Content</div>
<div *ngFor="let item of items; let i = index">{{ i }}: {{ item }}</div>

<!-- Attribute directives -->
<div [ngClass]="{'active': isActive, 'disabled': isDisabled}">
<div [ngStyle]="{'color': textColor, 'font-size': fontSize + 'px'}">

<!-- Template reference variable -->
<input #myInput type="text">
<button (click)="myInput.focus()">Focus</button>

<!-- Pipes -->
<p>{{ date | date:'short' }}</p>
<p>{{ price | currency:'USD' }}</p>
<p>{{ text | uppercase }}</p>
<p>{{ data | json }}</p>
<p>{{ items | async }}</p>
```

### Service

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'  // Singleton service
})
export class DataService {
  private apiUrl = 'https://api.example.com';
  
  constructor(private http: HttpClient) {}
  
  getData(): Observable<Data[]> {
    return this.http.get<Data[]>(this.apiUrl);
  }
  
  postData(data: Data): Observable<Data> {
    return this.http.post<Data>(this.apiUrl, data);
  }
}
```

### Routing

```typescript
// app-routing.module.ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'user/:id', component: UserComponent },
  { 
    path: 'admin', 
    component: AdminComponent,
    canActivate: [AuthGuard]
  },
  {
    path: 'products',
    loadChildren: () => import('./products/products.module')
      .then(m => m.ProductsModule)
  },
  { path: '**', component: NotFoundComponent }
];

// Navigation
<a routerLink="/about">About</a>
<a [routerLink]="['/user', userId]">User</a>

// Programmatic navigation
constructor(private router: Router) {}

navigate(): void {
  this.router.navigate(['/about']);
  this.router.navigate(['/user', 123]);
}

// Get route parameters
constructor(private route: ActivatedRoute) {}

ngOnInit(): void {
  // Snapshot
  const id = this.route.snapshot.paramMap.get('id');
  
  // Observable
  this.route.paramMap.subscribe(params => {
    const id = params.get('id');
  });
}
```

### Forms

```typescript
// Template-driven form
<form #myForm="ngForm" (ngSubmit)="onSubmit(myForm)">
  <input name="username" [(ngModel)]="user.username" required>
  <input name="email" [(ngModel)]="user.email" email>
  <button type="submit" [disabled]="myForm.invalid">Submit</button>
</form>

// Reactive form
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

export class MyComponent {
  form: FormGroup;
  
  constructor(private fb: FormBuilder) {
    this.form = this.fb.group({
      username: ['', [Validators.required, Validators.minLength(3)]],
      email: ['', [Validators.required, Validators.email]],
      age: ['', [Validators.required, Validators.min(18)]]
    });
  }
  
  onSubmit(): void {
    if (this.form.valid) {
      console.log(this.form.value);
    }
  }
}

<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="username">
  <div *ngIf="form.get('username')?.invalid && form.get('username')?.touched">
    Username is required
  </div>
  <button type="submit" [disabled]="form.invalid">Submit</button>
</form>
```

### RxJS Operators

```typescript
import { map, filter, tap, catchError, switchMap, debounceTime } from 'rxjs/operators';

// map - transform values
this.data$.pipe(
  map(data => data.map(item => item.name))
);

// filter - filter values
this.numbers$.pipe(
  filter(n => n > 10)
);

// tap - side effects
this.data$.pipe(
  tap(data => console.log(data))
);

// catchError - error handling
this.http.get('/api/data').pipe(
  catchError(error => of([]))
);

// switchMap - switch to new observable
this.searchTerm$.pipe(
  switchMap(term => this.service.search(term))
);

// debounceTime - delay emissions
this.input$.pipe(
  debounceTime(300)
);

// combineLatest - combine multiple observables
combineLatest([obs1$, obs2$]).pipe(
  map(([data1, data2]) => ({ data1, data2 }))
);
```

### HTTP Client

```typescript
import { HttpClient, HttpHeaders } from '@angular/common/http';

constructor(private http: HttpClient) {}

// GET
this.http.get<User[]>('/api/users').subscribe(users => {
  console.log(users);
});

// GET with params
this.http.get('/api/users', {
  params: { page: '1', limit: '10' }
});

// POST
this.http.post<User>('/api/users', userData).subscribe(user => {
  console.log(user);
});

// PUT
this.http.put<User>('/api/users/1', userData);

// DELETE
this.http.delete('/api/users/1');

// Custom headers
const headers = new HttpHeaders({
  'Authorization': 'Bearer token',
  'Content-Type': 'application/json'
});

this.http.get('/api/data', { headers });
```

### Lifecycle Hooks

```typescript
import { 
  OnInit, OnDestroy, OnChanges, 
  AfterViewInit, AfterContentInit 
} from '@angular/core';

export class MyComponent implements OnInit, OnDestroy, OnChanges {
  // Called once after first ngOnChanges
  ngOnInit(): void {
    console.log('Component initialized');
  }
  
  // Called when input properties change
  ngOnChanges(changes: SimpleChanges): void {
    console.log('Input changed:', changes);
  }
  
  // Called after component view initialized
  ngAfterViewInit(): void {
    console.log('View initialized');
  }
  
  // Called after content projection initialized
  ngAfterContentInit(): void {
    console.log('Content initialized');
  }
  
  // Called before component is destroyed
  ngOnDestroy(): void {
    console.log('Component destroyed');
  }
}
```

### Directives

```typescript
// Attribute directive
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

// Structural directive
@Directive({
  selector: '[appUnless]'
})
export class UnlessDirective {
  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef
  ) {}
  
  @Input()
  set appUnless(condition: boolean) {
    if (!condition) {
      this.viewContainer.createEmbeddedView(this.templateRef);
    } else {
      this.viewContainer.clear();
    }
  }
}
```

### Pipes

```typescript
// Custom pipe
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'truncate'
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 50): string {
    return value.length > limit 
      ? value.substring(0, limit) + '...' 
      : value;
  }
}

// Usage
<p>{{ longText | truncate:100 }}</p>

// Pure vs Impure pipes
@Pipe({
  name: 'filter',
  pure: false  // Impure pipe - runs on every change detection
})
```

## CLI Commands Quick Reference

```bash
# Create new app
ng new my-app
ng new my-app --routing --style=scss

# Serve application
ng serve
ng serve --open --port 4300

# Generate components
ng generate component my-component
ng g c my-component --skip-tests

# Generate service
ng generate service my-service
ng g s my-service

# Generate module
ng generate module my-module --routing
ng g m my-module

# Generate directive
ng generate directive my-directive
ng g d my-directive

# Generate pipe
ng generate pipe my-pipe
ng g p my-pipe

# Generate guard
ng generate guard my-guard
ng g g my-guard

# Build for production
ng build --configuration production

# Run tests
ng test
ng test --code-coverage

# Run e2e tests
ng e2e

# Lint code
ng lint

# Update Angular
ng update @angular/cli @angular/core
```

## Common Patterns

### Unsubscribe Pattern

```typescript
import { Subject } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

export class MyComponent implements OnInit, OnDestroy {
  private destroy$ = new Subject<void>();
  
  ngOnInit(): void {
    this.dataService.getData()
      .pipe(takeUntil(this.destroy$))
      .subscribe(data => {
        // Handle data
      });
  }
  
  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

### Smart/Dumb Component Pattern

```typescript
// Smart (Container) Component
@Component({
  selector: 'app-user-container',
  template: `
    <app-user-list
      [users]="users$ | async"
      (userSelected)="onUserSelected($event)">
    </app-user-list>
  `
})
export class UserContainerComponent {
  users$ = this.userService.getUsers();
  
  constructor(private userService: UserService) {}
  
  onUserSelected(user: User): void {
    // Handle selection
  }
}

// Dumb (Presentational) Component
@Component({
  selector: 'app-user-list',
  template: `
    <div *ngFor="let user of users" (click)="selectUser(user)">
      {{ user.name }}
    </div>
  `
})
export class UserListComponent {
  @Input() users: User[] = [];
  @Output() userSelected = new EventEmitter<User>();
  
  selectUser(user: User): void {
    this.userSelected.emit(user);
  }
}
```

### Error Handling Pattern

```typescript
// Service
getData(): Observable<Data[]> {
  return this.http.get<Data[]>('/api/data').pipe(
    retry(2),
    catchError(this.handleError)
  );
}

private handleError(error: HttpErrorResponse): Observable<never> {
  console.error('Error:', error);
  return throwError(() => new Error('Something went wrong'));
}

// Component
loadData(): void {
  this.loading = true;
  this.error = null;
  
  this.dataService.getData().subscribe({
    next: data => {
      this.data = data;
      this.loading = false;
    },
    error: error => {
      this.error = error.message;
      this.loading = false;
    }
  });
}
```

## Keyboard Shortcuts (VS Code)

```
Ctrl/Cmd + Shift + P    - Command palette
Ctrl/Cmd + P            - Quick file open
Ctrl/Cmd + `            - Toggle terminal
Ctrl/Cmd + B            - Toggle sidebar
Alt + Click             - Multiple cursors
Ctrl/Cmd + /            - Toggle comment
Ctrl/Cmd + D            - Select next occurrence
F2                      - Rename symbol
Ctrl/Cmd + .            - Quick fix
```

## Best Practices Summary

1. **Use TypeScript strictly** - Enable strict mode
2. **Follow Angular style guide** - Consistent code
3. **Use async pipe** - Automatic subscription management
4. **Implement OnPush strategy** - Better performance
5. **Lazy load modules** - Faster initial load
6. **Use trackBy in ngFor** - Optimize rendering
7. **Unsubscribe properly** - Prevent memory leaks
8. **Type everything** - Leverage TypeScript
9. **Use services for logic** - Keep components lean
10. **Write tests** - Ensure code quality

---

*For detailed explanations, see the full documentation in the `/docs` directory.*
