# Template Driven Forms (TDF) in Angular

## What are Template Driven Forms?

Template Driven Forms use directives in the template to create and manage forms. They're simpler and good for basic forms with straightforward validation.

## Setup

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';  // ← Required
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-form',
  standalone: true,
  imports: [FormsModule, CommonModule],  // ← Import FormsModule
  // ...
})
export class FormComponent {}
```

## Basic Form with ngModel

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-basic-form',
  standalone: true,
  imports: [FormsModule],
  template: `
    <form #myForm="ngForm" (ngSubmit)="onSubmit(myForm)">
      <h2>User Registration</h2>
      
      <!-- Text Input -->
      <div>
        <label>Username:</label>
        <input 
          type="text"
          name="username"
          [(ngModel)]="user.username"
          required
          minlength="3"
          #username="ngModel">
        <div *ngIf="username.invalid && username.touched">
          <small *ngIf="username.errors?.['required']">Username is required</small>
          <small *ngIf="username.errors?.['minlength']">Min 3 characters</small>
        </div>
      </div>
      
      <!-- Email Input -->
      <div>
        <label>Email:</label>
        <input 
          type="email"
          name="email"
          [(ngModel)]="user.email"
          required
          email
          #email="ngModel">
        <div *ngIf="email.invalid && email.touched">
          <small *ngIf="email.errors?.['required']">Email is required</small>
          <small *ngIf="email.errors?.['email']">Invalid email format</small>
        </div>
      </div>
      
      <!-- Password -->
      <div>
        <label>Password:</label>
        <input 
          type="password"
          name="password"
          [(ngModel)]="user.password"
          required
          minlength="6"
          #password="ngModel">
        <div *ngIf="password.invalid && password.touched">
          <small *ngIf="password.errors?.['required']">Password is required</small>
          <small *ngIf="password.errors?.['minlength']">Min 6 characters</small>
        </div>
      </div>
      
      <!-- Submit Button -->
      <button type="submit" [disabled]="myForm.invalid">Submit</button>
      
      <!-- Form Status -->
      <div class="debug">
        <h4>Form Status</h4>
        <p>Valid: {{ myForm.valid }}</p>
        <p>Touched: {{ myForm.touched }}</p>
        <p>Submitted: {{ myForm.submitted }}</p>
      </div>
    </form>
  `,
  styles: [`
    form {
      max-width: 500px;
      margin: 20px auto;
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 8px;
    }
    
    div {
      margin: 15px 0;
    }
    
    label {
      display: block;
      margin-bottom: 5px;
      font-weight: bold;
    }
    
    input {
      width: 100%;
      padding: 10px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    
    input.ng-invalid.ng-touched {
      border-color: #dc3545;
    }
    
    input.ng-valid.ng-touched {
      border-color: #28a745;
    }
    
    small {
      display: block;
      color: #dc3545;
      margin-top: 5px;
    }
    
    button {
      width: 100%;
      padding: 12px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-size: 16px;
    }
    
    button:disabled {
      background: #ccc;
      cursor: not-allowed;
    }
    
    .debug {
      margin-top: 20px;
      padding: 15px;
      background: #f8f9fa;
      border-radius: 4px;
    }
  `]
})
export class BasicFormComponent {
  user = {
    username: '',
    email: '',
    password: ''
  };
  
  onSubmit(form: any): void {
    if (form.valid) {
      console.log('Form submitted:', this.user);
      alert('Registration successful!');
      form.reset();
    }
  }
}
```

## Built-in Validators

### Available Validators

| Validator | Directive | Description |
|-----------|-----------|-------------|
| Required | `required` | Field must have a value |
| Min Length | `minlength="n"` | Minimum string length |
| Max Length | `maxlength="n"` | Maximum string length |
| Pattern | `pattern="regex"` | Must match regex |
| Email | `email` | Valid email format |
| Min | `min="n"` | Minimum number |
| Max | `max="n"` | Maximum number |

### Validation States

```typescript
@Component({
  selector: 'app-validation-demo',
  standalone: true,
  imports: [FormsModule, CommonModule],
  template: `
    <form #form="ngForm">
      <h2>Validation States Demo</h2>
      
      <div>
        <input 
          type="text"
          name="username"
          [(ngModel)]="username"
          required
          minlength="3"
          maxlength="20"
          pattern="[a-zA-Z0-9]+"
          #usernameField="ngModel">
        
        <!-- Validation messages -->
        <div *ngIf="usernameField.invalid && usernameField.touched">
          <small *ngIf="usernameField.errors?.['required']">
            Username is required
          </small>
          <small *ngIf="usernameField.errors?.['minlength']">
            Minimum 3 characters required
          </small>
          <small *ngIf="usernameField.errors?.['maxlength']">
            Maximum 20 characters allowed
          </small>
          <small *ngIf="usernameField.errors?.['pattern']">
            Only alphanumeric characters allowed
          </small>
        </div>
      </div>
      
      <!-- Validation State Classes -->
      <div class="state-info">
        <h4>Field States:</h4>
        <p>Valid: {{ usernameField.valid }}</p>
        <p>Invalid: {{ usernameField.invalid }}</p>
        <p>Touched: {{ usernameField.touched }}</p>
        <p>Untouched: {{ usernameField.untouched }}</p>
        <p>Dirty: {{ usernameField.dirty }}</p>
        <p>Pristine: {{ usernameField.pristine }}</p>
        
        <h4>CSS Classes:</h4>
        <ul>
          <li>ng-valid: {{ usernameField.valid }}</li>
          <li>ng-invalid: {{ usernameField.invalid }}</li>
          <li>ng-touched: {{ usernameField.touched }}</li>
          <li>ng-untouched: {{ usernameField.untouched }}</li>
          <li>ng-dirty: {{ usernameField.dirty }}</li>
          <li>ng-pristine: {{ usernameField.pristine }}</li>
        </ul>
      </div>
    </form>
  `
})
export class ValidationDemoComponent {
  username = '';
}
```

## Form Controls

### Select Dropdown

```typescript
<select name="country" [(ngModel)]="user.country" required #country="ngModel">
  <option value="">Select Country</option>
  <option value="usa">United States</option>
  <option value="uk">United Kingdom</option>
  <option value="canada">Canada</option>
</select>
```

### Radio Buttons

```typescript
<div>
  <label>
    <input type="radio" name="gender" value="male" [(ngModel)]="user.gender">
    Male
  </label>
  <label>
    <input type="radio" name="gender" value="female" [(ngModel)]="user.gender">
    Female
  </label>
  <label>
    <input type="radio" name="gender" value="other" [(ngModel)]="user.gender">
    Other
  </label>
</div>
```

### Checkboxes

```typescript
<!-- Single Checkbox -->
<label>
  <input 
    type="checkbox" 
    name="agreeToTerms" 
    [(ngModel)]="user.agreeToTerms"
    required>
  I agree to terms and conditions
</label>

<!-- Multiple Checkboxes -->
<div *ngFor="let interest of interests">
  <label>
    <input 
      type="checkbox" 
      [value]="interest.id"
      (change)="onInterestChange($event, interest)">
    {{ interest.name }}
  </label>
</div>
```

### Textarea

```typescript
<textarea 
  name="bio"
  [(ngModel)]="user.bio"
  rows="4"
  maxlength="200"
  #bio="ngModel">
</textarea>
<small>{{ bio.value?.length || 0 }} / 200 characters</small>
```

## Complete Example: User Profile Form

```typescript
import { Component } from '@angular/core';
import { FormsModule, NgForm } from '@angular/forms';
import { CommonModule } from '@angular/common';

interface UserProfile {
  // Personal Info
  firstName: string;
  lastName: string;
  email: string;
  phone: string;
  dateOfBirth: string;
  
  // Address
  street: string;
  city: string;
  state: string;
  zipCode: string;
  country: string;
  
  // Preferences
  gender: string;
  interests: string[];
  newsletter: boolean;
  notifications: boolean;
  
  // Additional
  bio: string;
}

@Component({
  selector: 'app-profile-form',
  standalone: true,
  imports: [FormsModule, CommonModule],
  template: `
    <form #profileForm="ngForm" (ngSubmit)="onSubmit(profileForm)" class="profile-form">
      <h1>User Profile</h1>
      
      <!-- Personal Information -->
      <section>
        <h2>Personal Information</h2>
        
        <div class="form-row">
          <div class="form-group">
            <label>First Name *</label>
            <input 
              type="text"
              name="firstName"
              [(ngModel)]="profile.firstName"
              required
              minlength="2"
              #firstName="ngModel"
              placeholder="John">
            <div class="error" *ngIf="firstName.invalid && firstName.touched">
              <small *ngIf="firstName.errors?.['required']">Required</small>
              <small *ngIf="firstName.errors?.['minlength']">Min 2 chars</small>
            </div>
          </div>
          
          <div class="form-group">
            <label>Last Name *</label>
            <input 
              type="text"
              name="lastName"
              [(ngModel)]="profile.lastName"
              required
              minlength="2"
              #lastName="ngModel"
              placeholder="Doe">
            <div class="error" *ngIf="lastName.invalid && lastName.touched">
              <small *ngIf="lastName.errors?.['required']">Required</small>
            </div>
          </div>
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label>Email *</label>
            <input 
              type="email"
              name="email"
              [(ngModel)]="profile.email"
              required
              email
              #email="ngModel"
              placeholder="john@example.com">
            <div class="error" *ngIf="email.invalid && email.touched">
              <small *ngIf="email.errors?.['required']">Required</small>
              <small *ngIf="email.errors?.['email']">Invalid email</small>
            </div>
          </div>
          
          <div class="form-group">
            <label>Phone</label>
            <input 
              type="tel"
              name="phone"
              [(ngModel)]="profile.phone"
              pattern="[0-9]{10}"
              #phone="ngModel"
              placeholder="1234567890">
            <div class="error" *ngIf="phone.invalid && phone.touched">
              <small *ngIf="phone.errors?.['pattern']">10 digits required</small>
            </div>
          </div>
        </div>
        
        <div class="form-group">
          <label>Date of Birth</label>
          <input 
            type="date"
            name="dateOfBirth"
            [(ngModel)]="profile.dateOfBirth"
            max="{{ today }}">
        </div>
        
        <div class="form-group">
          <label>Gender</label>
          <div class="radio-group">
            <label>
              <input type="radio" name="gender" value="male" [(ngModel)]="profile.gender">
              Male
            </label>
            <label>
              <input type="radio" name="gender" value="female" [(ngModel)]="profile.gender">
              Female
            </label>
            <label>
              <input type="radio" name="gender" value="other" [(ngModel)]="profile.gender">
              Other
            </label>
          </div>
        </div>
      </section>
      
      <!-- Address -->
      <section>
        <h2>Address</h2>
        
        <div class="form-group">
          <label>Street Address</label>
          <input 
            type="text"
            name="street"
            [(ngModel)]="profile.street"
            placeholder="123 Main St">
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label>City</label>
            <input 
              type="text"
              name="city"
              [(ngModel)]="profile.city"
              placeholder="New York">
          </div>
          
          <div class="form-group">
            <label>State</label>
            <input 
              type="text"
              name="state"
              [(ngModel)]="profile.state"
              placeholder="NY">
          </div>
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label>Zip Code</label>
            <input 
              type="text"
              name="zipCode"
              [(ngModel)]="profile.zipCode"
              pattern="[0-9]{5}"
              placeholder="12345">
          </div>
          
          <div class="form-group">
            <label>Country</label>
            <select name="country" [(ngModel)]="profile.country">
              <option value="">Select Country</option>
              <option value="usa">United States</option>
              <option value="uk">United Kingdom</option>
              <option value="canada">Canada</option>
              <option value="australia">Australia</option>
            </select>
          </div>
        </div>
      </section>
      
      <!-- Preferences -->
      <section>
        <h2>Preferences</h2>
        
        <div class="form-group">
          <label>Interests</label>
          <div class="checkbox-group">
            <label *ngFor="let interest of availableInterests">
              <input 
                type="checkbox"
                [value]="interest"
                (change)="onInterestChange($event, interest)">
              {{ interest }}
            </label>
          </div>
        </div>
        
        <div class="form-group">
          <label>Bio</label>
          <textarea 
            name="bio"
            [(ngModel)]="profile.bio"
            rows="4"
            maxlength="500"
            #bio="ngModel"
            placeholder="Tell us about yourself...">
          </textarea>
          <small>{{ bio.value?.length || 0 }} / 500 characters</small>
        </div>
        
        <div class="form-group">
          <label>
            <input 
              type="checkbox"
              name="newsletter"
              [(ngModel)]="profile.newsletter">
            Subscribe to newsletter
          </label>
        </div>
        
        <div class="form-group">
          <label>
            <input 
              type="checkbox"
              name="notifications"
              [(ngModel)]="profile.notifications">
            Enable notifications
          </label>
        </div>
      </section>
      
      <!-- Actions -->
      <div class="form-actions">
        <button type="button" (click)="profileForm.reset()" class="btn-secondary">
          Reset
        </button>
        <button type="submit" [disabled]="profileForm.invalid" class="btn-primary">
          Save Profile
        </button>
      </div>
      
      <!-- Debug Info -->
      <div class="debug" *ngIf="showDebug">
        <h3>Form Debug Info</h3>
        <p>Form Valid: {{ profileForm.valid }}</p>
        <p>Form Touched: {{ profileForm.touched }}</p>
        <p>Form Dirty: {{ profileForm.dirty }}</p>
        <pre>{{ profile | json }}</pre>
      </div>
    </form>
  `,
  styles: [`
    .profile-form {
      max-width: 800px;
      margin: 20px auto;
      padding: 30px;
      background: white;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    
    section {
      margin: 30px 0;
      padding: 20px;
      background: #f8f9fa;
      border-radius: 4px;
    }
    
    h1 {
      text-align: center;
      color: #333;
    }
    
    h2 {
      color: #007bff;
      margin-bottom: 20px;
    }
    
    .form-row {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
    }
    
    .form-group {
      margin: 15px 0;
    }
    
    label {
      display: block;
      margin-bottom: 5px;
      font-weight: bold;
      color: #555;
    }
    
    input[type="text"],
    input[type="email"],
    input[type="tel"],
    input[type="date"],
    select,
    textarea {
      width: 100%;
      padding: 10px;
      border: 1px solid #ddd;
      border-radius: 4px;
      font-size: 14px;
    }
    
    input.ng-invalid.ng-touched,
    select.ng-invalid.ng-touched,
    textarea.ng-invalid.ng-touched {
      border-color: #dc3545;
    }
    
    input.ng-valid.ng-touched,
    select.ng-valid.ng-touched,
    textarea.ng-valid.ng-touched {
      border-color: #28a745;
    }
    
    .error small {
      display: block;
      color: #dc3545;
      font-size: 12px;
      margin-top: 5px;
    }
    
    .radio-group,
    .checkbox-group {
      display: flex;
      flex-wrap: wrap;
      gap: 15px;
    }
    
    .radio-group label,
    .checkbox-group label {
      font-weight: normal;
      display: flex;
      align-items: center;
      gap: 5px;
    }
    
    .form-actions {
      display: flex;
      gap: 15px;
      justify-content: flex-end;
      margin-top: 30px;
    }
    
    button {
      padding: 12px 30px;
      border: none;
      border-radius: 4px;
      font-size: 16px;
      cursor: pointer;
      transition: all 0.3s;
    }
    
    .btn-primary {
      background: #007bff;
      color: white;
    }
    
    .btn-primary:hover:not(:disabled) {
      background: #0056b3;
    }
    
    .btn-primary:disabled {
      background: #ccc;
      cursor: not-allowed;
    }
    
    .btn-secondary {
      background: #6c757d;
      color: white;
    }
    
    .btn-secondary:hover {
      background: #545b62;
    }
    
    .debug {
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
export class ProfileFormComponent {
  showDebug = false;
  today = new Date().toISOString().split('T')[0];
  
  profile: UserProfile = {
    firstName: '',
    lastName: '',
    email: '',
    phone: '',
    dateOfBirth: '',
    street: '',
    city: '',
    state: '',
    zipCode: '',
    country: '',
    gender: '',
    interests: [],
    newsletter: false,
    notifications: true,
    bio: ''
  };
  
  availableInterests = [
    'Programming',
    'Design',
    'Marketing',
    'Business',
    'Photography',
    'Music'
  ];
  
  onInterestChange(event: Event, interest: string): void {
    const checkbox = event.target as HTMLInputElement;
    if (checkbox.checked) {
      this.profile.interests.push(interest);
    } else {
      const index = this.profile.interests.indexOf(interest);
      if (index > -1) {
        this.profile.interests.splice(index, 1);
      }
    }
  }
  
  onSubmit(form: NgForm): void {
    if (form.valid) {
      console.log('Profile saved:', this.profile);
      alert('Profile saved successfully!');
    } else {
      alert('Please fill all required fields correctly');
    }
  }
}
```

## Summary

### Pros of Template Driven Forms
- ✅ Easier to set up
- ✅ Less code
- ✅ Good for simple forms
- ✅ Familiar to developers from Angular JS

### Cons of Template Driven Forms
- ❌ Less control over validation
- ❌ Harder to unit test
- ❌ Logic in template
- ❌ Not suitable for complex forms

## Next Steps

- Learn Reactive Forms for more control
- Understand custom validators
- Master form arrays and dynamic forms
- Explore async validators
