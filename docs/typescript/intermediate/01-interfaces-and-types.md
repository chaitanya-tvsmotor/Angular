# Interfaces and Type Aliases

## Interfaces

Interfaces define the structure of objects in TypeScript.

### Basic Interface

```typescript
interface User {
  id: number;
  username: string;
  email: string;
}

const user: User = {
  id: 1,
  username: "johndoe",
  email: "john@example.com"
};

// Real-world example: Product catalog
interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
  inStock: boolean;
  category: string;
}

const laptop: Product = {
  id: 101,
  name: "Dell XPS 15",
  price: 1299.99,
  description: "High-performance laptop",
  inStock: true,
  category: "Electronics"
};
```

### Optional Properties

```typescript
interface UserProfile {
  id: number;
  username: string;
  email: string;
  phoneNumber?: string;      // Optional
  avatar?: string;           // Optional
  bio?: string;              // Optional
}

const profile1: UserProfile = {
  id: 1,
  username: "john",
  email: "john@example.com"
};

const profile2: UserProfile = {
  id: 2,
  username: "jane",
  email: "jane@example.com",
  phoneNumber: "+1234567890",
  avatar: "https://example.com/avatar.jpg"
};
```

### Readonly Properties

```typescript
interface Config {
  readonly apiKey: string;
  readonly apiUrl: string;
  timeout: number;
}

const config: Config = {
  apiKey: "abc123",
  apiUrl: "https://api.example.com",
  timeout: 5000
};

// config.apiKey = "new-key"; // Error: Cannot assign to 'apiKey'
config.timeout = 10000; // OK

// Real-world example: Database connection config
interface DatabaseConfig {
  readonly host: string;
  readonly port: number;
  readonly database: string;
  readonly username: string;
  readonly password: string;
  poolSize?: number;
}
```

### Function Types in Interfaces

```typescript
interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
  multiply(a: number, b: number): number;
  divide(a: number, b: number): number;
}

const calculator: Calculator = {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b,
  multiply: (a, b) => a * b,
  divide: (a, b) => a / b
};

// Real-world example: Logger interface
interface Logger {
  info(message: string): void;
  warn(message: string): void;
  error(message: string, error?: Error): void;
  debug(message: string, metadata?: any): void;
}

class ConsoleLogger implements Logger {
  info(message: string): void {
    console.log(`[INFO] ${message}`);
  }
  
  warn(message: string): void {
    console.warn(`[WARN] ${message}`);
  }
  
  error(message: string, error?: Error): void {
    console.error(`[ERROR] ${message}`, error);
  }
  
  debug(message: string, metadata?: any): void {
    console.debug(`[DEBUG] ${message}`, metadata);
  }
}
```

### Index Signatures

```typescript
// String index signature
interface StringMap {
  [key: string]: string;
}

const translations: StringMap = {
  hello: "Hola",
  goodbye: "Adiós",
  thanks: "Gracias"
};

// Number index signature
interface NumberArray {
  [index: number]: number;
}

const fibonacci: NumberArray = [0, 1, 1, 2, 3, 5, 8, 13];

// Real-world example: Configuration object
interface AppSettings {
  [key: string]: string | number | boolean;
  appName: string;
  version: string;
  port: number;
  debug: boolean;
}

const settings: AppSettings = {
  appName: "MyApp",
  version: "1.0.0",
  port: 3000,
  debug: true,
  theme: "dark",        // string
  maxRetries: 3,        // number
  enableCache: true     // boolean
};
```

### Extending Interfaces

```typescript
interface Person {
  name: string;
  age: number;
}

interface Employee extends Person {
  employeeId: number;
  department: string;
  salary: number;
}

const employee: Employee = {
  name: "John Doe",
  age: 30,
  employeeId: 12345,
  department: "Engineering",
  salary: 75000
};

// Multiple inheritance
interface Timestamped {
  createdAt: Date;
  updatedAt: Date;
}

interface Auditable {
  createdBy: string;
  updatedBy: string;
}

interface Document extends Timestamped, Auditable {
  id: string;
  title: string;
  content: string;
}

const document: Document = {
  id: "doc-123",
  title: "Report",
  content: "Document content...",
  createdAt: new Date(),
  updatedAt: new Date(),
  createdBy: "john",
  updatedBy: "john"
};
```

## Type Aliases

Type aliases create a new name for any type.

### Basic Type Aliases

```typescript
type UserID = number;
type Username = string;

type User = {
  id: UserID;
  username: Username;
  email: string;
};

const user: User = {
  id: 1,
  username: "johndoe",
  email: "john@example.com"
};
```

### Union Types

```typescript
// Union type
type Status = "pending" | "approved" | "rejected";
type ID = number | string;

function updateStatus(id: ID, status: Status): void {
  console.log(`Updating ${id} to ${status}`);
}

updateStatus(123, "approved");
updateStatus("abc-456", "pending");

// Real-world example: API response
type ApiResult<T> = {
  success: true;
  data: T;
} | {
  success: false;
  error: string;
};

function handleResponse<T>(result: ApiResult<T>): T | null {
  if (result.success) {
    return result.data;
  } else {
    console.error(result.error);
    return null;
  }
}

const successResponse: ApiResult<User> = {
  success: true,
  data: { id: 1, username: "john", email: "john@example.com" }
};

const errorResponse: ApiResult<User> = {
  success: false,
  error: "User not found"
};
```

### Intersection Types

```typescript
// Intersection type
type Printable = {
  print(): void;
};

type Deletable = {
  delete(): void;
};

type Document = Printable & Deletable & {
  id: string;
  title: string;
};

const doc: Document = {
  id: "doc-1",
  title: "My Document",
  print() {
    console.log(`Printing: ${this.title}`);
  },
  delete() {
    console.log(`Deleting: ${this.title}`);
  }
};

// Real-world example: Mixin pattern
type Timestamped = {
  createdAt: Date;
  updatedAt: Date;
};

type Identifiable = {
  id: string;
};

type Entity = Identifiable & Timestamped;

type User = Entity & {
  username: string;
  email: string;
};

type Product = Entity & {
  name: string;
  price: number;
};
```

### Literal Types

```typescript
// String literal types
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Theme = "light" | "dark" | "auto";

// Number literal types
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
type Port = 80 | 443 | 8080 | 3000;

// Boolean literal types
type EnabledFlag = true;

// Real-world example: HTTP request configuration
type RequestConfig = {
  url: string;
  method: HttpMethod;
  headers?: Record<string, string>;
  body?: any;
};

function makeRequest(config: RequestConfig): void {
  console.log(`${config.method} ${config.url}`);
}

makeRequest({
  url: "https://api.example.com/users",
  method: "GET"
});
```

## Interface vs Type Alias

### When to Use Interface

```typescript
// 1. When defining object shapes
interface User {
  id: number;
  name: string;
}

// 2. When you need declaration merging
interface Window {
  myCustomProperty: string;
}

interface Window {
  anotherProperty: number;
}
// Both declarations merge into one

// 3. When implementing classes
interface Shape {
  area(): number;
}

class Circle implements Shape {
  constructor(private radius: number) {}
  
  area(): number {
    return Math.PI * this.radius ** 2;
  }
}
```

### When to Use Type Alias

```typescript
// 1. When creating union types
type Result = Success | Failure;

// 2. When creating complex types
type Nullable<T> = T | null;
type ReadonlyArray<T> = readonly T[];

// 3. When using mapped types
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

// 4. When creating tuple types
type Point = [number, number];
type RGB = [number, number, number];
```

## Advanced Patterns

### Discriminated Unions

```typescript
// Tagged union pattern
interface Circle {
  kind: "circle";
  radius: number;
}

interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
}

interface Triangle {
  kind: "triangle";
  base: number;
  height: number;
}

type Shape = Circle | Rectangle | Triangle;

function calculateArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "rectangle":
      return shape.width * shape.height;
    case "triangle":
      return 0.5 * shape.base * shape.height;
  }
}

// Real-world example: Payment processing
interface CreditCardPayment {
  type: "credit-card";
  cardNumber: string;
  expiryDate: string;
  cvv: string;
}

interface PayPalPayment {
  type: "paypal";
  email: string;
}

interface BankTransferPayment {
  type: "bank-transfer";
  accountNumber: string;
  routingNumber: string;
}

type Payment = CreditCardPayment | PayPalPayment | BankTransferPayment;

function processPayment(payment: Payment, amount: number): void {
  switch (payment.type) {
    case "credit-card":
      console.log(`Processing credit card payment: $${amount}`);
      console.log(`Card: **** **** **** ${payment.cardNumber.slice(-4)}`);
      break;
    case "paypal":
      console.log(`Processing PayPal payment: $${amount}`);
      console.log(`Email: ${payment.email}`);
      break;
    case "bank-transfer":
      console.log(`Processing bank transfer: $${amount}`);
      console.log(`Account: ${payment.accountNumber}`);
      break;
  }
}
```

### Generic Interfaces

```typescript
interface Container<T> {
  value: T;
  getValue(): T;
  setValue(value: T): void;
}

class Box<T> implements Container<T> {
  constructor(public value: T) {}
  
  getValue(): T {
    return this.value;
  }
  
  setValue(value: T): void {
    this.value = value;
  }
}

const numberBox = new Box<number>(42);
const stringBox = new Box<string>("Hello");

// Real-world example: Repository pattern
interface Repository<T> {
  find(id: string): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(item: T): Promise<T>;
  update(id: string, item: Partial<T>): Promise<T>;
  delete(id: string): Promise<void>;
}

interface User {
  id: string;
  username: string;
  email: string;
}

class UserRepository implements Repository<User> {
  private users: Map<string, User> = new Map();
  
  async find(id: string): Promise<User | null> {
    return this.users.get(id) || null;
  }
  
  async findAll(): Promise<User[]> {
    return Array.from(this.users.values());
  }
  
  async create(user: User): Promise<User> {
    this.users.set(user.id, user);
    return user;
  }
  
  async update(id: string, updates: Partial<User>): Promise<User> {
    const user = await this.find(id);
    if (!user) throw new Error("User not found");
    
    const updated = { ...user, ...updates };
    this.users.set(id, updated);
    return updated;
  }
  
  async delete(id: string): Promise<void> {
    this.users.delete(id);
  }
}
```

## Real-World Example: E-commerce System

```typescript
// Product types
interface BaseProduct {
  id: string;
  name: string;
  description: string;
  price: number;
  category: string;
  inStock: boolean;
}

interface PhysicalProduct extends BaseProduct {
  type: "physical";
  weight: number;
  dimensions: {
    length: number;
    width: number;
    height: number;
  };
  shippingCost: number;
}

interface DigitalProduct extends BaseProduct {
  type: "digital";
  downloadUrl: string;
  fileSize: number;
  format: string;
}

type Product = PhysicalProduct | DigitalProduct;

// Order types
interface CartItem {
  product: Product;
  quantity: number;
}

interface ShippingAddress {
  street: string;
  city: string;
  state: string;
  zipCode: string;
  country: string;
}

interface Order {
  id: string;
  customerId: string;
  items: CartItem[];
  shippingAddress?: ShippingAddress;
  status: "pending" | "processing" | "shipped" | "delivered" | "cancelled";
  total: number;
  createdAt: Date;
  updatedAt: Date;
}

// Service interfaces
interface PricingService {
  calculateItemPrice(item: CartItem): number;
  calculateShipping(items: CartItem[], address: ShippingAddress): number;
  calculateTax(subtotal: number, address: ShippingAddress): number;
  calculateTotal(items: CartItem[], address?: ShippingAddress): number;
}

class EcommercePricingService implements PricingService {
  calculateItemPrice(item: CartItem): number {
    return item.product.price * item.quantity;
  }
  
  calculateShipping(items: CartItem[], address: ShippingAddress): number {
    // Only physical products need shipping
    const physicalItems = items.filter(
      item => item.product.type === "physical"
    ) as Array<CartItem & { product: PhysicalProduct }>;
    
    if (physicalItems.length === 0) return 0;
    
    const totalWeight = physicalItems.reduce(
      (sum, item) => sum + item.product.weight * item.quantity,
      0
    );
    
    // Simplified shipping calculation
    return totalWeight * 0.5;
  }
  
  calculateTax(subtotal: number, address: ShippingAddress): number {
    // Simplified tax calculation (8% for example)
    return subtotal * 0.08;
  }
  
  calculateTotal(items: CartItem[], address?: ShippingAddress): number {
    const subtotal = items.reduce(
      (sum, item) => sum + this.calculateItemPrice(item),
      0
    );
    
    const shipping = address
      ? this.calculateShipping(items, address)
      : 0;
    
    const tax = address
      ? this.calculateTax(subtotal, address)
      : 0;
    
    return subtotal + shipping + tax;
  }
}

// Usage
const pricingService = new EcommercePricingService();

const laptop: PhysicalProduct = {
  id: "prod-1",
  type: "physical",
  name: "Laptop",
  description: "High-performance laptop",
  price: 999,
  category: "Electronics",
  inStock: true,
  weight: 2.5,
  dimensions: { length: 30, width: 20, height: 2 },
  shippingCost: 15
};

const ebook: DigitalProduct = {
  id: "prod-2",
  type: "digital",
  name: "TypeScript Guide",
  description: "Complete TypeScript handbook",
  price: 29.99,
  category: "Books",
  inStock: true,
  downloadUrl: "https://example.com/download/typescript-guide",
  fileSize: 5242880,
  format: "PDF"
};

const cart: CartItem[] = [
  { product: laptop, quantity: 1 },
  { product: ebook, quantity: 1 }
];

const address: ShippingAddress = {
  street: "123 Main St",
  city: "New York",
  state: "NY",
  zipCode: "10001",
  country: "USA"
};

const total = pricingService.calculateTotal(cart, address);
console.log(`Order total: $${total.toFixed(2)}`);
```

## Summary

| Feature | Interface | Type Alias |
|---------|-----------|------------|
| Object shapes | ✅ Best | ✅ Works |
| Union types | ❌ No | ✅ Yes |
| Intersection | ✅ via extends | ✅ via & |
| Declaration merging | ✅ Yes | ❌ No |
| Implements in class | ✅ Yes | ✅ Yes |
| Tuples | ❌ No | ✅ Yes |
| Primitives/literals | ❌ No | ✅ Yes |

## Best Practices

1. **Use interfaces** for object shapes and class contracts
2. **Use type aliases** for unions, intersections, and complex types
3. **Prefer interfaces** for public API definitions
4. **Use discriminated unions** for type-safe state machines
5. **Make optional properties explicit** with `?`
6. **Use readonly** for immutable properties
7. **Leverage index signatures** for flexible objects

## Next Steps

- Learn about classes and inheritance
- Understand generics in depth
- Explore conditional types
- Master mapped and utility types
