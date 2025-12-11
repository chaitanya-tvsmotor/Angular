# CRUD Operations with JSON Server

> Timestamp: **6:29:26** - CRUD Operations – GET, PUT, POST & DELETE

## Introduction

CRUD operations (Create, Read, Update, Delete) are fundamental to data management. We'll use JSON Server as a mock REST API.

---

## Setup JSON Server

```bash
# Install JSON Server
npm install -g json-server

# Create db.json
{
  "products": [
    { "id": 1, "name": "Laptop", "price": 999, "category": "Electronics" },
    { "id": 2, "name": "Mouse", "price": 29, "category": "Accessories" }
  ]
}

# Start server
json-server --watch db.json --port 3000
```

---

## Complete CRUD Service

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

interface Product {
  id?: number;
  name: string;
  price: number;
  category: string;
}

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private http = inject(HttpClient);
  private apiUrl = 'http://localhost:3000/products';
  
  // CREATE
  createProduct(product: Product): Observable<Product> {
    return this.http.post<Product>(this.apiUrl, product);
  }
  
  // READ ALL
  getProducts(): Observable<Product[]> {
    return this.http.get<Product[]>(this.apiUrl);
  }
  
  // READ ONE
  getProduct(id: number): Observable<Product> {
    return this.http.get<Product>(`${this.apiUrl}/${id}`);
  }
  
  // UPDATE
  updateProduct(id: number, product: Product): Observable<Product> {
    return this.http.put<Product>(`${this.apiUrl}/${id}`, product);
  }
  
  // DELETE
  deleteProduct(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

---

## CRUD Component

```typescript
import { Component, OnInit, inject } from '@angular/core';
import { ProductService } from './product.service';
import { FormBuilder, ReactiveFormsModule, Validators } from '@angular/forms';

@Component({
  selector: 'app-product-crud',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <div class="crud-container">
      <h2>Product Management</h2>
      
      <!-- Create/Edit Form -->
      <form [formGroup]="productForm" (ngSubmit)="saveProduct()">
        <input formControlName="name" placeholder="Product Name">
        <input formControlName="price" type="number" placeholder="Price">
        <input formControlName="category" placeholder="Category">
        <button type="submit">{{ editMode ? 'Update' : 'Create' }}</button>
        @if (editMode) {
          <button type="button" (click)="cancelEdit()">Cancel</button>
        }
      </form>
      
      <!-- Product List -->
      <div class="products">
        @for (product of products; track product.id) {
          <div class="product-card">
            <h3>{{ product.name }}</h3>
            <p>{{ product.price | currency }}</p>
            <p>{{ product.category }}</p>
            <button (click)="editProduct(product)">Edit</button>
            <button (click)="deleteProduct(product.id!)">Delete</button>
          </div>
        }
      </div>
    </div>
  `,
  styles: [`
    .crud-container {
      padding: 2rem;
      max-width: 1200px;
      margin: 0 auto;
    }
    form {
      display: flex;
      gap: 1rem;
      margin-bottom: 2rem;
    }
    input {
      padding: 0.5rem;
      border: 1px solid #ddd;
      border-radius: 4px;
      flex: 1;
    }
    button {
      padding: 0.5rem 1rem;
      background: #1976d2;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    .products {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
      gap: 1rem;
    }
    .product-card {
      border: 1px solid #ddd;
      padding: 1rem;
      border-radius: 8px;
    }
  `]
})
export class ProductCrudComponent implements OnInit {
  private productService = inject(ProductService);
  private fb = inject(FormBuilder);
  
  products: any[] = [];
  editMode = false;
  editId?: number;
  
  productForm = this.fb.group({
    name: ['', Validators.required],
    price: [0, [Validators.required, Validators.min(0)]],
    category: ['', Validators.required]
  });
  
  ngOnInit(): void {
    this.loadProducts();
  }
  
  loadProducts(): void {
    this.productService.getProducts().subscribe(products => {
      this.products = products;
    });
  }
  
  saveProduct(): void {
    if (this.productForm.valid) {
      if (this.editMode) {
        this.productService.updateProduct(this.editId!, this.productForm.value as any)
          .subscribe(() => {
            this.loadProducts();
            this.cancelEdit();
          });
      } else {
        this.productService.createProduct(this.productForm.value as any)
          .subscribe(() => {
            this.loadProducts();
            this.productForm.reset();
          });
      }
    }
  }
  
  editProduct(product: any): void {
    this.editMode = true;
    this.editId = product.id;
    this.productForm.patchValue(product);
  }
  
  deleteProduct(id: number): void {
    if (confirm('Delete this product?')) {
      this.productService.deleteProduct(id).subscribe(() => {
        this.loadProducts();
      });
    }
  }
  
  cancelEdit(): void {
    this.editMode = false;
    this.editId = undefined;
    this.productForm.reset();
  }
}
```

---

## Summary

✅ **CREATE**: POST request to add new data

✅ **READ**: GET request to fetch data

✅ **UPDATE**: PUT request to modify existing data

✅ **DELETE**: DELETE request to remove data

✅ **JSON Server**: Quick mock REST API for development

CRUD operations are the foundation of data-driven applications!
