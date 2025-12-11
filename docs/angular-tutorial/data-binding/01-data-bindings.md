# Data Binding in Angular

## What is Data Binding?

Data binding is the synchronization between the component (TypeScript) and the template (HTML). Angular provides four types of data binding.

## Types of Data Binding

```
Component ────────> Template    Interpolation {{ }}
Component ────────> Template    Property Binding []
Component <──────── Template    Event Binding ()
Component <──────> Template     Two-Way Binding [()]
```

## 1. Interpolation {{ }}

Display component data in the template using double curly braces.

### Basic Interpolation

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-interpolation',
  standalone: true,
  template: `
    <h1>{{ title }}</h1>
    <p>Welcome, {{ userName }}!</p>
    <p>Age: {{ age }}</p>
    <p>Is Active: {{ isActive }}</p>
    
    <!-- Expressions -->
    <p>Next year: {{ age + 1 }}</p>
    <p>{{ 2 + 2 }}</p>
    <p>{{ getMessage() }}</p>
    
    <!-- Template expressions -->
    <p>{{ isActive ? 'Active' : 'Inactive' }}</p>
    <p>{{ userName.toUpperCase() }}</p>
  `
})
export class InterpolationComponent {
  title = 'Data Binding Demo';
  userName = 'John Doe';
  age = 25;
  isActive = true;
  
  getMessage(): string {
    return `Hello, ${this.userName}!`;
  }
}
```

### Interpolation with Objects

```typescript
@Component({
  selector: 'app-user-profile',
  standalone: true,
  template: `
    <div class="profile">
      <h2>{{ user.firstName }} {{ user.lastName }}</h2>
      <p>Email: {{ user.email }}</p>
      <p>Age: {{ user.age }}</p>
      <p>Address: {{ user.address.city }}, {{ user.address.country }}</p>
    </div>
  `
})
export class UserProfileComponent {
  user = {
    firstName: 'John',
    lastName: 'Doe',
    email: 'john@example.com',
    age: 30,
    address: {
      street: '123 Main St',
      city: 'New York',
      country: 'USA'
    }
  };
}
```

## 2. Property Binding []

Bind component properties to element properties, attributes, classes, or styles.

### Element Property Binding

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-property-binding',
  standalone: true,
  template: `
    <!-- Bind to element properties -->
    <img [src]="imageUrl" [alt]="imageAlt">
    <input [value]="username" [placeholder]="placeholderText">
    <button [disabled]="isDisabled">Click Me</button>
    
    <!-- Bind to attributes -->
    <div [attr.data-id]="userId"></div>
    <button [attr.aria-label]="ariaLabel">Help</button>
    
    <!-- Boolean properties -->
    <input type="checkbox" [checked]="isChecked">
    <details [open]="isOpen">
      <summary>Click to expand</summary>
      <p>Hidden content</p>
    </details>
  `,
  styles: [`
    img {
      max-width: 300px;
      border-radius: 8px;
    }
    
    button:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  `]
})
export class PropertyBindingComponent {
  imageUrl = 'https://via.placeholder.com/300';
  imageAlt = 'Placeholder Image';
  username = 'JohnDoe';
  placeholderText = 'Enter username';
  isDisabled = false;
  userId = 123;
  ariaLabel = 'Help button';
  isChecked = true;
  isOpen = false;
}
```

### Class Binding

```typescript
@Component({
  selector: 'app-class-binding',
  standalone: true,
  template: `
    <!-- Single class binding -->
    <div [class.active]="isActive">Status Box</div>
    <div [class.highlighted]="isHighlighted">Highlight Me</div>
    
    <!-- Multiple classes -->
    <div [class]="dynamicClasses">Dynamic Classes</div>
    
    <!-- Class object -->
    <div [ngClass]="{
      'active': isActive,
      'error': hasError,
      'warning': hasWarning
    }">Status Message</div>
  `,
  styles: [`
    .active {
      background: #28a745;
      color: white;
      padding: 10px;
    }
    
    .highlighted {
      background: yellow;
      padding: 10px;
    }
    
    .error {
      color: red;
      border: 2px solid red;
      padding: 10px;
    }
    
    .warning {
      color: orange;
      border: 2px solid orange;
      padding: 10px;
    }
  `]
})
export class ClassBindingComponent {
  isActive = true;
  isHighlighted = false;
  dynamicClasses = 'active highlighted';
  hasError = false;
  hasWarning = true;
}
```

### Style Binding

```typescript
@Component({
  selector: 'app-style-binding',
  standalone: true,
  template: `
    <!-- Single style binding -->
    <div [style.color]="textColor">Colored Text</div>
    <div [style.font-size.px]="fontSize">Font Size</div>
    <div [style.background-color]="bgColor">Background</div>
    
    <!-- Style object -->
    <div [style]="dynamicStyles">Dynamic Styles</div>
    
    <!-- ngStyle directive -->
    <div [ngStyle]="{
      'color': textColor,
      'font-size': fontSize + 'px',
      'font-weight': isBold ? 'bold' : 'normal',
      'text-decoration': isUnderlined ? 'underline' : 'none'
    }">Styled Text</div>
  `
})
export class StyleBindingComponent {
  textColor = 'blue';
  fontSize = 20;
  bgColor = '#f0f0f0';
  dynamicStyles = {
    'color': 'green',
    'font-size': '24px',
    'padding': '10px'
  };
  isBold = true;
  isUnderlined = false;
}
```

## 3. Event Binding ()

Respond to user events like clicks, key presses, mouse movements, etc.

### Basic Event Binding

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-event-binding',
  standalone: true,
  template: `
    <div class="container">
      <h2>Event Binding Examples</h2>
      
      <!-- Click event -->
      <button (click)="handleClick()">Click Me</button>
      <p>Click count: {{ clickCount }}</p>
      
      <!-- Input event -->
      <input (input)="handleInput($event)" placeholder="Type something">
      <p>You typed: {{ inputValue }}</p>
      
      <!-- Change event -->
      <select (change)="handleChange($event)">
        <option value="">Select an option</option>
        <option value="option1">Option 1</option>
        <option value="option2">Option 2</option>
        <option value="option3">Option 3</option>
      </select>
      <p>Selected: {{ selectedOption }}</p>
      
      <!-- Mouse events -->
      <div 
        class="hover-box"
        (mouseenter)="onMouseEnter()"
        (mouseleave)="onMouseLeave()">
        {{ isHovered ? 'Mouse is over!' : 'Hover over me!' }}
      </div>
      
      <!-- Keyboard events -->
      <input 
        (keyup)="onKeyUp($event)"
        (keyup.enter)="onEnterPressed()"
        (keyup.escape)="onEscapePressed()"
        placeholder="Press Enter or Escape">
      <p>Last key: {{ lastKey }}</p>
      
      <!-- Focus events -->
      <input 
        (focus)="onFocus()"
        (blur)="onBlur()"
        placeholder="Focus on me">
      <p>{{ isFocused ? 'Input is focused' : 'Input is not focused' }}</p>
    </div>
  `,
  styles: [`
    .container {
      padding: 20px;
    }
    
    button {
      margin: 10px 0;
      padding: 10px 20px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    input, select {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    
    .hover-box {
      padding: 20px;
      background: #f0f0f0;
      border: 2px solid #ddd;
      border-radius: 4px;
      text-align: center;
      cursor: pointer;
      transition: all 0.3s;
    }
    
    .hover-box:hover {
      background: #007bff;
      color: white;
    }
  `]
})
export class EventBindingComponent {
  clickCount = 0;
  inputValue = '';
  selectedOption = '';
  isHovered = false;
  lastKey = '';
  isFocused = false;
  
  handleClick(): void {
    this.clickCount++;
  }
  
  handleInput(event: Event): void {
    const target = event.target as HTMLInputElement;
    this.inputValue = target.value;
  }
  
  handleChange(event: Event): void {
    const target = event.target as HTMLSelectElement;
    this.selectedOption = target.value;
  }
  
  onMouseEnter(): void {
    this.isHovered = true;
  }
  
  onMouseLeave(): void {
    this.isHovered = false;
  }
  
  onKeyUp(event: KeyboardEvent): void {
    this.lastKey = event.key;
  }
  
  onEnterPressed(): void {
    console.log('Enter key pressed!');
  }
  
  onEscapePressed(): void {
    console.log('Escape key pressed!');
  }
  
  onFocus(): void {
    this.isFocused = true;
  }
  
  onBlur(): void {
    this.isFocused = false;
  }
}
```

### Event Object and $event

```typescript
@Component({
  selector: 'app-event-details',
  standalone: true,
  template: `
    <div>
      <!-- Mouse position -->
      <div 
        class="track-area"
        (mousemove)="trackMouse($event)">
        Mouse Position: X: {{ mouseX }}, Y: {{ mouseY }}
      </div>
      
      <!-- Key information -->
      <input 
        (keydown)="onKeyDown($event)"
        placeholder="Press any key">
      <p *ngIf="keyInfo">
        Key: {{ keyInfo.key }}, 
        Code: {{ keyInfo.code }}, 
        Ctrl: {{ keyInfo.ctrlKey }}, 
        Shift: {{ keyInfo.shiftKey }}
      </p>
    </div>
  `,
  styles: [`
    .track-area {
      height: 200px;
      background: #f0f0f0;
      border: 2px solid #ddd;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
    }
  `]
})
export class EventDetailsComponent {
  mouseX = 0;
  mouseY = 0;
  keyInfo: any = null;
  
  trackMouse(event: MouseEvent): void {
    this.mouseX = event.clientX;
    this.mouseY = event.clientY;
  }
  
  onKeyDown(event: KeyboardEvent): void {
    this.keyInfo = {
      key: event.key,
      code: event.code,
      ctrlKey: event.ctrlKey,
      shiftKey: event.shiftKey,
      altKey: event.altKey
    };
  }
}
```

## 4. Two-Way Binding [(ngModel)]

Synchronize data between component and template in both directions.

### Setup FormsModule

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-two-way-binding',
  standalone: true,
  imports: [FormsModule],  // ← Required for ngModel
  template: `
    <div class="container">
      <h2>Two-Way Binding</h2>
      
      <!-- Text input -->
      <label>Name:</label>
      <input [(ngModel)]="name" placeholder="Enter name">
      <p>Hello, {{ name }}!</p>
      
      <!-- Textarea -->
      <label>Description:</label>
      <textarea [(ngModel)]="description" rows="4"></textarea>
      <p>Character count: {{ description.length }}</p>
      
      <!-- Checkbox -->
      <label>
        <input type="checkbox" [(ngModel)]="isSubscribed">
        Subscribe to newsletter
      </label>
      <p>Subscribed: {{ isSubscribed }}</p>
      
      <!-- Radio buttons -->
      <div>
        <label>
          <input type="radio" [(ngModel)]="gender" value="male">
          Male
        </label>
        <label>
          <input type="radio" [(ngModel)]="gender" value="female">
          Female
        </label>
        <label>
          <input type="radio" [(ngModel)]="gender" value="other">
          Other
        </label>
      </div>
      <p>Gender: {{ gender }}</p>
      
      <!-- Select dropdown -->
      <label>Country:</label>
      <select [(ngModel)]="country">
        <option value="">Select country</option>
        <option value="usa">USA</option>
        <option value="uk">UK</option>
        <option value="canada">Canada</option>
        <option value="australia">Australia</option>
      </select>
      <p>Selected: {{ country }}</p>
      
      <!-- Number input -->
      <label>Age:</label>
      <input type="number" [(ngModel)]="age" min="0" max="150">
      <p>You are {{ age }} years old</p>
      
      <!-- Range slider -->
      <label>Volume: {{ volume }}%</label>
      <input type="range" [(ngModel)]="volume" min="0" max="100">
      
      <!-- Color picker -->
      <label>Favorite Color:</label>
      <input type="color" [(ngModel)]="favoriteColor">
      <div [style.background-color]="favoriteColor" 
           style="width: 100px; height: 100px; border: 1px solid #ddd;">
      </div>
    </div>
  `,
  styles: [`
    .container {
      max-width: 600px;
      margin: 20px auto;
      padding: 20px;
    }
    
    label {
      display: block;
      margin: 15px 0 5px;
      font-weight: bold;
    }
    
    input[type="text"],
    input[type="number"],
    input[type="color"],
    input[type="range"],
    textarea,
    select {
      width: 100%;
      padding: 8px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    
    input[type="checkbox"],
    input[type="radio"] {
      width: auto;
      margin-right: 5px;
    }
  `]
})
export class TwoWayBindingComponent {
  name = '';
  description = '';
  isSubscribed = false;
  gender = '';
  country = '';
  age = 25;
  volume = 50;
  favoriteColor = '#3498db';
}
```

## Complete Example: User Registration Form

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

interface User {
  username: string;
  email: string;
  password: string;
  confirmPassword: string;
  age: number;
  country: string;
  gender: string;
  agreeToTerms: boolean;
}

@Component({
  selector: 'app-registration',
  standalone: true,
  imports: [CommonModule, FormsModule],
  template: `
    <div class="registration-form">
      <h2>User Registration</h2>
      
      <form (submit)="onSubmit()">
        <!-- Username -->
        <div class="form-group">
          <label>Username:</label>
          <input 
            [(ngModel)]="user.username"
            name="username"
            required
            minlength="3"
            placeholder="Enter username">
          <small *ngIf="user.username.length > 0 && user.username.length < 3">
            Username must be at least 3 characters
          </small>
        </div>
        
        <!-- Email -->
        <div class="form-group">
          <label>Email:</label>
          <input 
            type="email"
            [(ngModel)]="user.email"
            name="email"
            required
            placeholder="Enter email">
        </div>
        
        <!-- Password -->
        <div class="form-group">
          <label>Password:</label>
          <input 
            type="password"
            [(ngModel)]="user.password"
            name="password"
            required
            minlength="6"
            placeholder="Enter password">
          <small *ngIf="user.password.length > 0 && user.password.length < 6">
            Password must be at least 6 characters
          </small>
        </div>
        
        <!-- Confirm Password -->
        <div class="form-group">
          <label>Confirm Password:</label>
          <input 
            type="password"
            [(ngModel)]="user.confirmPassword"
            name="confirmPassword"
            required
            placeholder="Confirm password">
          <small 
            *ngIf="user.confirmPassword && user.password !== user.confirmPassword"
            class="error">
            Passwords do not match
          </small>
        </div>
        
        <!-- Age -->
        <div class="form-group">
          <label>Age: {{ user.age }}</label>
          <input 
            type="range"
            [(ngModel)]="user.age"
            name="age"
            min="18"
            max="100">
        </div>
        
        <!-- Country -->
        <div class="form-group">
          <label>Country:</label>
          <select [(ngModel)]="user.country" name="country" required>
            <option value="">Select country</option>
            <option value="usa">United States</option>
            <option value="uk">United Kingdom</option>
            <option value="canada">Canada</option>
            <option value="australia">Australia</option>
            <option value="india">India</option>
          </select>
        </div>
        
        <!-- Gender -->
        <div class="form-group">
          <label>Gender:</label>
          <div class="radio-group">
            <label>
              <input 
                type="radio" 
                [(ngModel)]="user.gender"
                name="gender"
                value="male">
              Male
            </label>
            <label>
              <input 
                type="radio"
                [(ngModel)]="user.gender"
                name="gender"
                value="female">
              Female
            </label>
            <label>
              <input 
                type="radio"
                [(ngModel)]="user.gender"
                name="gender"
                value="other">
              Other
            </label>
          </div>
        </div>
        
        <!-- Terms and Conditions -->
        <div class="form-group">
          <label>
            <input 
              type="checkbox"
              [(ngModel)]="user.agreeToTerms"
              name="agreeToTerms">
            I agree to the terms and conditions
          </label>
        </div>
        
        <!-- Submit Button -->
        <button 
          type="submit"
          [disabled]="!isFormValid()"
          [class.disabled]="!isFormValid()">
          Register
        </button>
      </form>
      
      <!-- Form Data Preview -->
      <div class="preview" *ngIf="showPreview">
        <h3>Form Data:</h3>
        <pre>{{ user | json }}</pre>
      </div>
    </div>
  `,
  styles: [`
    .registration-form {
      max-width: 500px;
      margin: 20px auto;
      padding: 30px;
      border: 1px solid #ddd;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    
    .form-group {
      margin-bottom: 20px;
    }
    
    label {
      display: block;
      margin-bottom: 5px;
      font-weight: bold;
      color: #333;
    }
    
    input[type="text"],
    input[type="email"],
    input[type="password"],
    input[type="range"],
    select {
      width: 100%;
      padding: 10px;
      border: 1px solid #ddd;
      border-radius: 4px;
      font-size: 14px;
    }
    
    input[type="checkbox"],
    input[type="radio"] {
      width: auto;
      margin-right: 5px;
    }
    
    .radio-group {
      display: flex;
      gap: 15px;
    }
    
    .radio-group label {
      font-weight: normal;
    }
    
    small {
      display: block;
      margin-top: 5px;
      color: #666;
      font-size: 12px;
    }
    
    small.error {
      color: #dc3545;
    }
    
    button {
      width: 100%;
      padding: 12px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      font-size: 16px;
      cursor: pointer;
      transition: background 0.3s;
    }
    
    button:hover:not(:disabled) {
      background: #0056b3;
    }
    
    button:disabled {
      background: #ccc;
      cursor: not-allowed;
    }
    
    .preview {
      margin-top: 30px;
      padding: 20px;
      background: #f8f9fa;
      border-radius: 4px;
    }
    
    pre {
      background: white;
      padding: 15px;
      border-radius: 4px;
      overflow-x: auto;
    }
  `]
})
export class RegistrationComponent {
  user: User = {
    username: '',
    email: '',
    password: '',
    confirmPassword: '',
    age: 25,
    country: '',
    gender: '',
    agreeToTerms: false
  };
  
  showPreview = false;
  
  isFormValid(): boolean {
    return (
      this.user.username.length >= 3 &&
      this.user.email.length > 0 &&
      this.user.password.length >= 6 &&
      this.user.password === this.user.confirmPassword &&
      this.user.country.length > 0 &&
      this.user.gender.length > 0 &&
      this.user.agreeToTerms
    );
  }
  
  onSubmit(): void {
    if (this.isFormValid()) {
      console.log('Form submitted:', this.user);
      this.showPreview = true;
      alert('Registration successful!');
    }
  }
}
```

## Summary

| Type | Syntax | Direction | Use Case |
|------|--------|-----------|----------|
| Interpolation | `{{ }}` | Component → Template | Display data |
| Property Binding | `[property]` | Component → Template | Bind to properties |
| Event Binding | `(event)` | Template → Component | Handle events |
| Two-Way Binding | `[(ngModel)]` | Component ↔ Template | Form inputs |

## Best Practices

1. **Use interpolation** for simple text display
2. **Use property binding** for element properties
3. **Use event binding** with strong typing
4. **Use two-way binding** for form inputs
5. **Avoid complex expressions** in templates
6. **Use methods** for complex logic
7. **Be careful with** `$event` parameter typing

## Next Steps

- Learn about directives (*ngIf, *ngFor, *ngSwitch)
- Understand Angular's new control flow (@if, @for)
- Master template-driven and reactive forms
- Explore custom directives
