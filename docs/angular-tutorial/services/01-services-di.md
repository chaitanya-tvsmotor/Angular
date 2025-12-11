# Services and Dependency Injection

> Timestamp: **6:02:25** - Services and Dependency Injection

## Introduction

Services are singleton objects that provide:
- Shared data and logic across components
- API communication
- Business logic separation
- Reusable functionality

Dependency Injection (DI) provides services to components automatically.

---

## Creating a Service

```typescript
// user.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root' // Singleton service available app-wide
})
export class UserService {
  private users = [
    { id: 1, name: 'John', email: 'john@example.com' },
    { id: 2, name: 'Jane', email: 'jane@example.com' }
  ];
  
  getUsers() {
    return this.users;
  }
  
  getUserById(id: number) {
    return this.users.find(u => u.id === id);
  }
  
  addUser(user: any) {
    this.users.push({ ...user, id: this.users.length + 1 });
  }
}
```

---

## Injecting Services

### Method 1: Constructor Injection

```typescript
import { Component } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-users',
  standalone: true,
  template: `
    <div class="users">
      @for (user of users; track user.id) {
        <div class="user-card">
          <h3>{{ user.name }}</h3>
          <p>{{ user.email }}</p>
        </div>
      }
    </div>
  `
})
export class UsersComponent {
  users: any[] = [];
  
  constructor(private userService: UserService) {
    this.users = this.userService.getUsers();
  }
}
```

### Method 2: inject() Function (Modern)

```typescript
import { Component, inject } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-users',
  standalone: true,
  template: `...`
})
export class UsersComponent {
  private userService = inject(UserService);
  users = this.userService.getUsers();
}
```

---

## Service with HTTP

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

interface Product {
  id: number;
  name: string;
  price: number;
}

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private http = inject(HttpClient);
  private apiUrl = 'https://api.example.com/products';
  
  getProducts(): Observable<Product[]> {
    return this.http.get<Product[]>(this.apiUrl);
  }
  
  getProduct(id: number): Observable<Product> {
    return this.http.get<Product>(`${this.apiUrl}/${id}`);
  }
  
  createProduct(product: Product): Observable<Product> {
    return this.http.post<Product>(this.apiUrl, product);
  }
  
  updateProduct(id: number, product: Product): Observable<Product> {
    return this.http.put<Product>(`${this.apiUrl}/${id}`, product);
  }
  
  deleteProduct(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

---

## Summary

✅ **Services**: Shared logic and data across application

✅ **@Injectable**: Makes class available for DI

✅ **providedIn: 'root'**: Application-wide singleton

✅ **inject()**: Modern function-based injection

✅ **HTTP Service**: Centralize API communication

Services keep your code organized and reusable!
