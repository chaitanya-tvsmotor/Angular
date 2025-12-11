# Complete Angular Tutorial - Topic Index

> Based on the YouTube tutorial covering all essential Angular concepts from beginner to advanced level.

## 📺 Tutorial Overview

This documentation covers all 28 topics from the complete Angular tutorial, providing:
- **Interactive code examples** for every concept
- **Real-world applications** demonstrating practical usage
- **Best practices** and common pitfalls
- **Progressive learning path** from basics to advanced

---

## ✅ Completed Topics

### 1. Project Configuration & Directory Structure `01:47`
**Status:** ✅ Complete  
**File:** [01-configuration-and-structure.md](project-setup/01-configuration-and-structure.md)

**What you'll learn:**
- Setting up Angular CLI
- Understanding project structure
- Configuration files (angular.json, tsconfig.json, package.json)
- Environment setup and best practices
- CLI commands reference

**Key Examples:**
- Creating new Angular projects
- Project structure walkthrough
- Configuration customization

---

### 2. Standalone Components `14:04`
**Status:** ✅ Complete  
**File:** [01-standalone-components.md](components/01-standalone-components.md)

**What you'll learn:**
- Traditional vs Standalone components
- Creating standalone components
- Importing dependencies directly
- Bootstrapping applications
- Component composition

**Key Examples:**
- Todo Application
- Product Catalog with multiple components
- Service integration with standalone components

---

### 3. Data Bindings `26:50`
**Status:** ✅ Complete  
**File:** [01-data-bindings.md](data-binding/01-data-bindings.md)

**What you'll learn:**
- Interpolation `{{ }}`
- Property Binding `[property]`
- Event Binding `(event)`
- Two-Way Binding `[(ngModel)]`
- Class and Style binding

**Key Examples:**
- User Registration Form
- Dynamic styling and classes
- Event handling patterns
- Form controls with two-way binding

---

### 4. Structural Directives `46:00`
**Status:** ✅ Complete  
**File:** [01-structural-directives.md](directives/01-structural-directives.md)

**What you'll learn:**
- `*ngIf` vs `@if()` - Conditional rendering
- `*ngFor` vs `@for()` - Iteration with trackBy
- `*ngSwitch` vs `@switch()` - Multiple conditions
- New control flow syntax (Angular 17+)
- Performance optimization

**Key Examples:**
- Conditional content display
- List rendering with pagination
- Switch-case implementations
- Empty state handling

---

### 5. Bootstrap Installation `53:40`
**Status:** 🚧 Planned  

**What you'll learn:**
- Installing Bootstrap in Angular
- Bootstrap component integration
- Responsive design patterns
- Custom Bootstrap theming

---

### 6. *ngIf vs @if() `58:05`
**Status:** ✅ Covered in Structural Directives

See [01-structural-directives.md](directives/01-structural-directives.md)

---

### 7. *ngFor vs @for() `1:18:00`
**Status:** ✅ Covered in Structural Directives

See [01-structural-directives.md](directives/01-structural-directives.md)

---

### 8. *ngSwitch vs @switch() `1:40:50`
**Status:** ✅ Covered in Structural Directives

See [01-structural-directives.md](directives/01-structural-directives.md)

---

### 9. [ngClass] - Attribute Directive `1:53:14`
**Status:** ✅ Complete  
**File:** [02-attribute-directives.md](directives/02-attribute-directives.md)

**What you'll learn:**
- Dynamic class binding with ngClass
- Object, array, and string syntax
- Conditional classes
- Real-world UI components

**Key Examples:**
- Card components with dynamic classes
- Button states
- Alert components
- Badge system

---

### 10. [ngStyle] - Attribute Directive `2:07:07`
**Status:** ✅ Complete  
**File:** [02-attribute-directives.md](directives/02-attribute-directives.md)

**What you'll learn:**
- Dynamic style binding with ngStyle
- Inline style manipulation
- Conditional styling
- Animation with styles

**Key Examples:**
- Progress bars
- Draggable elements
- Dynamic theming
- Visual feedback systems

---

### 11. Angular Signals `2:17:50`
**Status:** ✅ Complete  
**File:** [01-angular-signals.md](signals/01-angular-signals.md)

**What you'll learn:**
- Creating signals with `signal()`
- Computed values with `computed()`
- Side effects with `effect()`
- Signal methods: set(), update(), mutate()
- Signals vs RxJS

**Key Examples:**
- Shopping Cart with computed totals
- Todo App with Signals
- Temperature Converter
- User Profile with derived data

---

### 12. linkedSignal() `2:36:14`
**Status:** ✅ Complete  
**File:** [01-angular-signals.md](signals/01-angular-signals.md)

**What you'll learn:**
- Linking signals together
- Derived state management
- Automatic synchronization
- Complex state relationships

**Key Examples:**
- Temperature unit converter
- Form field synchronization
- Username generation from profile

---

### 13. Routing - router-outlet, routerLink, routerLinkActive `2:45:19`
**Status:** 🚧 Planned

**What you'll learn:**
- Setting up routing
- router-outlet for view rendering
- routerLink for navigation
- routerLinkActive for active routes
- Route configuration

---

### 14. Navigation - navigate() & navigateByUrl() `3:07:25`
**Status:** 🚧 Planned

**What you'll learn:**
- Programmatic navigation
- navigate() method
- navigateByUrl() method
- Passing data between routes
- Query parameters

---

### 15. Lifecycle Hooks `3:16:42`
**Status:** 🚧 Planned

**What you'll learn:**
- ngOnInit
- ngOnChanges
- ngOnDestroy
- ngAfterViewInit
- ngAfterContentInit
- Use cases for each hook

---

### 16. Built-in Pipes `3:31:46`
**Status:** 🚧 Planned

**What you'll learn:**
- DatePipe, CurrencyPipe, DecimalPipe
- UpperCasePipe, LowerCasePipe, TitleCasePipe
- JsonPipe, SlicePipe, PercentPipe
- AsyncPipe for Observables
- Chaining pipes

---

### 17. Custom Pipes `3:58:23`
**Status:** 🚧 Planned

**What you'll learn:**
- Creating custom pipes
- Parameterized pipes
- Pipe transforms
- Reusable data transformation

---

### 18. Pure & Impure Pipes `4:11:07`
**Status:** 🚧 Planned

**What you'll learn:**
- Pure vs Impure pipes
- Performance implications
- When to use each type
- Change detection with pipes

---

### 19. Template Driven Forms (TDF) `4:17:59`
**Status:** ✅ Complete  
**File:** [01-template-driven-forms.md](forms/01-template-driven-forms.md)

**What you'll learn:**
- FormsModule and ngModel
- Two-way data binding
- Form controls (input, select, radio, checkbox)
- Template reference variables
- Form submission

**Key Examples:**
- User Registration Form
- Complete Profile Form
- All form control types
- Form state management

---

### 20. TDF Validation `4:50:39`
**Status:** ✅ Complete  
**File:** [01-template-driven-forms.md](forms/01-template-driven-forms.md)

**What you'll learn:**
- Built-in validators
- required, minlength, maxlength, pattern
- email, min, max validators
- Validation states (valid, invalid, touched, dirty)
- Custom error messages

**Key Examples:**
- Field-level validation
- Form-level validation
- Real-time error display
- Conditional validation

---

### 21. Reactive Forms `5:16:01`
**Status:** 🚧 Planned

**What you'll learn:**
- ReactiveFormsModule
- FormControl, FormGroup, FormArray
- FormBuilder service
- Dynamic forms
- Programmatic control

---

### 22. Reactive Form Validation `5:39:56`
**Status:** 🚧 Planned

**What you'll learn:**
- Validator functions
- Built-in validators
- Custom validators
- Async validators
- Cross-field validation

---

### 23. Services and Dependency Injection `6:02:25`
**Status:** 🚧 Planned

**What you'll learn:**
- Creating services
- @Injectable decorator
- providedIn: 'root'
- Dependency injection patterns
- Service hierarchies

---

### 24. CRUD Operations With JSON Server `6:29:26`
**Status:** 🚧 Planned

**What you'll learn:**
- HTTP Client setup
- GET, POST, PUT, DELETE operations
- JSON Server setup
- Error handling
- HTTP Interceptors

---

### 25. Resource API - resource() & rxResource() `7:27:37`
**Status:** 🚧 Planned

**What you'll learn:**
- Modern data fetching
- resource() function
- rxResource() with RxJS
- Loading states
- Error handling

---

### 26. Reusable Components `7:43:29`
**Status:** 🚧 Planned

**What you'll learn:**
- Component design patterns
- @Input and @Output
- Content projection
- Component libraries

---

### 27. Decorators `7:59:57`
**Status:** 🚧 Planned

**What you'll learn:**
- @Component, @Directive, @Pipe
- @Injectable, @Input, @Output
- @ViewChild, @ContentChild
- Custom decorators

---

### 28. Component Communication `8:05:57`
**Status:** 🚧 Planned

**What you'll learn:**
- @Input() - Parent to Child
- @Output() - Child to Parent
- @ViewChild() - Parent accessing Child
- Services for communication
- Route parameters

---

### 29. Session Storage `8:50:03`
**Status:** 🚧 Planned

**What you'll learn:**
- Session storage API
- Storing temporary data
- Session-specific state
- Security considerations

---

### 30. Local Storage `9:00:45`
**Status:** 🚧 Planned

**What you'll learn:**
- Local storage API
- Persistent data storage
- User preferences
- Token storage

---

### 31. Cookies `9:07:26`
**Status:** 🚧 Planned

**What you'll learn:**
- Cookie management in Angular
- HTTP-only cookies
- Secure cookie practices
- Cookie service

---

## 📊 Progress Summary

| Status | Count | Percentage |
|--------|-------|------------|
| ✅ Complete | 8 topics | 26% |
| 🚧 Planned | 23 topics | 74% |
| **Total** | **31 topics** | **100%** |

## 🎯 Learning Paths

### Path 1: Complete Beginner
1. Project Setup
2. Components
3. Data Binding
4. Directives
5. Forms (Template-Driven)
6. Basic Routing

### Path 2: Intermediate Developer
1. Signals
2. Reactive Forms
3. Services & DI
4. HTTP & CRUD
5. Component Communication
6. Lifecycle Hooks

### Path 3: Advanced Developer
1. Custom Directives & Pipes
2. Advanced Routing
3. State Management with Signals
4. Resource API
5. Performance Optimization
6. Production Deployment

## 📚 Additional Resources

- [Angular Official Documentation](https://angular.dev)
- [Angular CLI Reference](https://angular.io/cli)
- [RxJS Documentation](https://rxjs.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

## 🤝 Contributing

This documentation is continuously being updated. Check back for new topics and improvements!

---

**Legend:**
- ✅ Complete - Documentation available with examples
- 🚧 Planned - Coming soon
- 📝 In Progress - Currently being written
