# Real-World Example: Complete CRUD Application

## Project Overview

A full-featured Employee Management System demonstrating TypeScript and Angular concepts in a real-world scenario.

### Features
- Employee CRUD operations (Create, Read, Update, Delete)
- Form validation
- Search and filtering
- Sorting
- Pagination
- Service-based architecture
- Reactive programming with RxJS
- HTTP communication
- Error handling

## Project Structure

```
src/app/
├── core/
│   ├── models/
│   │   └── employee.model.ts
│   ├── services/
│   │   ├── employee.service.ts
│   │   └── notification.service.ts
│   └── interceptors/
│       └── error.interceptor.ts
├── features/
│   └── employees/
│       ├── employee-list/
│       ├── employee-detail/
│       ├── employee-form/
│       └── employees.module.ts
├── shared/
│   ├── components/
│   │   ├── pagination/
│   │   └── search-bar/
│   ├── pipes/
│   │   └── filter.pipe.ts
│   └── validators/
│       └── custom-validators.ts
└── app.module.ts
```

## 1. Data Models

### employee.model.ts

```typescript
export interface Employee {
  id: string;
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
  department: Department;
  position: string;
  salary: number;
  hireDate: Date;
  isActive: boolean;
  address: Address;
}

export interface Address {
  street: string;
  city: string;
  state: string;
  zipCode: string;
  country: string;
}

export enum Department {
  Engineering = 'ENGINEERING',
  Sales = 'SALES',
  Marketing = 'MARKETING',
  HR = 'HR',
  Finance = 'FINANCE',
  Operations = 'OPERATIONS'
}

export interface EmployeeFilter {
  searchTerm?: string;
  department?: Department;
  isActive?: boolean;
  minSalary?: number;
  maxSalary?: number;
}

export interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  pageSize: number;
  totalPages: number;
}

export interface EmployeeFormData {
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
  department: Department;
  position: string;
  salary: number;
  hireDate: Date;
  address: Address;
}
```

## 2. Services

### employee.service.ts

```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable, BehaviorSubject, throwError } from 'rxjs';
import { map, catchError, tap, delay } from 'rxjs/operators';
import { Employee, EmployeeFilter, PaginatedResponse, EmployeeFormData } from '../models/employee.model';

@Injectable({
  providedIn: 'root'
})
export class EmployeeService {
  private apiUrl = 'https://api.example.com/employees';
  
  // Local state management
  private employeesSubject = new BehaviorSubject<Employee[]>([]);
  public employees$ = this.employeesSubject.asObservable();
  
  private loadingSubject = new BehaviorSubject<boolean>(false);
  public loading$ = this.loadingSubject.asObservable();
  
  private errorSubject = new BehaviorSubject<string | null>(null);
  public error$ = this.errorSubject.asObservable();
  
  constructor(private http: HttpClient) {}
  
  /**
   * Get all employees with optional filtering and pagination
   */
  getEmployees(
    page: number = 1,
    pageSize: number = 10,
    filter?: EmployeeFilter,
    sortBy?: string,
    sortOrder: 'asc' | 'desc' = 'asc'
  ): Observable<PaginatedResponse<Employee>> {
    this.loadingSubject.next(true);
    
    let params = new HttpParams()
      .set('page', page.toString())
      .set('pageSize', pageSize.toString());
    
    if (sortBy) {
      params = params.set('sortBy', sortBy).set('sortOrder', sortOrder);
    }
    
    if (filter) {
      if (filter.searchTerm) {
        params = params.set('search', filter.searchTerm);
      }
      if (filter.department) {
        params = params.set('department', filter.department);
      }
      if (filter.isActive !== undefined) {
        params = params.set('isActive', filter.isActive.toString());
      }
      if (filter.minSalary) {
        params = params.set('minSalary', filter.minSalary.toString());
      }
      if (filter.maxSalary) {
        params = params.set('maxSalary', filter.maxSalary.toString());
      }
    }
    
    return this.http.get<PaginatedResponse<Employee>>(this.apiUrl, { params }).pipe(
      tap(response => {
        this.employeesSubject.next(response.data);
        this.loadingSubject.next(false);
        this.errorSubject.next(null);
      }),
      catchError(error => {
        this.loadingSubject.next(false);
        this.errorSubject.next('Failed to load employees');
        return throwError(() => error);
      })
    );
  }
  
  /**
   * Get a single employee by ID
   */
  getEmployeeById(id: string): Observable<Employee> {
    this.loadingSubject.next(true);
    
    return this.http.get<Employee>(`${this.apiUrl}/${id}`).pipe(
      tap(() => {
        this.loadingSubject.next(false);
        this.errorSubject.next(null);
      }),
      catchError(error => {
        this.loadingSubject.next(false);
        this.errorSubject.next('Failed to load employee');
        return throwError(() => error);
      })
    );
  }
  
  /**
   * Create a new employee
   */
  createEmployee(employeeData: EmployeeFormData): Observable<Employee> {
    this.loadingSubject.next(true);
    
    const employee: Partial<Employee> = {
      ...employeeData,
      id: this.generateId(),
      isActive: true
    };
    
    return this.http.post<Employee>(this.apiUrl, employee).pipe(
      tap(newEmployee => {
        const currentEmployees = this.employeesSubject.value;
        this.employeesSubject.next([...currentEmployees, newEmployee]);
        this.loadingSubject.next(false);
        this.errorSubject.next(null);
      }),
      catchError(error => {
        this.loadingSubject.next(false);
        this.errorSubject.next('Failed to create employee');
        return throwError(() => error);
      })
    );
  }
  
  /**
   * Update an existing employee
   */
  updateEmployee(id: string, updates: Partial<Employee>): Observable<Employee> {
    this.loadingSubject.next(true);
    
    return this.http.put<Employee>(`${this.apiUrl}/${id}`, updates).pipe(
      tap(updatedEmployee => {
        const currentEmployees = this.employeesSubject.value;
        const index = currentEmployees.findIndex(e => e.id === id);
        if (index !== -1) {
          currentEmployees[index] = updatedEmployee;
          this.employeesSubject.next([...currentEmployees]);
        }
        this.loadingSubject.next(false);
        this.errorSubject.next(null);
      }),
      catchError(error => {
        this.loadingSubject.next(false);
        this.errorSubject.next('Failed to update employee');
        return throwError(() => error);
      })
    );
  }
  
  /**
   * Delete an employee
   */
  deleteEmployee(id: string): Observable<void> {
    this.loadingSubject.next(true);
    
    return this.http.delete<void>(`${this.apiUrl}/${id}`).pipe(
      tap(() => {
        const currentEmployees = this.employeesSubject.value;
        this.employeesSubject.next(currentEmployees.filter(e => e.id !== id));
        this.loadingSubject.next(false);
        this.errorSubject.next(null);
      }),
      catchError(error => {
        this.loadingSubject.next(false);
        this.errorSubject.next('Failed to delete employee');
        return throwError(() => error);
      })
    );
  }
  
  /**
   * Toggle employee active status
   */
  toggleEmployeeStatus(id: string): Observable<Employee> {
    return this.getEmployeeById(id).pipe(
      map(employee => ({ ...employee, isActive: !employee.isActive })),
      switchMap(updatedEmployee => this.updateEmployee(id, updatedEmployee))
    );
  }
  
  /**
   * Search employees by term
   */
  searchEmployees(term: string): Observable<Employee[]> {
    if (!term.trim()) {
      return of([]);
    }
    
    return this.http.get<Employee[]>(`${this.apiUrl}/search`, {
      params: new HttpParams().set('q', term)
    });
  }
  
  /**
   * Get employees by department
   */
  getEmployeesByDepartment(department: Department): Observable<Employee[]> {
    return this.http.get<Employee[]>(`${this.apiUrl}/department/${department}`);
  }
  
  /**
   * Get department statistics
   */
  getDepartmentStats(): Observable<Record<Department, number>> {
    return this.http.get<Record<Department, number>>(`${this.apiUrl}/stats/department`);
  }
  
  /**
   * Export employees to CSV
   */
  exportToCSV(filter?: EmployeeFilter): Observable<Blob> {
    let params = new HttpParams();
    
    if (filter) {
      // Add filter params
    }
    
    return this.http.get(`${this.apiUrl}/export`, {
      params,
      responseType: 'blob'
    });
  }
  
  /**
   * Generate unique ID (mock - in real app, backend handles this)
   */
  private generateId(): string {
    return `emp_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
  }
}
```

### notification.service.ts

```typescript
import { Injectable } from '@angular/core';
import { Subject, Observable } from 'rxjs';

export interface Notification {
  id: string;
  type: 'success' | 'error' | 'warning' | 'info';
  message: string;
  duration?: number;
}

@Injectable({
  providedIn: 'root'
})
export class NotificationService {
  private notificationSubject = new Subject<Notification>();
  public notifications$: Observable<Notification> = this.notificationSubject.asObservable();
  
  success(message: string, duration: number = 3000): void {
    this.show('success', message, duration);
  }
  
  error(message: string, duration: number = 5000): void {
    this.show('error', message, duration);
  }
  
  warning(message: string, duration: number = 4000): void {
    this.show('warning', message, duration);
  }
  
  info(message: string, duration: number = 3000): void {
    this.show('info', message, duration);
  }
  
  private show(type: Notification['type'], message: string, duration: number): void {
    const notification: Notification = {
      id: this.generateId(),
      type,
      message,
      duration
    };
    
    this.notificationSubject.next(notification);
  }
  
  private generateId(): string {
    return `notification_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
  }
}
```

## 3. Custom Validators

### custom-validators.ts

```typescript
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

export class CustomValidators {
  /**
   * Validate phone number format
   */
  static phoneNumber(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) {
        return null;
      }
      
      const phoneRegex = /^\+?1?\d{9,15}$/;
      const valid = phoneRegex.test(control.value);
      
      return valid ? null : { invalidPhone: { value: control.value } };
    };
  }
  
  /**
   * Validate salary range
   */
  static salaryRange(min: number, max: number): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) {
        return null;
      }
      
      const salary = Number(control.value);
      
      if (salary < min) {
        return { salaryTooLow: { min, actual: salary } };
      }
      
      if (salary > max) {
        return { salaryTooHigh: { max, actual: salary } };
      }
      
      return null;
    };
  }
  
  /**
   * Validate hire date (not in future)
   */
  static notFutureDate(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) {
        return null;
      }
      
      const inputDate = new Date(control.value);
      const today = new Date();
      today.setHours(0, 0, 0, 0);
      
      return inputDate > today ? { futureDate: true } : null;
    };
  }
  
  /**
   * Validate zip code
   */
  static zipCode(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) {
        return null;
      }
      
      const zipRegex = /^\d{5}(-\d{4})?$/;
      const valid = zipRegex.test(control.value);
      
      return valid ? null : { invalidZipCode: { value: control.value } };
    };
  }
}
```

## 4. Employee Form Component

### employee-form.component.ts

```typescript
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Employee, Department, EmployeeFormData } from '../../models/employee.model';
import { CustomValidators } from '../../../shared/validators/custom-validators';

@Component({
  selector: 'app-employee-form',
  templateUrl: './employee-form.component.html',
  styleUrls: ['./employee-form.component.css']
})
export class EmployeeFormComponent implements OnInit {
  @Input() employee?: Employee;
  @Input() mode: 'create' | 'edit' = 'create';
  @Output() submitForm = new EventEmitter<EmployeeFormData>();
  @Output() cancel = new EventEmitter<void>();
  
  employeeForm!: FormGroup;
  departments = Object.values(Department);
  submitted = false;
  
  constructor(private fb: FormBuilder) {}
  
  ngOnInit(): void {
    this.initializeForm();
    
    if (this.employee && this.mode === 'edit') {
      this.populateForm(this.employee);
    }
  }
  
  private initializeForm(): void {
    this.employeeForm = this.fb.group({
      firstName: ['', [Validators.required, Validators.minLength(2)]],
      lastName: ['', [Validators.required, Validators.minLength(2)]],
      email: ['', [Validators.required, Validators.email]],
      phoneNumber: ['', [Validators.required, CustomValidators.phoneNumber()]],
      department: ['', Validators.required],
      position: ['', [Validators.required, Validators.minLength(3)]],
      salary: ['', [
        Validators.required,
        Validators.min(0),
        CustomValidators.salaryRange(20000, 500000)
      ]],
      hireDate: ['', [Validators.required, CustomValidators.notFutureDate()]],
      address: this.fb.group({
        street: ['', Validators.required],
        city: ['', Validators.required],
        state: ['', [Validators.required, Validators.minLength(2)]],
        zipCode: ['', [Validators.required, CustomValidators.zipCode()]],
        country: ['', Validators.required]
      })
    });
  }
  
  private populateForm(employee: Employee): void {
    this.employeeForm.patchValue({
      firstName: employee.firstName,
      lastName: employee.lastName,
      email: employee.email,
      phoneNumber: employee.phoneNumber,
      department: employee.department,
      position: employee.position,
      salary: employee.salary,
      hireDate: this.formatDate(employee.hireDate),
      address: employee.address
    });
  }
  
  onSubmit(): void {
    this.submitted = true;
    
    if (this.employeeForm.invalid) {
      this.markFormGroupTouched(this.employeeForm);
      return;
    }
    
    const formData: EmployeeFormData = this.employeeForm.value;
    this.submitForm.emit(formData);
  }
  
  onCancel(): void {
    this.cancel.emit();
  }
  
  onReset(): void {
    this.submitted = false;
    this.employeeForm.reset();
  }
  
  // Helper methods for template
  
  get f() {
    return this.employeeForm.controls;
  }
  
  get addressForm() {
    return this.employeeForm.get('address') as FormGroup;
  }
  
  isFieldInvalid(fieldName: string): boolean {
    const field = this.employeeForm.get(fieldName);
    return !!(field && field.invalid && (field.dirty || field.touched || this.submitted));
  }
  
  getFieldError(fieldName: string): string {
    const field = this.employeeForm.get(fieldName);
    
    if (!field || !field.errors) {
      return '';
    }
    
    const errors = field.errors;
    
    if (errors['required']) {
      return 'This field is required';
    }
    if (errors['minlength']) {
      return `Minimum length is ${errors['minlength'].requiredLength}`;
    }
    if (errors['email']) {
      return 'Invalid email address';
    }
    if (errors['min']) {
      return `Minimum value is ${errors['min'].min}`;
    }
    if (errors['invalidPhone']) {
      return 'Invalid phone number format';
    }
    if (errors['salaryTooLow']) {
      return `Salary must be at least $${errors['salaryTooLow'].min}`;
    }
    if (errors['salaryTooHigh']) {
      return `Salary cannot exceed $${errors['salaryTooHigh'].max}`;
    }
    if (errors['futureDate']) {
      return 'Hire date cannot be in the future';
    }
    if (errors['invalidZipCode']) {
      return 'Invalid zip code format';
    }
    
    return 'Invalid field';
  }
  
  private markFormGroupTouched(formGroup: FormGroup): void {
    Object.keys(formGroup.controls).forEach(key => {
      const control = formGroup.get(key);
      control?.markAsTouched();
      
      if (control instanceof FormGroup) {
        this.markFormGroupTouched(control);
      }
    });
  }
  
  private formatDate(date: Date): string {
    const d = new Date(date);
    const month = String(d.getMonth() + 1).padStart(2, '0');
    const day = String(d.getDate()).padStart(2, '0');
    const year = d.getFullYear();
    return `${year}-${month}-${day}`;
  }
}
```

### employee-form.component.html

```html
<div class="employee-form-container">
  <h2>{{ mode === 'create' ? 'Add New Employee' : 'Edit Employee' }}</h2>
  
  <form [formGroup]="employeeForm" (ngSubmit)="onSubmit()" novalidate>
    <!-- Personal Information -->
    <div class="form-section">
      <h3>Personal Information</h3>
      
      <div class="form-row">
        <div class="form-group">
          <label for="firstName">First Name *</label>
          <input 
            type="text" 
            id="firstName"
            formControlName="firstName"
            [class.is-invalid]="isFieldInvalid('firstName')">
          <div class="error-message" *ngIf="isFieldInvalid('firstName')">
            {{ getFieldError('firstName') }}
          </div>
        </div>
        
        <div class="form-group">
          <label for="lastName">Last Name *</label>
          <input 
            type="text" 
            id="lastName"
            formControlName="lastName"
            [class.is-invalid]="isFieldInvalid('lastName')">
          <div class="error-message" *ngIf="isFieldInvalid('lastName')">
            {{ getFieldError('lastName') }}
          </div>
        </div>
      </div>
      
      <div class="form-row">
        <div class="form-group">
          <label for="email">Email *</label>
          <input 
            type="email" 
            id="email"
            formControlName="email"
            [class.is-invalid]="isFieldInvalid('email')">
          <div class="error-message" *ngIf="isFieldInvalid('email')">
            {{ getFieldError('email') }}
          </div>
        </div>
        
        <div class="form-group">
          <label for="phoneNumber">Phone Number *</label>
          <input 
            type="tel" 
            id="phoneNumber"
            formControlName="phoneNumber"
            placeholder="+1234567890"
            [class.is-invalid]="isFieldInvalid('phoneNumber')">
          <div class="error-message" *ngIf="isFieldInvalid('phoneNumber')">
            {{ getFieldError('phoneNumber') }}
          </div>
        </div>
      </div>
    </div>
    
    <!-- Employment Information -->
    <div class="form-section">
      <h3>Employment Information</h3>
      
      <div class="form-row">
        <div class="form-group">
          <label for="department">Department *</label>
          <select 
            id="department"
            formControlName="department"
            [class.is-invalid]="isFieldInvalid('department')">
            <option value="">Select Department</option>
            <option *ngFor="let dept of departments" [value]="dept">
              {{ dept }}
            </option>
          </select>
          <div class="error-message" *ngIf="isFieldInvalid('department')">
            {{ getFieldError('department') }}
          </div>
        </div>
        
        <div class="form-group">
          <label for="position">Position *</label>
          <input 
            type="text" 
            id="position"
            formControlName="position"
            [class.is-invalid]="isFieldInvalid('position')">
          <div class="error-message" *ngIf="isFieldInvalid('position')">
            {{ getFieldError('position') }}
          </div>
        </div>
      </div>
      
      <div class="form-row">
        <div class="form-group">
          <label for="salary">Salary *</label>
          <input 
            type="number" 
            id="salary"
            formControlName="salary"
            min="0"
            [class.is-invalid]="isFieldInvalid('salary')">
          <div class="error-message" *ngIf="isFieldInvalid('salary')">
            {{ getFieldError('salary') }}
          </div>
        </div>
        
        <div class="form-group">
          <label for="hireDate">Hire Date *</label>
          <input 
            type="date" 
            id="hireDate"
            formControlName="hireDate"
            [class.is-invalid]="isFieldInvalid('hireDate')">
          <div class="error-message" *ngIf="isFieldInvalid('hireDate')">
            {{ getFieldError('hireDate') }}
          </div>
        </div>
      </div>
    </div>
    
    <!-- Address -->
    <div class="form-section" formGroupName="address">
      <h3>Address</h3>
      
      <div class="form-group">
        <label for="street">Street *</label>
        <input 
          type="text" 
          id="street"
          formControlName="street"
          [class.is-invalid]="isFieldInvalid('address.street')">
        <div class="error-message" *ngIf="isFieldInvalid('address.street')">
          {{ getFieldError('address.street') }}
        </div>
      </div>
      
      <div class="form-row">
        <div class="form-group">
          <label for="city">City *</label>
          <input 
            type="text" 
            id="city"
            formControlName="city"
            [class.is-invalid]="isFieldInvalid('address.city')">
          <div class="error-message" *ngIf="isFieldInvalid('address.city')">
            {{ getFieldError('address.city') }}
          </div>
        </div>
        
        <div class="form-group">
          <label for="state">State *</label>
          <input 
            type="text" 
            id="state"
            formControlName="state"
            maxlength="2"
            [class.is-invalid]="isFieldInvalid('address.state')">
          <div class="error-message" *ngIf="isFieldInvalid('address.state')">
            {{ getFieldError('address.state') }}
          </div>
        </div>
      </div>
      
      <div class="form-row">
        <div class="form-group">
          <label for="zipCode">Zip Code *</label>
          <input 
            type="text" 
            id="zipCode"
            formControlName="zipCode"
            [class.is-invalid]="isFieldInvalid('address.zipCode')">
          <div class="error-message" *ngIf="isFieldInvalid('address.zipCode')">
            {{ getFieldError('address.zipCode') }}
          </div>
        </div>
        
        <div class="form-group">
          <label for="country">Country *</label>
          <input 
            type="text" 
            id="country"
            formControlName="country"
            [class.is-invalid]="isFieldInvalid('address.country')">
          <div class="error-message" *ngIf="isFieldInvalid('address.country')">
            {{ getFieldError('address.country') }}
          </div>
        </div>
      </div>
    </div>
    
    <!-- Form Actions -->
    <div class="form-actions">
      <button type="submit" class="btn btn-primary">
        {{ mode === 'create' ? 'Create Employee' : 'Update Employee' }}
      </button>
      <button type="button" class="btn btn-secondary" (click)="onReset()">
        Reset
      </button>
      <button type="button" class="btn btn-outline" (click)="onCancel()">
        Cancel
      </button>
    </div>
  </form>
</div>
```

## Key Concepts Demonstrated

### TypeScript Concepts
1. **Interfaces**: Strong typing for data models
2. **Enums**: Type-safe constants for departments
3. **Generics**: `PaginatedResponse<T>` for reusable types
4. **Type Guards**: Input validation and type checking
5. **Async/Await & Promises**: HTTP operations
6. **Optional Properties**: `phoneNumber?: string`
7. **Union Types**: `'create' | 'edit'` for mode
8. **Type Assertions**: Type casting for form controls

### Angular Concepts
1. **Component Architecture**: Feature-based organization
2. **Services**: Business logic separation
3. **Dependency Injection**: Service injection
4. **RxJS**: Observables for async operations
5. **Forms**: Reactive forms with validation
6. **HTTP Client**: API communication
7. **Lifecycle Hooks**: `OnInit`, `OnDestroy`
8. **Data Binding**: Property, event, two-way binding
9. **Directives**: `*ngIf`, `*ngFor`, `ngClass`
10. **Pipes**: Data transformation in templates
11. **Custom Validators**: Form validation logic
12. **State Management**: BehaviorSubject for shared state

### Real-World Patterns
1. **Repository Pattern**: Data access abstraction
2. **Service Layer**: Business logic separation
3. **Error Handling**: Centralized error management
4. **Loading States**: User feedback during operations
5. **Form Validation**: Comprehensive input validation
6. **Pagination**: Large dataset handling
7. **Search & Filter**: Data querying
8. **CRUD Operations**: Complete data lifecycle
9. **Responsive Design**: Mobile-friendly UI
10. **Code Organization**: Modular structure

## Next Steps

- Add unit tests with Jasmine/Karma
- Implement integration tests
- Add authentication and authorization
- Implement state management with NgRx
- Add lazy loading for modules
- Optimize performance with OnPush strategy
- Add internationalization (i18n)
- Implement PWA features
- Add error logging and monitoring
- Deploy to production
