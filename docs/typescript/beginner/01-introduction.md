# TypeScript Introduction

## What is TypeScript?

TypeScript is a **strongly typed** programming language that builds on JavaScript by adding static type definitions. It was developed and is maintained by Microsoft.

### Key Features
- **Static Typing**: Catch errors at compile-time rather than runtime
- **Type Inference**: Automatically detects types when possible
- **Modern JavaScript Features**: Supports ES6+ features
- **Better Tooling**: Enhanced IDE support with autocomplete and intellisense
- **Compiles to JavaScript**: Runs anywhere JavaScript runs

## Why TypeScript?

### Problems with JavaScript
```javascript
// JavaScript - Runtime errors
function add(a, b) {
  return a + b;
}

console.log(add(5, 10));        // 15 ✓
console.log(add("5", 10));      // "510" ✗ (unexpected string concatenation)
console.log(add(5, null));      // 5 ✗ (unexpected behavior)
```

### Solution with TypeScript
```typescript
// TypeScript - Compile-time errors
function add(a: number, b: number): number {
  return a + b;
}

console.log(add(5, 10));        // 15 ✓
console.log(add("5", 10));      // Error: Argument of type 'string' is not assignable
console.log(add(5, null));      // Error: Argument of type 'null' is not assignable
```

## Setting Up TypeScript

### Installation
```bash
# Global installation
npm install -g typescript

# Project-specific installation
npm install --save-dev typescript

# Check version
tsc --version
```

### Creating a TypeScript Project
```bash
# Initialize npm project
npm init -y

# Install TypeScript
npm install --save-dev typescript

# Create TypeScript config
tsc --init
```

### Basic Configuration (tsconfig.json)
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

## Your First TypeScript Program

### hello.ts
```typescript
// Define a function with type annotations
function greet(name: string): string {
  return `Hello, ${name}!`;
}

// Use the function
const userName: string = "TypeScript Developer";
const message: string = greet(userName);

console.log(message); // Output: Hello, TypeScript Developer!
```

### Compiling and Running
```bash
# Compile TypeScript to JavaScript
tsc hello.ts

# This creates hello.js
# Run the JavaScript file
node hello.js
```

## TypeScript Workflow

```
TypeScript Code (.ts) 
    ↓
TypeScript Compiler (tsc)
    ↓
JavaScript Code (.js)
    ↓
Runtime (Node.js/Browser)
```

## Real-World Use Case

### Problem: User Registration Validation
Without TypeScript, validation errors might only appear at runtime:

```typescript
// With TypeScript - Errors caught during development
interface UserRegistration {
  username: string;
  email: string;
  age: number;
  agreedToTerms: boolean;
}

function registerUser(user: UserRegistration): void {
  if (!user.agreedToTerms) {
    throw new Error("User must agree to terms");
  }
  
  if (user.age < 18) {
    throw new Error("User must be 18 or older");
  }
  
  console.log(`Registering user: ${user.username}`);
  // Database save logic here
}

// Usage
const newUser: UserRegistration = {
  username: "john_doe",
  email: "john@example.com",
  age: 25,
  agreedToTerms: true
};

registerUser(newUser); // Works!

// This would cause compile-time error:
// registerUser({ username: "jane", age: "25" }); // Error: Type 'string' is not assignable to type 'number'
```

## Benefits in Real Projects

1. **Early Error Detection**: Find bugs before runtime
2. **Better Documentation**: Types serve as inline documentation
3. **Refactoring Confidence**: Safe code changes across large codebases
4. **Team Collaboration**: Clear contracts between code modules
5. **Enhanced IDE Support**: Better autocomplete and navigation

## Next Steps

- Learn about basic types (string, number, boolean, etc.)
- Understand interfaces and type aliases
- Explore functions and their type annotations
- Master arrays and tuples
- Work with enums and literals
