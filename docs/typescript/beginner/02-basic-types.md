# TypeScript Basic Types

## Primitive Types

### 1. String
Textual data enclosed in quotes.

```typescript
// String types
let firstName: string = "John";
let lastName: string = 'Doe';
let fullName: string = `${firstName} ${lastName}`; // Template literals

// Real-world example: User profile
interface UserProfile {
  username: string;
  bio: string;
  website: string;
}

const profile: UserProfile = {
  username: "developer123",
  bio: "Full-stack developer passionate about TypeScript",
  website: "https://example.com"
};
```

### 2. Number
All numeric values (integers and floats).

```typescript
// Number types
let age: number = 30;
let price: number = 99.99;
let hexValue: number = 0xf00d;
let binaryValue: number = 0b1010;
let octalValue: number = 0o744;

// Real-world example: E-commerce calculations
function calculateTotal(price: number, quantity: number, taxRate: number): number {
  const subtotal = price * quantity;
  const tax = subtotal * taxRate;
  return subtotal + tax;
}

const orderTotal = calculateTotal(29.99, 3, 0.08);
console.log(`Total: $${orderTotal.toFixed(2)}`); // Total: $97.17
```

### 3. Boolean
True or false values.

```typescript
// Boolean types
let isActive: boolean = true;
let hasPermission: boolean = false;

// Real-world example: Feature flags and permissions
interface FeatureFlags {
  isDarkModeEnabled: boolean;
  showBetaFeatures: boolean;
  allowComments: boolean;
}

function checkAccess(isAdmin: boolean, isLoggedIn: boolean): boolean {
  return isAdmin && isLoggedIn;
}

const userAccess = checkAccess(true, true); // true
```

### 4. Null and Undefined
Absence of value.

```typescript
// Null and Undefined
let nullValue: null = null;
let undefinedValue: undefined = undefined;

// In strict mode, these are separate types
let maybeString: string | null = null; // Union type
let optional: string | undefined = undefined;

// Real-world example: Optional user data
interface User {
  id: number;
  name: string;
  email: string;
  phoneNumber: string | null; // Optional field
  lastLogin: Date | undefined; // May not be set yet
}

const user: User = {
  id: 1,
  name: "Jane Smith",
  email: "jane@example.com",
  phoneNumber: null, // User hasn't provided phone
  lastLogin: undefined // Never logged in
};
```

## Array Types

### Basic Arrays
```typescript
// Array syntax - two ways
let numbers: number[] = [1, 2, 3, 4, 5];
let strings: Array<string> = ["apple", "banana", "orange"];

// Real-world example: Shopping cart
interface Product {
  id: number;
  name: string;
  price: number;
}

let cart: Product[] = [
  { id: 1, name: "Laptop", price: 999 },
  { id: 2, name: "Mouse", price: 29 },
  { id: 3, name: "Keyboard", price: 79 }
];

// Calculate total
const total = cart.reduce((sum, product) => sum + product.price, 0);
console.log(`Cart total: $${total}`); // Cart total: $1107
```

### Multi-dimensional Arrays
```typescript
// 2D Array
let matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];

// Real-world example: Seating chart
type Seat = "available" | "booked" | "reserved";
let theaterSeating: Seat[][] = [
  ["available", "available", "booked"],
  ["reserved", "available", "available"],
  ["booked", "booked", "available"]
];
```

## Tuple Types

Fixed-length arrays with specific types for each position.

```typescript
// Basic tuple
let person: [string, number] = ["Alice", 30];

// Accessing tuple elements
let name = person[0]; // string
let age = person[1];  // number

// Real-world example: Database query results
type QueryResult = [boolean, string, any];

function executeQuery(sql: string): QueryResult {
  try {
    // Simulate query execution
    const result = { id: 1, name: "John" };
    return [true, "Query executed successfully", result];
  } catch (error) {
    return [false, "Query failed", null];
  }
}

const [success, message, data] = executeQuery("SELECT * FROM users");
if (success) {
  console.log("Data:", data);
}
```

### Tuple with Optional and Rest Elements
```typescript
// Optional elements
let optionalTuple: [string, number?] = ["test"];
optionalTuple = ["test", 42];

// Rest elements
let restTuple: [string, ...number[]] = ["scores", 85, 90, 95, 88];

// Real-world example: API response
type ApiResponse = [number, string, ...any[]];

function apiCall(endpoint: string): ApiResponse {
  return [200, "Success", { userId: 1 }, { userName: "John" }];
}
```

## Enum Types

Named constants for better code readability.

```typescript
// Numeric enum
enum Status {
  Pending,    // 0
  Active,     // 1
  Inactive,   // 2
  Deleted     // 3
}

let userStatus: Status = Status.Active;
console.log(userStatus); // 1

// String enum
enum LogLevel {
  Error = "ERROR",
  Warning = "WARNING",
  Info = "INFO",
  Debug = "DEBUG"
}

// Real-world example: Order management system
enum OrderStatus {
  Pending = "PENDING",
  Processing = "PROCESSING",
  Shipped = "SHIPPED",
  Delivered = "DELIVERED",
  Cancelled = "CANCELLED"
}

interface Order {
  orderId: string;
  status: OrderStatus;
  items: Product[];
}

function updateOrderStatus(order: Order, newStatus: OrderStatus): void {
  order.status = newStatus;
  console.log(`Order ${order.orderId} status updated to ${newStatus}`);
}

const myOrder: Order = {
  orderId: "ORD-12345",
  status: OrderStatus.Pending,
  items: []
};

updateOrderStatus(myOrder, OrderStatus.Processing);
```

### Enum Best Practices
```typescript
// Const enum for performance (inlined at compile time)
const enum Direction {
  Up,
  Down,
  Left,
  Right
}

let move: Direction = Direction.Up; // Compiled to: let move = 0;

// Heterogeneous enum (not recommended)
enum Mixed {
  No = 0,
  Yes = "YES"
}
```

## Any Type

Opt-out of type checking (use sparingly).

```typescript
// Any type
let variable: any = "string";
variable = 42;           // OK
variable = true;         // OK
variable.anyMethod();    // No error, but dangerous!

// Real-world example: Working with third-party libraries
function parseJson(jsonString: string): any {
  return JSON.parse(jsonString);
}

const data: any = parseJson('{"name": "John", "age": 30}');
console.log(data.name); // Works, but no type safety
```

## Unknown Type

Type-safe alternative to any.

```typescript
// Unknown type
let userInput: unknown;

userInput = "hello";
userInput = 42;

// Must check type before using
if (typeof userInput === "string") {
  console.log(userInput.toUpperCase()); // OK
}

// Real-world example: API response validation
function processApiResponse(response: unknown): void {
  // Type guard
  if (typeof response === "object" && response !== null && "data" in response) {
    console.log("Valid response:", response);
  } else {
    console.error("Invalid response format");
  }
}
```

## Void Type

Absence of return value.

```typescript
// Void type
function logMessage(message: string): void {
  console.log(message);
  // No return statement
}

// Real-world example: Event handlers
function handleClick(event: MouseEvent): void {
  console.log(`Button clicked at (${event.clientX}, ${event.clientY})`);
}
```

## Never Type

Represents values that never occur.

```typescript
// Never type
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {
    // Never returns
  }
}

// Real-world example: Exhaustive type checking
type Shape = "circle" | "square" | "triangle";

function getArea(shape: Shape): number {
  switch (shape) {
    case "circle":
      return Math.PI * 10 * 10;
    case "square":
      return 10 * 10;
    case "triangle":
      return 0.5 * 10 * 10;
    default:
      // If we add a new shape and forget to handle it,
      // TypeScript will catch it here
      const exhaustiveCheck: never = shape;
      throw new Error(`Unhandled shape: ${exhaustiveCheck}`);
  }
}
```

## Type Assertions

Tell TypeScript you know better about the type.

```typescript
// Type assertion - two syntaxes
let someValue: any = "this is a string";
let strLength1: number = (someValue as string).length;
let strLength2: number = (<string>someValue).length; // Not usable in JSX

// Real-world example: DOM manipulation
const inputElement = document.getElementById("username") as HTMLInputElement;
inputElement.value = "John Doe";

// Non-null assertion
const maybeElement = document.getElementById("app");
const element = maybeElement!; // Assert it's not null
element.innerHTML = "Hello";
```

## Practical Example: Form Validation

```typescript
enum ValidationStatus {
  Valid = "VALID",
  Invalid = "INVALID",
  Pending = "PENDING"
}

interface FormField {
  name: string;
  value: string | number;
  required: boolean;
  status: ValidationStatus;
}

interface FormData {
  fields: FormField[];
  isValid: boolean;
  errors: string[];
}

function validateForm(formData: FormData): boolean {
  let isValid = true;
  const errors: string[] = [];

  for (const field of formData.fields) {
    if (field.required && !field.value) {
      field.status = ValidationStatus.Invalid;
      errors.push(`${field.name} is required`);
      isValid = false;
    } else {
      field.status = ValidationStatus.Valid;
    }
  }

  formData.isValid = isValid;
  formData.errors = errors;
  
  return isValid;
}

// Usage
const form: FormData = {
  fields: [
    { name: "username", value: "", required: true, status: ValidationStatus.Pending },
    { name: "email", value: "user@example.com", required: true, status: ValidationStatus.Pending },
    { name: "age", value: 25, required: false, status: ValidationStatus.Pending }
  ],
  isValid: false,
  errors: []
};

if (validateForm(form)) {
  console.log("Form is valid!");
} else {
  console.log("Form errors:", form.errors);
}
```

## Summary

| Type | Description | Use Case |
|------|-------------|----------|
| `string` | Text data | Names, messages, URLs |
| `number` | Numeric values | Quantities, prices, IDs |
| `boolean` | True/false | Flags, conditions |
| `array` | Collection of values | Lists, collections |
| `tuple` | Fixed-length array | Coordinates, key-value pairs |
| `enum` | Named constants | Status codes, categories |
| `any` | Any type (avoid) | Migration, unknown types |
| `unknown` | Type-safe any | User input, API responses |
| `void` | No return value | Event handlers, loggers |
| `never` | Never returns | Error functions |

## Next Steps

- Learn about interfaces and type aliases
- Understand union and intersection types
- Master type guards and narrowing
- Explore generic types
