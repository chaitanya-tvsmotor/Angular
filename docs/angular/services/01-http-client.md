# HTTP Client in Angular

## Introduction

Angular's HttpClient module provides a powerful way to communicate with backend services over HTTP. It's built on top of RxJS Observables and offers features like request/response interception, typed responses, and error handling.

## Setup

```typescript
// app.module.ts
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [
    BrowserModule,
    HttpClientModule  // Import HttpClientModule
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Basic HTTP Operations

### GET Request

```typescript
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

interface User {
  id: number;
  name: string;
  email: string;
}

@Injectable({
  providedIn: 'root'
})
export class UserService {
  private apiUrl = 'https://api.example.com/users';
  
  constructor(private http: HttpClient) {}
  
  // Get all users
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl);
  }
  
  // Get single user
  getUser(id: number): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/${id}`);
  }
  
  // With query parameters
  searchUsers(term: string, limit: number = 10): Observable<User[]> {
    const params = { q: term, limit: limit.toString() };
    return this.http.get<User[]>(`${this.apiUrl}/search`, { params });
  }
}

// Component usage
@Component({
  selector: 'app-user-list',
  template: `
    <div *ngFor="let user of users">
      {{ user.name }}
    </div>
  `
})
export class UserListComponent implements OnInit {
  users: User[] = [];
  
  constructor(private userService: UserService) {}
  
  ngOnInit(): void {
    this.userService.getUsers().subscribe(
      users => this.users = users,
      error => console.error('Error:', error)
    );
  }
}
```

### POST Request

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  createUser(user: Partial<User>): Observable<User> {
    return this.http.post<User>(this.apiUrl, user);
  }
  
  // With custom headers
  createUserWithAuth(user: Partial<User>, token: string): Observable<User> {
    const headers = {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    };
    
    return this.http.post<User>(this.apiUrl, user, { headers });
  }
}

// Usage
@Component({
  selector: 'app-user-form',
  template: `
    <form (ngSubmit)="onSubmit()">
      <input [(ngModel)]="newUser.name" name="name">
      <input [(ngModel)]="newUser.email" name="email">
      <button type="submit">Create User</button>
    </form>
  `
})
export class UserFormComponent {
  newUser: Partial<User> = {};
  
  constructor(private userService: UserService) {}
  
  onSubmit(): void {
    this.userService.createUser(this.newUser).subscribe(
      user => {
        console.log('User created:', user);
        this.newUser = {}; // Reset form
      },
      error => console.error('Error:', error)
    );
  }
}
```

### PUT Request

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  // Full update
  updateUser(id: number, user: User): Observable<User> {
    return this.http.put<User>(`${this.apiUrl}/${id}`, user);
  }
  
  // Partial update (PATCH)
  patchUser(id: number, updates: Partial<User>): Observable<User> {
    return this.http.patch<User>(`${this.apiUrl}/${id}`, updates);
  }
}
```

### DELETE Request

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  deleteUser(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}

// Usage
deleteUserHandler(userId: number): void {
  if (confirm('Are you sure you want to delete this user?')) {
    this.userService.deleteUser(userId).subscribe(
      () => {
        console.log('User deleted');
        this.loadUsers(); // Refresh list
      },
      error => console.error('Error:', error)
    );
  }
}
```

## Error Handling

### Basic Error Handling

```typescript
import { catchError } from 'rxjs/operators';
import { throwError } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class UserService {
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl).pipe(
      catchError(error => {
        console.error('Error fetching users:', error);
        return throwError(() => new Error('Failed to fetch users'));
      })
    );
  }
}
```

### Comprehensive Error Handler

```typescript
import { HttpErrorResponse } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class ErrorHandlerService {
  handleError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'An error occurred';
    
    if (error.error instanceof ErrorEvent) {
      // Client-side or network error
      errorMessage = `Client Error: ${error.error.message}`;
    } else {
      // Backend error
      errorMessage = `Server Error: ${error.status} - ${error.message}`;
      
      // Handle specific status codes
      switch (error.status) {
        case 400:
          errorMessage = 'Bad Request: Invalid data submitted';
          break;
        case 401:
          errorMessage = 'Unauthorized: Please log in';
          break;
        case 403:
          errorMessage = 'Forbidden: Access denied';
          break;
        case 404:
          errorMessage = 'Not Found: Resource does not exist';
          break;
        case 500:
          errorMessage = 'Internal Server Error: Please try again later';
          break;
      }
    }
    
    console.error(errorMessage);
    return throwError(() => new Error(errorMessage));
  }
}

// Usage in service
@Injectable({
  providedIn: 'root'
})
export class UserService {
  constructor(
    private http: HttpClient,
    private errorHandler: ErrorHandlerService
  ) {}
  
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl).pipe(
      catchError(this.errorHandler.handleError)
    );
  }
}
```

## HTTP Interceptors

Intercept and modify HTTP requests/responses globally.

### Authentication Interceptor

```typescript
import { 
  HttpInterceptor, 
  HttpRequest, 
  HttpHandler, 
  HttpEvent 
} from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}
  
  intercept(
    req: HttpRequest<any>, 
    next: HttpHandler
  ): Observable<HttpEvent<any>> {
    // Get auth token
    const authToken = this.authService.getToken();
    
    // Clone request and add authorization header
    if (authToken) {
      req = req.clone({
        setHeaders: {
          Authorization: `Bearer ${authToken}`
        }
      });
    }
    
    return next.handle(req);
  }
}

// Register interceptor
@NgModule({
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    }
  ]
})
export class AppModule { }
```

### Loading Interceptor

```typescript
@Injectable()
export class LoadingInterceptor implements HttpInterceptor {
  private requests: HttpRequest<any>[] = [];
  
  constructor(private loadingService: LoadingService) {}
  
  intercept(
    req: HttpRequest<any>,
    next: HttpHandler
  ): Observable<HttpEvent<any>> {
    this.requests.push(req);
    this.loadingService.setLoading(true);
    
    return next.handle(req).pipe(
      finalize(() => {
        this.removeRequest(req);
        if (this.requests.length === 0) {
          this.loadingService.setLoading(false);
        }
      })
    );
  }
  
  private removeRequest(req: HttpRequest<any>): void {
    const index = this.requests.indexOf(req);
    if (index >= 0) {
      this.requests.splice(index, 1);
    }
  }
}
```

### Error Interceptor

```typescript
@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  constructor(
    private authService: AuthService,
    private router: Router,
    private notificationService: NotificationService
  ) {}
  
  intercept(
    req: HttpRequest<any>,
    next: HttpHandler
  ): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        if (error.status === 401) {
          // Unauthorized - logout and redirect
          this.authService.logout();
          this.router.navigate(['/login']);
          this.notificationService.error('Session expired. Please log in again.');
        } else if (error.status === 403) {
          // Forbidden
          this.notificationService.error('Access denied.');
          this.router.navigate(['/']);
        } else if (error.status === 500) {
          // Server error
          this.notificationService.error('Server error. Please try again later.');
        }
        
        return throwError(() => error);
      })
    );
  }
}
```

### Caching Interceptor

```typescript
@Injectable()
export class CachingInterceptor implements HttpInterceptor {
  private cache = new Map<string, HttpResponse<any>>();
  
  intercept(
    req: HttpRequest<any>,
    next: HttpHandler
  ): Observable<HttpEvent<any>> {
    // Only cache GET requests
    if (req.method !== 'GET') {
      return next.handle(req);
    }
    
    // Check if request is marked as cacheable
    if (!req.headers.has('X-Cache')) {
      return next.handle(req);
    }
    
    const cachedResponse = this.cache.get(req.url);
    if (cachedResponse) {
      return of(cachedResponse.clone());
    }
    
    return next.handle(req).pipe(
      tap(event => {
        if (event instanceof HttpResponse) {
          this.cache.set(req.url, event.clone());
        }
      })
    );
  }
}

// Usage - mark requests as cacheable
this.http.get('/api/config', {
  headers: new HttpHeaders({ 'X-Cache': 'true' })
});
```

## Real-World Example: Complete API Service

```typescript
interface ApiResponse<T> {
  data: T;
  message: string;
  success: boolean;
}

interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
}

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  private readonly baseUrl = environment.apiUrl;
  
  constructor(
    private http: HttpClient,
    private errorHandler: ErrorHandlerService
  ) {}
  
  /**
   * GET request
   */
  get<T>(endpoint: string, params?: any): Observable<T> {
    return this.http.get<T>(`${this.baseUrl}/${endpoint}`, { params }).pipe(
      retry(2),
      catchError(this.errorHandler.handleError)
    );
  }
  
  /**
   * POST request
   */
  post<T>(endpoint: string, data: any): Observable<T> {
    return this.http.post<T>(`${this.baseUrl}/${endpoint}`, data).pipe(
      catchError(this.errorHandler.handleError)
    );
  }
  
  /**
   * PUT request
   */
  put<T>(endpoint: string, data: any): Observable<T> {
    return this.http.put<T>(`${this.baseUrl}/${endpoint}`, data).pipe(
      catchError(this.errorHandler.handleError)
    );
  }
  
  /**
   * PATCH request
   */
  patch<T>(endpoint: string, data: any): Observable<T> {
    return this.http.patch<T>(`${this.baseUrl}/${endpoint}`, data).pipe(
      catchError(this.errorHandler.handleError)
    );
  }
  
  /**
   * DELETE request
   */
  delete<T>(endpoint: string): Observable<T> {
    return this.http.delete<T>(`${this.baseUrl}/${endpoint}`).pipe(
      catchError(this.errorHandler.handleError)
    );
  }
  
  /**
   * Upload file
   */
  uploadFile<T>(endpoint: string, file: File, additionalData?: any): Observable<T> {
    const formData = new FormData();
    formData.append('file', file, file.name);
    
    if (additionalData) {
      Object.keys(additionalData).forEach(key => {
        formData.append(key, additionalData[key]);
      });
    }
    
    return this.http.post<T>(`${this.baseUrl}/${endpoint}`, formData).pipe(
      catchError(this.errorHandler.handleError)
    );
  }
  
  /**
   * Download file
   */
  downloadFile(endpoint: string): Observable<Blob> {
    return this.http.get(`${this.baseUrl}/${endpoint}`, {
      responseType: 'blob'
    }).pipe(
      catchError(this.errorHandler.handleError)
    );
  }
}

// Product Service using API Service
@Injectable({
  providedIn: 'root'
})
export class ProductService {
  constructor(private api: ApiService) {}
  
  getProducts(page: number = 1, pageSize: number = 10): Observable<PaginatedResponse<Product>> {
    return this.api.get<PaginatedResponse<Product>>('products', { page, pageSize });
  }
  
  getProduct(id: string): Observable<Product> {
    return this.api.get<Product>(`products/${id}`);
  }
  
  createProduct(product: Partial<Product>): Observable<Product> {
    return this.api.post<Product>('products', product);
  }
  
  updateProduct(id: string, product: Partial<Product>): Observable<Product> {
    return this.api.put<Product>(`products/${id}`, product);
  }
  
  deleteProduct(id: string): Observable<void> {
    return this.api.delete<void>(`products/${id}`);
  }
  
  uploadProductImage(productId: string, image: File): Observable<{ url: string }> {
    return this.api.uploadFile<{ url: string }>(
      `products/${productId}/image`,
      image
    );
  }
  
  searchProducts(term: string): Observable<Product[]> {
    return this.api.get<Product[]>('products/search', { q: term });
  }
}
```

## Progress Tracking

```typescript
import { HttpEvent, HttpEventType } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class FileUploadService {
  constructor(private http: HttpClient) {}
  
  upload(file: File): Observable<number> {
    const formData = new FormData();
    formData.append('file', file);
    
    return this.http.post('/api/upload', formData, {
      reportProgress: true,
      observe: 'events'
    }).pipe(
      map((event: HttpEvent<any>) => {
        switch (event.type) {
          case HttpEventType.UploadProgress:
            const progress = event.total
              ? Math.round((100 * event.loaded) / event.total)
              : 0;
            return progress;
          case HttpEventType.Response:
            return 100;
          default:
            return 0;
        }
      })
    );
  }
}

// Component
@Component({
  selector: 'app-file-upload',
  template: `
    <input type="file" (change)="onFileSelected($event)">
    <div *ngIf="uploadProgress > 0">
      <progress [value]="uploadProgress" max="100"></progress>
      <span>{{ uploadProgress }}%</span>
    </div>
  `
})
export class FileUploadComponent {
  uploadProgress = 0;
  
  constructor(private uploadService: FileUploadService) {}
  
  onFileSelected(event: Event): void {
    const file = (event.target as HTMLInputElement).files?.[0];
    
    if (file) {
      this.uploadService.upload(file).subscribe(
        progress => this.uploadProgress = progress,
        error => console.error('Upload failed:', error),
        () => console.log('Upload complete!')
      );
    }
  }
}
```

## Testing HTTP Calls

```typescript
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
import { TestBed } from '@angular/core/testing';

describe('UserService', () => {
  let service: UserService;
  let httpMock: HttpTestingController;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [UserService]
    });
    
    service = TestBed.inject(UserService);
    httpMock = TestBed.inject(HttpTestingController);
  });
  
  afterEach(() => {
    httpMock.verify(); // Ensure no outstanding requests
  });
  
  it('should fetch users', () => {
    const mockUsers: User[] = [
      { id: 1, name: 'John', email: 'john@example.com' },
      { id: 2, name: 'Jane', email: 'jane@example.com' }
    ];
    
    service.getUsers().subscribe(users => {
      expect(users).toEqual(mockUsers);
    });
    
    const req = httpMock.expectOne('https://api.example.com/users');
    expect(req.request.method).toBe('GET');
    req.flush(mockUsers);
  });
  
  it('should create user', () => {
    const newUser: Partial<User> = { name: 'Test', email: 'test@example.com' };
    const createdUser: User = { id: 3, ...newUser } as User;
    
    service.createUser(newUser).subscribe(user => {
      expect(user).toEqual(createdUser);
    });
    
    const req = httpMock.expectOne('https://api.example.com/users');
    expect(req.request.method).toBe('POST');
    expect(req.request.body).toEqual(newUser);
    req.flush(createdUser);
  });
});
```

## Best Practices

1. **Use TypeScript interfaces**: Define response types
2. **Centralize API calls**: Create service layer
3. **Handle errors gracefully**: Use interceptors and error handlers
4. **Use interceptors**: For authentication, logging, caching
5. **Unsubscribe properly**: Use async pipe or takeUntil
6. **Retry failed requests**: Use retry operator for transient failures
7. **Cache strategically**: Cache static data to reduce requests
8. **Type everything**: Leverage TypeScript's type system
9. **Test HTTP calls**: Use HttpClientTestingModule
10. **Monitor performance**: Track API response times

## Summary

| Feature | Purpose | Example |
|---------|---------|---------|
| HttpClient | Make HTTP requests | `http.get()` |
| Observables | Handle async data | `subscribe()` |
| Interceptors | Global request/response handling | Auth, logging |
| Error Handling | Manage failures | `catchError()` |
| Progress Tracking | Upload/download progress | `reportProgress: true` |
| Testing | Unit test HTTP calls | `HttpTestingController` |

## Next Steps

- Learn about WebSocket integration
- Explore GraphQL with Apollo
- Master advanced RxJS patterns
- Study API optimization techniques
