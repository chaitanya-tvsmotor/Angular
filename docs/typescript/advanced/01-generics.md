# Advanced Generics in TypeScript

## What are Generics?

Generics allow you to create reusable components that work with multiple types while maintaining type safety.

## Basic Generic Functions

```typescript
// Without generics - need separate functions for each type
function identityNumber(arg: number): number {
  return arg;
}

function identityString(arg: string): string {
  return arg;
}

// With generics - one function for all types
function identity<T>(arg: T): T {
  return arg;
}

// Usage
const num = identity<number>(42);      // number
const str = identity<string>("hello"); // string
const bool = identity(true);           // boolean (type inferred)
```

## Generic Constraints

Restrict generic types to have certain properties.

```typescript
// Constraint: T must have a length property
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}

logLength("hello");           // OK - string has length
logLength([1, 2, 3]);        // OK - array has length
logLength({ length: 10 });   // OK - object has length
// logLength(42);            // Error - number doesn't have length

// Real-world example: Repository pattern
interface Entity {
  id: string;
  createdAt: Date;
  updatedAt: Date;
}

interface Repository<T extends Entity> {
  findById(id: string): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(item: Omit<T, 'id' | 'createdAt' | 'updatedAt'>): Promise<T>;
  update(id: string, item: Partial<T>): Promise<T>;
  delete(id: string): Promise<void>;
}

interface User extends Entity {
  username: string;
  email: string;
}

interface Product extends Entity {
  name: string;
  price: number;
}

class UserRepository implements Repository<User> {
  async findById(id: string): Promise<User | null> {
    // Implementation
    return null;
  }
  
  async findAll(): Promise<User[]> {
    return [];
  }
  
  async create(userData: Omit<User, 'id' | 'createdAt' | 'updatedAt'>): Promise<User> {
    const user: User = {
      ...userData,
      id: this.generateId(),
      createdAt: new Date(),
      updatedAt: new Date()
    };
    // Save to database
    return user;
  }
  
  async update(id: string, updates: Partial<User>): Promise<User> {
    // Implementation
    throw new Error("Not implemented");
  }
  
  async delete(id: string): Promise<void> {
    // Implementation
  }
  
  private generateId(): string {
    return `user_${Date.now()}`;
  }
}
```

## Generic Classes

```typescript
// Generic stack implementation
class Stack<T> {
  private items: T[] = [];
  
  push(item: T): void {
    this.items.push(item);
  }
  
  pop(): T | undefined {
    return this.items.pop();
  }
  
  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }
  
  isEmpty(): boolean {
    return this.items.length === 0;
  }
  
  size(): number {
    return this.items.length;
  }
  
  clear(): void {
    this.items = [];
  }
}

// Usage
const numberStack = new Stack<number>();
numberStack.push(1);
numberStack.push(2);
numberStack.push(3);
console.log(numberStack.pop()); // 3

const stringStack = new Stack<string>();
stringStack.push("hello");
stringStack.push("world");

// Real-world example: Generic cache
class Cache<K, V> {
  private cache: Map<K, V> = new Map();
  private maxSize: number;
  private ttl: number; // Time to live in ms
  private timestamps: Map<K, number> = new Map();
  
  constructor(maxSize: number = 100, ttl: number = 60000) {
    this.maxSize = maxSize;
    this.ttl = ttl;
  }
  
  set(key: K, value: V): void {
    // Remove oldest if at capacity
    if (this.cache.size >= this.maxSize && !this.cache.has(key)) {
      const oldestKey = this.cache.keys().next().value;
      this.delete(oldestKey);
    }
    
    this.cache.set(key, value);
    this.timestamps.set(key, Date.now());
  }
  
  get(key: K): V | undefined {
    const timestamp = this.timestamps.get(key);
    
    if (!timestamp) {
      return undefined;
    }
    
    // Check if expired
    if (Date.now() - timestamp > this.ttl) {
      this.delete(key);
      return undefined;
    }
    
    return this.cache.get(key);
  }
  
  has(key: K): boolean {
    return this.cache.has(key) && this.get(key) !== undefined;
  }
  
  delete(key: K): boolean {
    this.timestamps.delete(key);
    return this.cache.delete(key);
  }
  
  clear(): void {
    this.cache.clear();
    this.timestamps.clear();
  }
  
  size(): number {
    return this.cache.size;
  }
}

// Usage
interface UserProfile {
  id: string;
  name: string;
  email: string;
}

const userCache = new Cache<string, UserProfile>(50, 300000); // 5 min TTL

userCache.set("user1", {
  id: "user1",
  name: "John Doe",
  email: "john@example.com"
});

const user = userCache.get("user1");
console.log(user);
```

## Multiple Type Parameters

```typescript
// Generic pair/tuple
class Pair<K, V> {
  constructor(public key: K, public value: V) {}
  
  getKey(): K {
    return this.key;
  }
  
  getValue(): V {
    return this.value;
  }
}

const numberStringPair = new Pair<number, string>(1, "one");
const stringBooleanPair = new Pair<string, boolean>("active", true);

// Real-world example: API Response wrapper
interface ApiResponse<T, E = Error> {
  data?: T;
  error?: E;
  status: number;
  timestamp: Date;
}

class ApiClient {
  async get<T>(url: string): Promise<ApiResponse<T>> {
    try {
      const response = await fetch(url);
      const data = await response.json();
      
      return {
        data,
        status: response.status,
        timestamp: new Date()
      };
    } catch (error) {
      return {
        error: error as Error,
        status: 500,
        timestamp: new Date()
      };
    }
  }
  
  async post<T, D>(url: string, body: D): Promise<ApiResponse<T>> {
    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(body)
      });
      const data = await response.json();
      
      return {
        data,
        status: response.status,
        timestamp: new Date()
      };
    } catch (error) {
      return {
        error: error as Error,
        status: 500,
        timestamp: new Date()
      };
    }
  }
}

// Usage
interface User {
  id: string;
  name: string;
  email: string;
}

interface CreateUserDto {
  name: string;
  email: string;
}

const client = new ApiClient();

async function fetchUser(id: string): Promise<User | null> {
  const response = await client.get<User>(`/api/users/${id}`);
  return response.data || null;
}

async function createUser(userData: CreateUserDto): Promise<User | null> {
  const response = await client.post<User, CreateUserDto>('/api/users', userData);
  return response.data || null;
}
```

## Generic Utility Types

```typescript
// Partial - makes all properties optional
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
}

type PartialUser = Partial<User>;
// Equivalent to:
// {
//   id?: string;
//   name?: string;
//   email?: string;
//   age?: number;
// }

function updateUser(id: string, updates: Partial<User>): void {
  // Can update any subset of properties
}

updateUser("1", { name: "John" });
updateUser("2", { email: "jane@example.com", age: 30 });

// Required - makes all properties required
type RequiredUser = Required<Partial<User>>;

// Readonly - makes all properties readonly
type ReadonlyUser = Readonly<User>;

const user: ReadonlyUser = {
  id: "1",
  name: "John",
  email: "john@example.com",
  age: 30
};
// user.name = "Jane"; // Error: Cannot assign to 'name'

// Pick - select specific properties
type UserPreview = Pick<User, 'id' | 'name'>;
// { id: string; name: string; }

// Omit - exclude specific properties
type UserWithoutId = Omit<User, 'id'>;
// { name: string; email: string; age: number; }

// Record - construct object type with specific keys and values
type UserRoles = 'admin' | 'user' | 'guest';
type Permissions = Record<UserRoles, string[]>;

const permissions: Permissions = {
  admin: ['read', 'write', 'delete'],
  user: ['read', 'write'],
  guest: ['read']
};
```

## Advanced Generic Patterns

### Generic Factory Pattern

```typescript
interface Product {
  id: string;
  name: string;
  price: number;
}

interface Service {
  init(): void;
  start(): void;
  stop(): void;
}

type Constructor<T> = new (...args: any[]) => T;

class Factory<T> {
  private instances: Map<string, T> = new Map();
  
  create<K extends T>(
    key: string,
    Constructor: Constructor<K>,
    ...args: any[]
  ): K {
    if (this.instances.has(key)) {
      return this.instances.get(key) as K;
    }
    
    const instance = new Constructor(...args);
    this.instances.set(key, instance);
    return instance;
  }
  
  get(key: string): T | undefined {
    return this.instances.get(key);
  }
  
  has(key: string): boolean {
    return this.instances.has(key);
  }
}

// Usage
class EmailService implements Service {
  constructor(private config: any) {}
  
  init(): void {
    console.log('Email service initialized');
  }
  
  start(): void {
    console.log('Email service started');
  }
  
  stop(): void {
    console.log('Email service stopped');
  }
  
  sendEmail(to: string, subject: string, body: string): void {
    console.log(`Sending email to ${to}`);
  }
}

const serviceFactory = new Factory<Service>();
const emailService = serviceFactory.create(
  'email',
  EmailService,
  { host: 'smtp.example.com' }
);
```

### Generic Builder Pattern

```typescript
class QueryBuilder<T> {
  private conditions: string[] = [];
  private orderByClause: string = '';
  private limitValue: number = 0;
  
  where(condition: string): this {
    this.conditions.push(condition);
    return this;
  }
  
  orderBy(field: keyof T, direction: 'ASC' | 'DESC' = 'ASC'): this {
    this.orderByClause = `ORDER BY ${String(field)} ${direction}`;
    return this;
  }
  
  limit(value: number): this {
    this.limitValue = value;
    return this;
  }
  
  build(): string {
    let query = 'SELECT *';
    
    if (this.conditions.length > 0) {
      query += ` WHERE ${this.conditions.join(' AND ')}`;
    }
    
    if (this.orderByClause) {
      query += ` ${this.orderByClause}`;
    }
    
    if (this.limitValue > 0) {
      query += ` LIMIT ${this.limitValue}`;
    }
    
    return query;
  }
}

// Usage
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
}

const query = new QueryBuilder<User>()
  .where('age > 18')
  .where('email LIKE "%@example.com"')
  .orderBy('name', 'ASC')
  .limit(10)
  .build();

console.log(query);
```

## Real-World Example: Generic Event Emitter

```typescript
type EventHandler<T> = (data: T) => void;

class EventEmitter<Events extends Record<string, any>> {
  private handlers: {
    [K in keyof Events]?: EventHandler<Events[K]>[];
  } = {};
  
  on<K extends keyof Events>(event: K, handler: EventHandler<Events[K]>): void {
    if (!this.handlers[event]) {
      this.handlers[event] = [];
    }
    this.handlers[event]!.push(handler);
  }
  
  off<K extends keyof Events>(event: K, handler: EventHandler<Events[K]>): void {
    const handlers = this.handlers[event];
    if (handlers) {
      const index = handlers.indexOf(handler);
      if (index !== -1) {
        handlers.splice(index, 1);
      }
    }
  }
  
  emit<K extends keyof Events>(event: K, data: Events[K]): void {
    const handlers = this.handlers[event];
    if (handlers) {
      handlers.forEach(handler => handler(data));
    }
  }
  
  once<K extends keyof Events>(event: K, handler: EventHandler<Events[K]>): void {
    const onceHandler: EventHandler<Events[K]> = (data) => {
      handler(data);
      this.off(event, onceHandler);
    };
    this.on(event, onceHandler);
  }
}

// Usage
interface UserEvents {
  login: { userId: string; timestamp: Date };
  logout: { userId: string; timestamp: Date };
  profileUpdate: { userId: string; changes: Record<string, any> };
  error: { message: string; code: number };
}

const userEmitter = new EventEmitter<UserEvents>();

// Type-safe event handling
userEmitter.on('login', (data) => {
  console.log(`User ${data.userId} logged in at ${data.timestamp}`);
});

userEmitter.on('profileUpdate', (data) => {
  console.log(`User ${data.userId} updated profile:`, data.changes);
});

// Type-safe emission
userEmitter.emit('login', {
  userId: 'user123',
  timestamp: new Date()
});

userEmitter.emit('profileUpdate', {
  userId: 'user123',
  changes: { name: 'John Doe' }
});
```

## Generic Type Inference

```typescript
// Type inference with generics
function toArray<T>(value: T): T[] {
  return [value];
}

const numArray = toArray(42);        // number[]
const strArray = toArray("hello");   // string[]

// Infer from multiple parameters
function map<T, U>(array: T[], fn: (item: T) => U): U[] {
  return array.map(fn);
}

const numbers = [1, 2, 3, 4, 5];
const strings = map(numbers, n => n.toString()); // string[]
const doubled = map(numbers, n => n * 2);        // number[]

// Real-world example: Type-safe API client
class TypeSafeAPI {
  async request<T, D = unknown>(
    endpoint: string,
    options?: {
      method?: 'GET' | 'POST' | 'PUT' | 'DELETE';
      body?: D;
      headers?: Record<string, string>;
    }
  ): Promise<T> {
    const response = await fetch(endpoint, {
      method: options?.method || 'GET',
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers
      },
      body: options?.body ? JSON.stringify(options.body) : undefined
    });
    
    return response.json();
  }
}

// Usage - types are inferred
const api = new TypeSafeAPI();

interface User {
  id: string;
  name: string;
}

interface CreateUserData {
  name: string;
  email: string;
}

// Return type is inferred as Promise<User>
const user = await api.request<User>('/api/users/1');

// Return type is inferred as Promise<User>
// Body type is CreateUserData
const newUser = await api.request<User, CreateUserData>('/api/users', {
  method: 'POST',
  body: { name: 'John', email: 'john@example.com' }
});
```

## Summary

| Pattern | Use Case | Example |
|---------|----------|---------|
| Generic Functions | Reusable type-safe functions | `identity<T>()` |
| Generic Classes | Type-safe data structures | `Stack<T>`, `Cache<K,V>` |
| Generic Constraints | Restrict generic types | `<T extends Entity>` |
| Utility Types | Transform types | `Partial<T>`, `Pick<T>` |
| Multiple Parameters | Complex type relationships | `<T, U, V>` |
| Type Inference | Automatic type detection | Infer from usage |

## Best Practices

1. **Use constraints** when generics need specific properties
2. **Name generics meaningfully**: `T` for Type, `K` for Key, `V` for Value, `E` for Element
3. **Avoid over-generification**: Don't make everything generic
4. **Leverage type inference**: Let TypeScript infer types when possible
5. **Use utility types**: Prefer built-in utilities like `Partial<T>`, `Pick<T>`
6. **Document generic parameters**: Explain what types are expected

## Next Steps

- Learn about conditional types
- Explore mapped types in depth
- Master advanced type manipulation
- Study decorators and metadata
