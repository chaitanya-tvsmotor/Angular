# RxJS and Observables in Angular

## What is RxJS?

RxJS (Reactive Extensions for JavaScript) is a library for reactive programming using Observables. It's deeply integrated into Angular for handling asynchronous operations.

## Why RxJS?

- **Unified API**: Handle events, HTTP requests, timers with one pattern
- **Composability**: Chain operations together
- **Cancellation**: Unsubscribe from ongoing operations
- **Error Handling**: Centralized error management
- **Backpressure**: Handle data flow control

## Observables vs Promises

```typescript
// Promise - single value, not cancellable
const promise = new Promise((resolve) => {
  setTimeout(() => resolve('Hello'), 1000);
});

promise.then(value => console.log(value));

// Observable - multiple values, cancellable
import { Observable } from 'rxjs';

const observable = new Observable((subscriber) => {
  setTimeout(() => subscriber.next('Hello'), 1000);
  setTimeout(() => subscriber.next('World'), 2000);
  setTimeout(() => subscriber.complete(), 3000);
});

const subscription = observable.subscribe({
  next: value => console.log(value),
  complete: () => console.log('Done')
});

// Can cancel
subscription.unsubscribe();
```

## Creating Observables

### From Values

```typescript
import { of, from } from 'rxjs';

// of - emit values in sequence
const numbers$ = of(1, 2, 3, 4, 5);
numbers$.subscribe(n => console.log(n));
// Output: 1, 2, 3, 4, 5

// from - convert array/promise/iterable to observable
const fromArray$ = from([10, 20, 30]);
fromArray$.subscribe(n => console.log(n));

const fromPromise$ = from(fetch('/api/data'));
fromPromise$.subscribe(response => console.log(response));
```

### From Events

```typescript
import { fromEvent } from 'rxjs';
import { debounceTime, map } from 'rxjs/operators';

// Real-world example: Search input
@Component({
  selector: 'app-search',
  template: `
    <input #searchInput type="text" placeholder="Search...">
    <div *ngFor="let result of searchResults">{{ result }}</div>
  `
})
export class SearchComponent implements OnInit, OnDestroy {
  @ViewChild('searchInput') searchInput!: ElementRef;
  searchResults: string[] = [];
  private subscription!: Subscription;
  
  ngOnInit(): void {
    this.subscription = fromEvent<KeyboardEvent>(
      this.searchInput.nativeElement,
      'keyup'
    ).pipe(
      map(event => (event.target as HTMLInputElement).value),
      debounceTime(300), // Wait 300ms after typing stops
      distinctUntilChanged() // Only if value changed
    ).subscribe(searchTerm => {
      this.performSearch(searchTerm);
    });
  }
  
  ngOnDestroy(): void {
    this.subscription.unsubscribe();
  }
  
  performSearch(term: string): void {
    // API call here
    console.log('Searching for:', term);
  }
}
```

### Interval and Timer

```typescript
import { interval, timer } from 'rxjs';
import { take } from 'rxjs/operators';

// Emit every second
const interval$ = interval(1000);
interval$.pipe(take(5)).subscribe(n => console.log(n));
// Output: 0, 1, 2, 3, 4

// Emit after delay, then at intervals
const timer$ = timer(2000, 1000); // Wait 2s, then emit every 1s
timer$.pipe(take(3)).subscribe(n => console.log(n));
// Output: 0 (after 2s), 1 (after 3s), 2 (after 4s)

// Real-world example: Auto-refresh data
@Component({
  selector: 'app-dashboard',
  template: `
    <div>Last updated: {{ lastUpdate | date:'medium' }}</div>
    <div *ngFor="let item of data">{{ item }}</div>
  `
})
export class DashboardComponent implements OnInit, OnDestroy {
  data: any[] = [];
  lastUpdate: Date = new Date();
  private refreshSubscription!: Subscription;
  
  constructor(private dataService: DataService) {}
  
  ngOnInit(): void {
    // Fetch immediately, then every 30 seconds
    this.refreshSubscription = timer(0, 30000)
      .subscribe(() => {
        this.fetchData();
      });
  }
  
  ngOnDestroy(): void {
    this.refreshSubscription.unsubscribe();
  }
  
  fetchData(): void {
    this.dataService.getData().subscribe(data => {
      this.data = data;
      this.lastUpdate = new Date();
    });
  }
}
```

## Common Operators

### Transformation Operators

```typescript
import { map, pluck, switchMap, mergeMap } from 'rxjs/operators';

// map - transform each value
const numbers$ = of(1, 2, 3, 4, 5);
numbers$.pipe(
  map(n => n * 2)
).subscribe(n => console.log(n));
// Output: 2, 4, 6, 8, 10

// pluck - extract property
interface User {
  id: number;
  name: string;
  email: string;
}

const users$ = of<User>(
  { id: 1, name: 'John', email: 'john@example.com' },
  { id: 2, name: 'Jane', email: 'jane@example.com' }
);

users$.pipe(
  pluck('name')
).subscribe(name => console.log(name));
// Output: 'John', 'Jane'

// Real-world example: Load user profile with posts
@Injectable({ providedIn: 'root' })
export class UserProfileService {
  constructor(private http: HttpClient) {}
  
  getUserWithPosts(userId: string): Observable<{ user: User; posts: Post[] }> {
    return this.http.get<User>(`/api/users/${userId}`).pipe(
      switchMap(user => {
        // switchMap cancels previous inner observable if new value arrives
        return this.http.get<Post[]>(`/api/users/${userId}/posts`).pipe(
          map(posts => ({ user, posts }))
        );
      })
    );
  }
}
```

### Filtering Operators

```typescript
import { filter, take, takeUntil, distinctUntilChanged } from 'rxjs/operators';

// filter - only emit values that pass condition
const numbers$ = of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
numbers$.pipe(
  filter(n => n % 2 === 0)
).subscribe(n => console.log(n));
// Output: 2, 4, 6, 8, 10

// take - emit only first N values
numbers$.pipe(
  take(3)
).subscribe(n => console.log(n));
// Output: 1, 2, 3

// distinctUntilChanged - only emit if value changed
const values$ = of(1, 1, 2, 2, 2, 3, 3, 1);
values$.pipe(
  distinctUntilChanged()
).subscribe(n => console.log(n));
// Output: 1, 2, 3, 1

// Real-world example: Unsubscribe on component destroy
@Component({
  selector: 'app-live-data',
  template: `<div>{{ data }}</div>`
})
export class LiveDataComponent implements OnInit, OnDestroy {
  data: any;
  private destroy$ = new Subject<void>();
  
  constructor(private dataService: DataService) {}
  
  ngOnInit(): void {
    this.dataService.getLiveData()
      .pipe(
        takeUntil(this.destroy$) // Automatically unsubscribe
      )
      .subscribe(data => this.data = data);
  }
  
  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

### Combination Operators

```typescript
import { combineLatest, forkJoin, merge, concat } from 'rxjs';

// combineLatest - emit when any observable emits
const age$ = of(25);
const name$ = of('John');
const email$ = of('john@example.com');

combineLatest([age$, name$, email$]).subscribe(
  ([age, name, email]) => {
    console.log({ age, name, email });
  }
);

// forkJoin - wait for all to complete, emit last values
const request1$ = this.http.get('/api/users');
const request2$ = this.http.get('/api/posts');
const request3$ = this.http.get('/api/comments');

forkJoin([request1$, request2$, request3$]).subscribe(
  ([users, posts, comments]) => {
    console.log('All data loaded', { users, posts, comments });
  }
);

// Real-world example: Form with dependent fields
@Component({
  selector: 'app-registration',
  template: `
    <form [formGroup]="form">
      <input formControlName="country">
      <select formControlName="state">
        <option *ngFor="let state of states$ | async" [value]="state">
          {{ state }}
        </option>
      </select>
      <select formControlName="city">
        <option *ngFor="let city of cities$ | async" [value]="city">
          {{ city }}
        </option>
      </select>
    </form>
  `
})
export class RegistrationComponent implements OnInit {
  form!: FormGroup;
  states$!: Observable<string[]>;
  cities$!: Observable<string[]>;
  
  constructor(
    private fb: FormBuilder,
    private locationService: LocationService
  ) {}
  
  ngOnInit(): void {
    this.form = this.fb.group({
      country: [''],
      state: [''],
      city: ['']
    });
    
    // Load states when country changes
    this.states$ = this.form.get('country')!.valueChanges.pipe(
      distinctUntilChanged(),
      switchMap(country => this.locationService.getStates(country))
    );
    
    // Load cities when state changes
    this.cities$ = this.form.get('state')!.valueChanges.pipe(
      distinctUntilChanged(),
      switchMap(state => this.locationService.getCities(state))
    );
  }
}
```

### Error Handling

```typescript
import { catchError, retry, retryWhen, delay } from 'rxjs/operators';
import { throwError } from 'rxjs';

// catchError - handle errors gracefully
this.http.get('/api/data').pipe(
  catchError(error => {
    console.error('Error occurred:', error);
    return of([]); // Return default value
  })
).subscribe(data => console.log(data));

// retry - retry failed operations
this.http.get('/api/data').pipe(
  retry(3), // Retry 3 times before failing
  catchError(error => {
    console.error('Failed after 3 retries');
    return throwError(() => error);
  })
).subscribe();

// Real-world example: Robust API service
@Injectable({ providedIn: 'root' })
export class ApiService {
  constructor(
    private http: HttpClient,
    private notification: NotificationService
  ) {}
  
  get<T>(url: string): Observable<T> {
    return this.http.get<T>(url).pipe(
      retry(2),
      catchError(error => {
        this.handleError(error);
        return throwError(() => error);
      })
    );
  }
  
  post<T>(url: string, data: any): Observable<T> {
    return this.http.post<T>(url, data).pipe(
      catchError(error => {
        this.handleError(error);
        return throwError(() => error);
      })
    );
  }
  
  private handleError(error: HttpErrorResponse): void {
    let message = 'An error occurred';
    
    if (error.error instanceof ErrorEvent) {
      // Client-side error
      message = `Error: ${error.error.message}`;
    } else {
      // Server-side error
      message = `Error Code: ${error.status}\nMessage: ${error.message}`;
    }
    
    this.notification.error(message);
  }
}
```

## Subjects

Special type of Observable that allows multicasting.

```typescript
import { Subject, BehaviorSubject, ReplaySubject, AsyncSubject } from 'rxjs';

// Subject - no initial value, only receives future emissions
const subject = new Subject<number>();

subject.subscribe(n => console.log('Observer 1:', n));
subject.next(1);
subject.next(2);

subject.subscribe(n => console.log('Observer 2:', n));
subject.next(3);
// Observer 1: 1, 2, 3
// Observer 2: 3

// BehaviorSubject - requires initial value, emits last value to new subscribers
const behaviorSubject = new BehaviorSubject<number>(0);

behaviorSubject.subscribe(n => console.log('Observer 1:', n));
// Observer 1: 0

behaviorSubject.next(1);
behaviorSubject.next(2);

behaviorSubject.subscribe(n => console.log('Observer 2:', n));
// Observer 2: 2

// Real-world example: User authentication state
@Injectable({ providedIn: 'root' })
export class AuthService {
  private currentUserSubject = new BehaviorSubject<User | null>(null);
  public currentUser$ = this.currentUserSubject.asObservable();
  
  constructor(private http: HttpClient) {
    this.loadUserFromStorage();
  }
  
  login(credentials: Credentials): Observable<User> {
    return this.http.post<User>('/api/auth/login', credentials).pipe(
      tap(user => {
        this.currentUserSubject.next(user);
        localStorage.setItem('user', JSON.stringify(user));
      })
    );
  }
  
  logout(): void {
    this.currentUserSubject.next(null);
    localStorage.removeItem('user');
  }
  
  get currentUserValue(): User | null {
    return this.currentUserSubject.value;
  }
  
  isAuthenticated(): boolean {
    return this.currentUserValue !== null;
  }
  
  private loadUserFromStorage(): void {
    const userJson = localStorage.getItem('user');
    if (userJson) {
      const user = JSON.parse(userJson);
      this.currentUserSubject.next(user);
    }
  }
}

// Usage in components
@Component({
  selector: 'app-header',
  template: `
    <div *ngIf="currentUser$ | async as user; else notLoggedIn">
      Welcome, {{ user.name }}
      <button (click)="logout()">Logout</button>
    </div>
    <ng-template #notLoggedIn>
      <button routerLink="/login">Login</button>
    </ng-template>
  `
})
export class HeaderComponent {
  currentUser$ = this.authService.currentUser$;
  
  constructor(private authService: AuthService) {}
  
  logout(): void {
    this.authService.logout();
  }
}
```

## Real-World Example: Search with Typeahead

```typescript
@Component({
  selector: 'app-search',
  template: `
    <div class="search-container">
      <input
        type="text"
        [(ngModel)]="searchTerm"
        (input)="search()"
        placeholder="Search products..."
      >
      
      <div *ngIf="loading" class="loading">Searching...</div>
      
      <div *ngIf="error" class="error">{{ error }}</div>
      
      <ul *ngIf="results$ | async as results" class="results">
        <li *ngFor="let result of results" (click)="selectResult(result)">
          {{ result.name }}
        </li>
      </ul>
    </div>
  `
})
export class SearchComponent implements OnInit, OnDestroy {
  searchTerm = '';
  loading = false;
  error: string | null = null;
  
  private searchSubject = new Subject<string>();
  results$!: Observable<Product[]>;
  private destroy$ = new Subject<void>();
  
  constructor(private productService: ProductService) {}
  
  ngOnInit(): void {
    this.results$ = this.searchSubject.pipe(
      debounceTime(300),           // Wait 300ms after typing stops
      distinctUntilChanged(),      // Only if search term changed
      tap(() => {
        this.loading = true;
        this.error = null;
      }),
      switchMap(term => {
        if (!term.trim()) {
          return of([]);
        }
        return this.productService.search(term).pipe(
          catchError(error => {
            this.error = 'Search failed. Please try again.';
            return of([]);
          })
        );
      }),
      tap(() => this.loading = false),
      takeUntil(this.destroy$)
    );
  }
  
  search(): void {
    this.searchSubject.next(this.searchTerm);
  }
  
  selectResult(result: Product): void {
    console.log('Selected:', result);
    // Navigate to product detail or perform action
  }
  
  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

## Async Pipe

The async pipe subscribes and unsubscribes automatically.

```typescript
@Component({
  selector: 'app-user-list',
  template: `
    <div *ngIf="loading$ | async">Loading...</div>
    
    <div *ngIf="error$ | async as error" class="error">
      {{ error }}
    </div>
    
    <ul *ngIf="users$ | async as users">
      <li *ngFor="let user of users">
        {{ user.name }}
      </li>
    </ul>
  `
})
export class UserListComponent implements OnInit {
  users$!: Observable<User[]>;
  loading$!: Observable<boolean>;
  error$!: Observable<string | null>;
  
  constructor(private userService: UserService) {}
  
  ngOnInit(): void {
    this.users$ = this.userService.getUsers();
    this.loading$ = this.userService.loading$;
    this.error$ = this.userService.error$;
  }
}
```

## Best Practices

1. **Always unsubscribe**: Use `takeUntil`, `async pipe`, or manual unsubscribe
2. **Use async pipe**: Automatic subscription management
3. **Avoid nested subscriptions**: Use operators like `switchMap`, `mergeMap`
4. **Handle errors**: Always use `catchError`
5. **Use BehaviorSubject for state**: When you need current value
6. **Debounce user input**: Reduce API calls
7. **Use shareReplay**: Share expensive operations
8. **Type your observables**: `Observable<User>` not just `Observable`

## Summary

| Concept | Purpose | Example |
|---------|---------|---------|
| Observable | Async data stream | `http.get()` |
| Subject | Multicast observable | Event bus |
| BehaviorSubject | State management | Current user |
| Operators | Transform data | `map`, `filter` |
| Async Pipe | Auto subscription | `{{ data$ \| async }}` |

## Next Steps

- Learn about NgRx for state management
- Explore advanced operators
- Master error handling patterns
- Study testing observables
