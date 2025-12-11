# Angular Pipes - Complete Guide

> Timestamps: **3:31:46** - Built-in Pipes | **3:58:23** - Custom Pipes | **4:11:07** - Pure & Impure Pipes

## Table of Contents
1. [Introduction](#introduction)
2. [Built-in Pipes](#built-in-pipes)
3. [Creating Custom Pipes](#creating-custom-pipes)
4. [Pure vs Impure Pipes](#pure-vs-impure-pipes)
5. [Chaining Pipes](#chaining-pipes)
6. [Complete Examples](#complete-examples)

---

## Introduction

Pipes transform displayed values within a template. They:
- Format data for display (dates, numbers, currency)
- Filter and sort arrays
- Are reusable across components
- Can be chained together
- Accept parameters for customization

**Syntax**: `{{ value | pipeName:param1:param2 }}`

---

## Built-in Pipes

### 1. DatePipe - Format Dates

```typescript
import { Component } from '@angular/core';
import { DatePipe } from '@angular/common';

@Component({
  selector: 'app-date-demo',
  standalone: true,
  imports: [DatePipe],
  template: `
    <div class="date-examples">
      <h3>Date Pipe Examples</h3>
      
      <p>Default: {{ today | date }}</p>
      <p>Short: {{ today | date:'short' }}</p>
      <p>Medium: {{ today | date:'medium' }}</p>
      <p>Long: {{ today | date:'long' }}</p>
      <p>Full: {{ today | date:'full' }}</p>
      
      <h4>Custom Formats:</h4>
      <p>MM/dd/yyyy: {{ today | date:'MM/dd/yyyy' }}</p>
      <p>dd-MM-yyyy HH:mm: {{ today | date:'dd-MM-yyyy HH:mm' }}</p>
      <p>EEEE, MMMM d, y: {{ today | date:'EEEE, MMMM d, y' }}</p>
      <p>h:mm a: {{ today | date:'h:mm a' }}</p>
    </div>
  `
})
export class DateDemoComponent {
  today = new Date();
}
```

### 2. CurrencyPipe - Format Currency

```typescript
import { Component } from '@angular/core';
import { CurrencyPipe } from '@angular/common';

@Component({
  selector: 'app-currency-demo',
  standalone: true,
  imports: [CurrencyPipe],
  template: `
    <div class="currency-examples">
      <h3>Currency Pipe Examples</h3>
      
      <p>Default USD: {{ price | currency }}</p>
      <p>EUR: {{ price | currency:'EUR' }}</p>
      <p>INR: {{ price | currency:'INR' }}</p>
      <p>GBP with code: {{ price | currency:'GBP':'code' }}</p>
      <p>Custom format: {{ price | currency:'USD':'symbol':'1.2-2' }}</p>
      
      <h4>Product List:</h4>
      @for (product of products; track product.id) {
        <div class="product">
          <span>{{ product.name }}</span>
          <span>{{ product.price | currency:'USD':'symbol':'1.2-2' }}</span>
        </div>
      }
    </div>
  `,
  styles: [`
    .product {
      display: flex;
      justify-content: space-between;
      padding: 0.5rem;
      border-bottom: 1px solid #ddd;
    }
  `]
})
export class CurrencyDemoComponent {
  price = 1234.56;
  products = [
    { id: 1, name: 'Laptop', price: 999.99 },
    { id: 2, name: 'Mouse', price: 29.95 },
    { id: 3, name: 'Keyboard', price: 79.50 }
  ];
}
```

### 3. DecimalPipe - Format Numbers

```typescript
import { Component } from '@angular/core';
import { DecimalPipe } from '@angular/common';

@Component({
  selector: 'app-decimal-demo',
  standalone: true,
  imports: [DecimalPipe],
  template: `
    <div class="decimal-examples">
      <h3>Decimal Pipe Examples</h3>
      
      <p>Default: {{ number | number }}</p>
      <p>1.0-0 (no decimals): {{ number | number:'1.0-0' }}</p>
      <p>1.2-2 (2 decimals): {{ number | number:'1.2-2' }}</p>
      <p>3.1-5 (1-5 decimals): {{ number | number:'3.1-5' }}</p>
      
      <h4>Statistics:</h4>
      <p>Average: {{ average | number:'1.2-2' }}</p>
      <p>Percentage: {{ percentage | number:'1.1-1' }}%</p>
    </div>
  `
})
export class DecimalDemoComponent {
  number = 1234.567890;
  average = 87.456;
  percentage = 65.4;
}
```

### 4. PercentPipe - Format Percentages

```typescript
import { Component } from '@angular/core';
import { PercentPipe } from '@angular/common';

@Component({
  selector: 'app-percent-demo',
  standalone: true,
  imports: [PercentPipe],
  template: `
    <div class="percent-examples">
      <h3>Percent Pipe Examples</h3>
      
      <p>Default: {{ ratio | percent }}</p>
      <p>Custom: {{ ratio | percent:'1.2-2' }}</p>
      
      <h4>Progress Bars:</h4>
      @for (task of tasks; track task.id) {
        <div class="task">
          <span>{{ task.name }}</span>
          <div class="progress-bar">
            <div class="progress" [style.width.%]="task.progress * 100">
              {{ task.progress | percent }}
            </div>
          </div>
        </div>
      }
    </div>
  `,
  styles: [`
    .task {
      margin: 1rem 0;
    }
    .progress-bar {
      width: 100%;
      height: 30px;
      background: #f0f0f0;
      border-radius: 4px;
      overflow: hidden;
    }
    .progress {
      height: 100%;
      background: #4caf50;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      transition: width 0.3s;
    }
  `]
})
export class PercentDemoComponent {
  ratio = 0.759;
  tasks = [
    { id: 1, name: 'Design', progress: 1.0 },
    { id: 2, name: 'Development', progress: 0.65 },
    { id: 3, name: 'Testing', progress: 0.30 }
  ];
}
```

### 5. UpperCasePipe / LowerCasePipe / TitleCasePipe

```typescript
import { Component } from '@angular/core';
import { UpperCasePipe, LowerCasePipe, TitleCasePipe } from '@angular/common';

@Component({
  selector: 'app-case-demo',
  standalone: true,
  imports: [UpperCasePipe, LowerCasePipe, TitleCasePipe],
  template: `
    <div class="case-examples">
      <h3>Case Transformation Pipes</h3>
      
      <p>Original: {{ text }}</p>
      <p>Uppercase: {{ text | uppercase }}</p>
      <p>Lowercase: {{ text | lowercase }}</p>
      <p>Titlecase: {{ text | titlecase }}</p>
      
      <h4>Names:</h4>
      @for (name of names; track name) {
        <p>{{ name | titlecase }}</p>
      }
    </div>
  `
})
export class CaseDemoComponent {
  text = 'Hello Angular World';
  names = ['john doe', 'jane smith', 'bob johnson'];
}
```

### 6. JsonPipe - Debug Objects

```typescript
import { Component } from '@angular/core';
import { JsonPipe } from '@angular/common';

@Component({
  selector: 'app-json-demo',
  standalone: true,
  imports: [JsonPipe],
  template: `
    <div class="json-examples">
      <h3>JSON Pipe (Debug)</h3>
      <pre>{{ user | json }}</pre>
      <pre>{{ products | json }}</pre>
    </div>
  `,
  styles: [`
    pre {
      background: #f5f5f5;
      padding: 1rem;
      border-radius: 4px;
      overflow-x: auto;
    }
  `]
})
export class JsonDemoComponent {
  user = { name: 'John', age: 30, email: 'john@example.com' };
  products = [
    { id: 1, name: 'Product 1', price: 99 },
    { id: 2, name: 'Product 2', price: 149 }
  ];
}
```

### 7. SlicePipe - Extract Array/String Portions

```typescript
import { Component } from '@angular/core';
import { SlicePipe } from '@angular/common';

@Component({
  selector: 'app-slice-demo',
  standalone: true,
  imports: [SlicePipe],
  template: `
    <div class="slice-examples">
      <h3>Slice Pipe Examples</h3>
      
      <h4>String Slicing:</h4>
      <p>Full: {{ text }}</p>
      <p>First 10: {{ text | slice:0:10 }}</p>
      <p>Last 5: {{ text | slice:-5 }}</p>
      
      <h4>Array Slicing (Pagination):</h4>
      <p>Showing {{ currentPage * pageSize + 1 }} - 
         {{ (currentPage + 1) * pageSize }}</p>
      <ul>
        @for (item of items | slice:(currentPage * pageSize):((currentPage + 1) * pageSize); track item.id) {
          <li>{{ item.name }}</li>
        }
      </ul>
      <button (click)="prevPage()" [disabled]="currentPage === 0">Previous</button>
      <button (click)="nextPage()" [disabled]="(currentPage + 1) * pageSize >= items.length">Next</button>
    </div>
  `
})
export class SliceDemoComponent {
  text = 'The quick brown fox jumps over the lazy dog';
  items = Array.from({ length: 50 }, (_, i) => ({ 
    id: i + 1, 
    name: `Item ${i + 1}` 
  }));
  
  currentPage = 0;
  pageSize = 10;
  
  nextPage(): void {
    if ((this.currentPage + 1) * this.pageSize < this.items.length) {
      this.currentPage++;
    }
  }
  
  prevPage(): void {
    if (this.currentPage > 0) {
      this.currentPage--;
    }
  }
}
```

---

## Creating Custom Pipes

### Basic Custom Pipe

```typescript
// exponential.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'exponential',
  standalone: true
})
export class ExponentialPipe implements PipeTransform {
  transform(value: number, exponent: number = 2): number {
    return Math.pow(value, exponent);
  }
}

// Usage
@Component({
  selector: 'app-math',
  standalone: true,
  imports: [ExponentialPipe],
  template: `
    <p>2^3 = {{ 2 | exponential:3 }}</p>
    <p>5^2 = {{ 5 | exponential }}</p>
  `
})
export class MathComponent { }
```

### Custom String Pipe

```typescript
// truncate.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'truncate',
  standalone: true
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 20, ellipsis: string = '...'): string {
    if (!value) return '';
    if (value.length <= limit) return value;
    return value.substring(0, limit) + ellipsis;
  }
}

// Usage
@Component({
  selector: 'app-text',
  standalone: true,
  imports: [TruncatePipe],
  template: `
    <p>{{ longText | truncate:50 }}</p>
    <p>{{ longText | truncate:30:'... Read more' }}</p>
  `
})
export class TextComponent {
  longText = 'This is a very long text that needs to be truncated for display purposes';
}
```

### Custom Filter Pipe

```typescript
// filter.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filter',
  standalone: true
})
export class FilterPipe implements PipeTransform {
  transform(items: any[], searchText: string, property: string): any[] {
    if (!items || !searchText) {
      return items;
    }
    
    searchText = searchText.toLowerCase();
    
    return items.filter(item => {
      const value = item[property];
      return value && value.toString().toLowerCase().includes(searchText);
    });
  }
}

// Usage
@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [FilterPipe, FormsModule],
  template: `
    <div class="product-list">
      <input [(ngModel)]="searchTerm" placeholder="Search products...">
      
      <div class="products">
        @for (product of products | filter:searchTerm:'name'; track product.id) {
          <div class="product-card">
            <h4>{{ product.name }}</h4>
            <p>{{ product.price | currency }}</p>
          </div>
        }
      </div>
    </div>
  `
})
export class ProductListComponent {
  searchTerm = '';
  products = [
    { id: 1, name: 'Laptop', price: 999 },
    { id: 2, name: 'Mouse', price: 29 },
    { id: 3, name: 'Keyboard', price: 79 },
    { id: 4, name: 'Monitor', price: 299 }
  ];
}
```

### Custom Sort Pipe

```typescript
// sort.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'sort',
  standalone: true
})
export class SortPipe implements PipeTransform {
  transform(array: any[], field: string, order: 'asc' | 'desc' = 'asc'): any[] {
    if (!Array.isArray(array) || !field) {
      return array;
    }
    
    const sorted = [...array].sort((a, b) => {
      const aVal = a[field];
      const bVal = b[field];
      
      if (aVal < bVal) return order === 'asc' ? -1 : 1;
      if (aVal > bVal) return order === 'asc' ? 1 : -1;
      return 0;
    });
    
    return sorted;
  }
}

// Usage
@Component({
  selector: 'app-user-list',
  standalone: true,
  imports: [SortPipe],
  template: `
    <div class="user-list">
      <button (click)="sortBy('name')">Sort by Name</button>
      <button (click)="sortBy('age')">Sort by Age</button>
      <button (click)="toggleOrder()">Toggle Order</button>
      
      <table>
        <tr>
          <th>Name</th>
          <th>Age</th>
          <th>Email</th>
        </tr>
        @for (user of users | sort:sortField:sortOrder; track user.id) {
          <tr>
            <td>{{ user.name }}</td>
            <td>{{ user.age }}</td>
            <td>{{ user.email }}</td>
          </tr>
        }
      </table>
    </div>
  `
})
export class UserListComponent {
  sortField = 'name';
  sortOrder: 'asc' | 'desc' = 'asc';
  
  users = [
    { id: 1, name: 'John', age: 30, email: 'john@example.com' },
    { id: 2, name: 'Alice', age: 25, email: 'alice@example.com' },
    { id: 3, name: 'Bob', age: 35, email: 'bob@example.com' }
  ];
  
  sortBy(field: string): void {
    this.sortField = field;
  }
  
  toggleOrder(): void {
    this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc';
  }
}
```

---

## Pure vs Impure Pipes

### Pure Pipes (Default)

**Definition**: Only executes when Angular detects a pure change to the input value.

**Pure Changes**:
- Primitive value changes (number, string, boolean)
- Object reference changes

```typescript
@Pipe({
  name: 'purePipe',
  standalone: true,
  pure: true // Default
})
export class PurePipe implements PipeTransform {
  transform(value: any): any {
    console.log('Pure pipe executed');
    return value;
  }
}
```

**Example**:

```typescript
@Component({
  selector: 'app-pure-demo',
  standalone: true,
  imports: [FilterPipe],
  template: `
    <div>
      <input [(ngModel)]="searchTerm">
      
      <!-- Won't update when array items change (same reference) -->
      @for (item of items | filter:searchTerm:'name'; track item.id) {
        <p>{{ item.name }}</p>
      }
      
      <button (click)="addItem()">Add Item</button>
    </div>
  `
})
export class PureDemoComponent {
  searchTerm = '';
  items = [{ id: 1, name: 'Item 1' }];
  
  addItem(): void {
    // Pure pipe won't detect this change!
    this.items.push({ id: this.items.length + 1, name: `Item ${this.items.length + 1}` });
    
    // To make it work with pure pipe, create new reference:
    // this.items = [...this.items, newItem];
  }
}
```

### Impure Pipes

**Definition**: Executes on every change detection cycle.

**Use Cases**:
- Filtering/sorting arrays that change internally
- Async operations
- Frequent updates needed

**Warning**: Can impact performance if overused!

```typescript
@Pipe({
  name: 'impureFilter',
  standalone: true,
  pure: false // Impure pipe
})
export class ImpureFilterPipe implements PipeTransform {
  transform(items: any[], searchText: string): any[] {
    console.log('Impure pipe executed - runs on every change detection!');
    
    if (!items || !searchText) {
      return items;
    }
    
    return items.filter(item => 
      item.name.toLowerCase().includes(searchText.toLowerCase())
    );
  }
}

@Component({
  selector: 'app-impure-demo',
  standalone: true,
  imports: [ImpureFilterPipe, FormsModule],
  template: `
    <div>
      <input [(ngModel)]="searchTerm">
      
      <!-- Will update even when array items change internally -->
      @for (item of items | impureFilter:searchTerm; track item.id) {
        <p>{{ item.name }}</p>
      }
      
      <button (click)="addItem()">Add Item</button>
    </div>
  `
})
export class ImpureDemoComponent {
  searchTerm = '';
  items = [{ id: 1, name: 'Item 1' }];
  
  addItem(): void {
    // Impure pipe will detect this!
    this.items.push({ 
      id: this.items.length + 1, 
      name: `Item ${this.items.length + 1}` 
    });
  }
}
```

### Pure vs Impure Comparison

| Aspect | Pure Pipe | Impure Pipe |
|--------|-----------|-------------|
| **Execution** | Only on input/reference change | Every change detection cycle |
| **Performance** | Better | Can be worse |
| **Use Case** | Primitive values, immutable data | Mutable data, async operations |
| **Default** | Yes (`pure: true`) | No (`pure: false`) |
| **Example** | `currency`, `date`, `uppercase` | `async`, custom array filters |

---

## Chaining Pipes

Combine multiple pipes for complex transformations:

```typescript
@Component({
  selector: 'app-pipe-chain',
  standalone: true,
  imports: [DatePipe, UpperCasePipe, SlicePipe, CurrencyPipe],
  template: `
    <div class="pipe-chain">
      <h3>Chaining Pipes</h3>
      
      <!-- Date + Uppercase -->
      <p>{{ today | date:'fullDate' | uppercase }}</p>
      
      <!-- Slice + Titlecase -->
      <p>{{ text | slice:0:20 | titlecase }}</p>
      
      <!-- Multiple transformations -->
      <p>{{ longText | slice:0:50 | lowercase }}</p>
      
      <!-- Price formatting chain -->
      @for (product of products; track product.id) {
        <div>
          <span>{{ product.name | uppercase }}</span>
          <span>{{ product.price | currency:'USD':'symbol':'1.2-2' }}</span>
        </div>
      }
    </div>
  `
})
export class PipeChainComponent {
  today = new Date();
  text = 'the quick brown fox';
  longText = 'This is a very long text that will be sliced and transformed';
  products = [
    { id: 1, name: 'laptop', price: 999.99 },
    { id: 2, name: 'mouse', price: 29.95 }
  ];
}
```

---

## Complete Examples

### Example 1: E-Commerce Product Display

```typescript
import { Component } from '@angular/core';
import { CurrencyPipe, DatePipe, PercentPipe } from '@angular/common';
import { FilterPipe } from './filter.pipe';
import { SortPipe } from './sort.pipe';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-product-catalog',
  standalone: true,
  imports: [CurrencyPipe, DatePipe, PercentPipe, FilterPipe, SortPipe, FormsModule],
  template: `
    <div class="catalog">
      <h2>Product Catalog</h2>
      
      <div class="filters">
        <input 
          [(ngModel)]="searchTerm" 
          placeholder="Search products..."
        >
        <select [(ngModel)]="sortField">
          <option value="name">Sort by Name</option>
          <option value="price">Sort by Price</option>
          <option value="rating">Sort by Rating</option>
        </select>
        <button (click)="toggleSortOrder()">
          {{ sortOrder === 'asc' ? '↑' : '↓' }}
        </button>
      </div>
      
      <div class="products">
        @for (product of products | filter:searchTerm:'name' | sort:sortField:sortOrder; track product.id) {
          <div class="product-card">
            <img [src]="product.image" [alt]="product.name">
            <h3>{{ product.name | titlecase }}</h3>
            <p class="description">{{ product.description | slice:0:100 }}...</p>
            <p class="price">{{ product.price | currency:'USD':'symbol':'1.2-2' }}</p>
            @if (product.discount > 0) {
              <p class="discount">{{ product.discount | percent }} OFF</p>
              <p class="final-price">
                {{ product.price * (1 - product.discount) | currency:'USD':'symbol':'1.2-2' }}
              </p>
            }
            <p class="rating">Rating: {{ product.rating | number:'1.1-1' }}/5</p>
            <p class="date">Added: {{ product.addedDate | date:'MMM d, y' }}</p>
          </div>
        }
      </div>
    </div>
  `,
  styles: [`
    .catalog {
      padding: 2rem;
    }
    
    .filters {
      display: flex;
      gap: 1rem;
      margin-bottom: 2rem;
      padding: 1rem;
      background: #f5f5f5;
      border-radius: 8px;
    }
    
    .filters input,
    .filters select {
      padding: 0.5rem;
      border: 1px solid #ddd;
      border-radius: 4px;
      flex: 1;
    }
    
    .products {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
      gap: 1.5rem;
    }
    
    .product-card {
      border: 1px solid #ddd;
      border-radius: 8px;
      padding: 1rem;
      transition: transform 0.2s;
    }
    
    .product-card:hover {
      transform: translateY(-4px);
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    }
    
    .product-card img {
      width: 100%;
      height: 200px;
      object-fit: cover;
      border-radius: 4px;
    }
    
    .price {
      font-size: 1.5rem;
      font-weight: bold;
      color: #4caf50;
    }
    
    .discount {
      color: #f44336;
      font-weight: bold;
    }
    
    .final-price {
      text-decoration: line-through;
      color: #999;
    }
  `]
})
export class ProductCatalogComponent {
  searchTerm = '';
  sortField = 'name';
  sortOrder: 'asc' | 'desc' = 'asc';
  
  products = [
    {
      id: 1,
      name: 'wireless mouse',
      description: 'Ergonomic wireless mouse with precision tracking',
      price: 29.99,
      discount: 0.15,
      rating: 4.5,
      addedDate: new Date('2024-01-15'),
      image: 'https://via.placeholder.com/250'
    },
    {
      id: 2,
      name: 'mechanical keyboard',
      description: 'RGB mechanical keyboard with cherry MX switches',
      price: 149.99,
      discount: 0,
      rating: 4.8,
      addedDate: new Date('2024-02-20'),
      image: 'https://via.placeholder.com/250'
    },
    // Add more products...
  ];
  
  toggleSortOrder(): void {
    this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc';
  }
}
```

---

## Best Practices

### 1. Use Pure Pipes When Possible

```typescript
// ✅ Good - pure pipe with immutable data
this.items = [...this.items, newItem];

// ❌ Avoid - requires impure pipe
this.items.push(newItem);
```

### 2. Keep Pipes Simple

```typescript
// ✅ Good - simple, focused
@Pipe({ name: 'truncate' })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number): string {
    return value.length > limit ? value.slice(0, limit) + '...' : value;
  }
}

// ❌ Bad - too complex
@Pipe({ name: 'complexTransform' })
export class ComplexPipe implements PipeTransform {
  transform(value: any, ...args: any[]): any {
    // Multiple responsibilities, hard to test
  }
}
```

### 3. Avoid Heavy Computations in Impure Pipes

```typescript
// ❌ Bad - expensive operation in impure pipe
@Pipe({ name: 'heavyFilter', pure: false })
export class HeavyFilterPipe implements PipeTransform {
  transform(items: any[]): any[] {
    // Heavy computation on every change detection!
    return this.expensiveOperation(items);
  }
}

// ✅ Good - use component method or service
```

### 4. Test Your Pipes

```typescript
import { TruncatePipe } from './truncate.pipe';

describe('TruncatePipe', () => {
  let pipe: TruncatePipe;
  
  beforeEach(() => {
    pipe = new TruncatePipe();
  });
  
  it('should truncate long text', () => {
    expect(pipe.transform('Hello World', 5)).toBe('Hello...');
  });
  
  it('should not truncate short text', () => {
    expect(pipe.transform('Hi', 5)).toBe('Hi');
  });
});
```

---

## Summary

✅ **Built-in Pipes**: Date, Currency, Decimal, Percent, Case, JSON, Slice

✅ **Custom Pipes**: Implement `PipeTransform` interface

✅ **Pure Pipes**: Better performance, default behavior

✅ **Impure Pipes**: Run on every change detection, use sparingly

✅ **Chaining**: Combine pipes with `|` operator

✅ **Best Practices**: Keep pipes simple, prefer pure, test thoroughly

Pipes are powerful tools for transforming data in Angular templates!
