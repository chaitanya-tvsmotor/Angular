# Reactive Forms - Complete Guide

> Timestamps: **5:16:01** - Reactive Forms | **5:39:56** - Reactive Form Validation

## Introduction

Reactive Forms provide a model-driven approach to handling form inputs with:
- Explicit form structure in component class
- Immutable data model
- Synchronous access to data
- Easier unit testing
- Better scalability for complex forms

---

## Setup

```typescript
import { Component, inject } from '@angular/core';
import { ReactiveFormsModule, FormBuilder, FormGroup, FormControl, Validators } from '@angular/forms';

@Component({
  selector: 'app-registration',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="registrationForm" (ngSubmit)="onSubmit()">
      <div>
        <label>Username:</label>
        <input formControlName="username">
        @if (username.invalid && username.touched) {
          <span class="error">Username is required</span>
        }
      </div>
      
      <div>
        <label>Email:</label>
        <input formControlName="email" type="email">
        @if (email.invalid && email.touched) {
          @if (email.errors?.['required']) {
            <span class="error">Email is required</span>
          }
          @if (email.errors?.['email']) {
            <span class="error">Invalid email format</span>
          }
        }
      </div>
      
      <div>
        <label>Password:</label>
        <input formControlName="password" type="password">
        @if (password.invalid && password.touched) {
          <span class="error">Password must be at least 6 characters</span>
        }
      </div>
      
      <button type="submit" [disabled]="registrationForm.invalid">Register</button>
    </form>
  `,
  styles: [`
    form {
      max-width: 400px;
      margin: 2rem auto;
      padding: 2rem;
      border: 1px solid #ddd;
      border-radius: 8px;
    }
    div {
      margin-bottom: 1rem;
    }
    label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: bold;
    }
    input {
      width: 100%;
      padding: 0.5rem;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    input.ng-invalid.ng-touched {
      border-color: #f44336;
    }
    .error {
      color: #f44336;
      font-size: 0.875rem;
      display: block;
      margin-top: 0.25rem;
    }
    button {
      width: 100%;
      padding: 0.75rem;
      background: #1976d2;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    button:disabled {
      background: #ccc;
      cursor: not-allowed;
    }
  `]
})
export class RegistrationComponent {
  private fb = inject(FormBuilder);
  
  registrationForm = this.fb.group({
    username: ['', Validators.required],
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(6)]]
  });
  
  get username() { return this.registrationForm.get('username')!; }
  get email() { return this.registrationForm.get('email')!; }
  get password() { return this.registrationForm.get('password')!; }
  
  onSubmit(): void {
    if (this.registrationForm.valid) {
      console.log('Form submitted:', this.registrationForm.value);
    }
  }
}
```

---

## Built-in Validators

| Validator | Description | Usage |
|-----------|-------------|-------|
| `Validators.required` | Field must have a value | `Validators.required` |
| `Validators.email` | Must be valid email | `Validators.email` |
| `Validators.minLength(n)` | Minimum length | `Validators.minLength(6)` |
| `Validators.maxLength(n)` | Maximum length | `Validators.maxLength(20)` |
| `Validators.min(n)` | Minimum number value | `Validators.min(18)` |
| `Validators.max(n)` | Maximum number value | `Validators.max(100)` |
| `Validators.pattern(regex)` | Must match regex | `Validators.pattern(/^[0-9]+$/)` |

---

## Custom Validators

```typescript
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

// Custom validator function
export function passwordStrength(): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const value = control.value;
    if (!value) return null;
    
    const hasNumber = /[0-9]/.test(value);
    const hasUpper = /[A-Z]/.test(value);
    const hasLower = /[a-z]/.test(value);
    const hasSpecial = /[!@#$%^&*]/.test(value);
    
    const valid = hasNumber && hasUpper && hasLower && hasSpecial;
    
    return valid ? null : { passwordStrength: true };
  };
}

// Async validator (check username availability)
export function usernameValidator(userService: UserService): AsyncValidatorFn {
  return (control: AbstractControl): Observable<ValidationErrors | null> => {
    return userService.checkUsername(control.value).pipe(
      map(exists => exists ? { usernameTaken: true } : null),
      catchError(() => of(null))
    );
  };
}

// Usage
this.fb.group({
  username: ['', [Validators.required], [usernameValidator(this.userService)]],
  password: ['', [Validators.required, passwordStrength()]]
});
```

---

## FormArray - Dynamic Forms

```typescript
import { FormArray } from '@angular/forms';

@Component({
  selector: 'app-skills-form',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form">
      <div formArrayName="skills">
        @for (skill of skills.controls; track $index) {
          <div [formGroupName]="$index">
            <input formControlName="name" placeholder="Skill name">
            <input formControlName="level" type="number" min="1" max="10">
            <button type="button" (click)="removeSkill($index)">Remove</button>
          </div>
        }
      </div>
      <button type="button" (click)="addSkill()">Add Skill</button>
    </form>
  `
})
export class SkillsFormComponent {
  private fb = inject(FormBuilder);
  
  form = this.fb.group({
    skills: this.fb.array([])
  });
  
  get skills() {
    return this.form.get('skills') as FormArray;
  }
  
  addSkill(): void {
    const skillForm = this.fb.group({
      name: ['', Validators.required],
      level: [1, [Validators.min(1), Validators.max(10)]]
    });
    this.skills.push(skillForm);
  }
  
  removeSkill(index: number): void {
    this.skills.removeAt(index);
  }
}
```

---

## Summary

✅ **Reactive Forms**: Model-driven, explicit form structure

✅ **FormBuilder**: Simplifies form creation

✅ **Validators**: Built-in and custom validation

✅ **FormArray**: Dynamic form fields

✅ **Async Validators**: Server-side validation

Reactive forms provide powerful, scalable form management!
