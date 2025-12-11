# TypeScript Functions

## Function Type Annotations

### Basic Function Syntax

```typescript
// Named function with type annotations
function add(x: number, y: number): number {
  return x + y;
}

// Function expression
const subtract = function(x: number, y: number): number {
  return x - y;
};

// Arrow function
const multiply = (x: number, y: number): number => {
  return x * y;
};

// Concise arrow function
const divide = (x: number, y: number): number => x / y;
```

### Function Type

```typescript
// Define a function type
type MathOperation = (a: number, b: number) => number;

const add: MathOperation = (a, b) => a + b;
const subtract: MathOperation = (a, b) => a - b;

// Real-world example: Calculator
interface Calculator {
  add: MathOperation;
  subtract: MathOperation;
  multiply: MathOperation;
  divide: MathOperation;
}

const calculator: Calculator = {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b,
  multiply: (a, b) => a * b,
  divide: (a, b) => a / b
};

console.log(calculator.add(10, 5));      // 15
console.log(calculator.multiply(10, 5)); // 50
```

## Optional Parameters

```typescript
// Optional parameter with ?
function greet(name: string, greeting?: string): string {
  if (greeting) {
    return `${greeting}, ${name}!`;
  }
  return `Hello, ${name}!`;
}

console.log(greet("Alice"));              // Hello, Alice!
console.log(greet("Alice", "Good morning")); // Good morning, Alice!

// Real-world example: User registration
interface RegistrationData {
  username: string;
  email: string;
  phoneNumber?: string; // Optional
}

function registerUser(data: RegistrationData, referralCode?: string): void {
  console.log(`Registering user: ${data.username}`);
  console.log(`Email: ${data.email}`);
  
  if (data.phoneNumber) {
    console.log(`Phone: ${data.phoneNumber}`);
  }
  
  if (referralCode) {
    console.log(`Referral code applied: ${referralCode}`);
  }
}

registerUser({
  username: "johndoe",
  email: "john@example.com"
});
```

## Default Parameters

```typescript
// Default parameter values
function createUser(name: string, role: string = "user", isActive: boolean = true): void {
  console.log(`Creating ${role}: ${name}, Active: ${isActive}`);
}

createUser("Alice");                    // Creating user: Alice, Active: true
createUser("Bob", "admin");             // Creating admin: Bob, Active: true
createUser("Charlie", "moderator", false); // Creating moderator: Charlie, Active: false

// Real-world example: Pagination
interface PaginationOptions {
  page?: number;
  limit?: number;
  sortBy?: string;
  order?: "asc" | "desc";
}

function fetchUsers(options: PaginationOptions = {}): void {
  const page = options.page ?? 1;
  const limit = options.limit ?? 10;
  const sortBy = options.sortBy ?? "createdAt";
  const order = options.order ?? "desc";
  
  console.log(`Fetching page ${page}, showing ${limit} users`);
  console.log(`Sorted by ${sortBy} in ${order} order`);
}

fetchUsers();                             // Uses all defaults
fetchUsers({ page: 2, limit: 20 });       // Custom pagination
```

## Rest Parameters

```typescript
// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3));          // 6
console.log(sum(1, 2, 3, 4, 5));    // 15

// Real-world example: Logging utility
enum LogLevel {
  Info = "INFO",
  Warning = "WARNING",
  Error = "ERROR"
}

function log(level: LogLevel, message: string, ...metadata: any[]): void {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${level}: ${message}`);
  
  if (metadata.length > 0) {
    console.log("Metadata:", metadata);
  }
}

log(LogLevel.Info, "User logged in", { userId: 123, username: "john" });
log(LogLevel.Error, "Database connection failed", "timeout", { host: "localhost" });
```

## Function Overloads

Multiple function signatures for the same function.

```typescript
// Function overload signatures
function getValue(id: number): string;
function getValue(name: string): number;
function getValue(value: number | string): string | number {
  if (typeof value === "number") {
    return `ID: ${value}`;
  } else {
    return value.length;
  }
}

const result1 = getValue(123);      // string
const result2 = getValue("John");   // number

// Real-world example: Database query
interface User {
  id: number;
  username: string;
  email: string;
}

// Overload signatures
function findUser(id: number): User | undefined;
function findUser(username: string): User | undefined;
function findUser(criteria: { email: string }): User | undefined;

// Implementation
function findUser(search: number | string | { email: string }): User | undefined {
  // Mock database
  const users: User[] = [
    { id: 1, username: "john", email: "john@example.com" },
    { id: 2, username: "jane", email: "jane@example.com" }
  ];
  
  if (typeof search === "number") {
    return users.find(u => u.id === search);
  } else if (typeof search === "string") {
    return users.find(u => u.username === search);
  } else {
    return users.find(u => u.email === search.email);
  }
}

// Usage
const user1 = findUser(1);                          // Find by ID
const user2 = findUser("john");                     // Find by username
const user3 = findUser({ email: "jane@example.com" }); // Find by email
```

## Void and Never Return Types

```typescript
// Void - no return value
function logMessage(message: string): void {
  console.log(message);
  // No return statement needed
}

// Never - function never returns
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {
    // Loop forever
  }
}

// Real-world example: Error handling
class ValidationError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "ValidationError";
  }
}

function assertNever(x: never): never {
  throw new Error("Unexpected value: " + x);
}

type Status = "pending" | "approved" | "rejected";

function handleStatus(status: Status): string {
  switch (status) {
    case "pending":
      return "Processing...";
    case "approved":
      return "Approved!";
    case "rejected":
      return "Rejected.";
    default:
      return assertNever(status); // Ensures all cases are handled
  }
}
```

## Callback Functions

```typescript
// Callback function type
type Callback = (result: string) => void;
type ErrorCallback = (error: Error) => void;

function fetchData(url: string, onSuccess: Callback, onError: ErrorCallback): void {
  // Simulate async operation
  setTimeout(() => {
    if (url) {
      onSuccess(`Data from ${url}`);
    } else {
      onError(new Error("Invalid URL"));
    }
  }, 1000);
}

// Usage
fetchData(
  "https://api.example.com/users",
  (data) => console.log("Success:", data),
  (error) => console.error("Error:", error.message)
);

// Real-world example: Event handlers
interface MouseEvent {
  x: number;
  y: number;
  button: "left" | "right" | "middle";
}

type EventHandler<T> = (event: T) => void;

class Button {
  private clickHandlers: EventHandler<MouseEvent>[] = [];
  
  onClick(handler: EventHandler<MouseEvent>): void {
    this.clickHandlers.push(handler);
  }
  
  click(x: number, y: number): void {
    const event: MouseEvent = { x, y, button: "left" };
    this.clickHandlers.forEach(handler => handler(event));
  }
}

const button = new Button();
button.onClick((event) => {
  console.log(`Button clicked at (${event.x}, ${event.y})`);
});
button.click(100, 200);
```

## Higher-Order Functions

Functions that take functions as arguments or return functions.

```typescript
// Function that returns a function
function multiplier(factor: number): (x: number) => number {
  return (x: number) => x * factor;
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Real-world example: Middleware pattern
type RequestHandler = (request: any) => any;

function createLogger(prefix: string): RequestHandler {
  return (request: any) => {
    console.log(`${prefix}:`, request);
    return request;
  };
}

function createValidator(rules: any): RequestHandler {
  return (request: any) => {
    // Validation logic
    console.log("Validating request:", request);
    return request;
  };
}

// Compose middlewares
function compose(...handlers: RequestHandler[]): RequestHandler {
  return (request: any) => {
    return handlers.reduce((req, handler) => handler(req), request);
  };
}

const middleware = compose(
  createLogger("[API]"),
  createValidator({ required: ["username", "email"] })
);

middleware({ username: "john", email: "john@example.com" });
```

## Generic Functions

Functions that work with multiple types.

```typescript
// Generic function
function identity<T>(arg: T): T {
  return arg;
}

const num = identity<number>(42);
const str = identity<string>("Hello");
const bool = identity(true); // Type inference

// Generic with constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "John", age: 30 };
const name = getProperty(person, "name"); // string
const age = getProperty(person, "age");   // number

// Real-world example: API response handler
interface ApiResponse<T> {
  success: boolean;
  data: T | null;
  error: string | null;
}

function handleApiResponse<T>(response: ApiResponse<T>): T {
  if (response.success && response.data) {
    return response.data;
  }
  throw new Error(response.error || "Unknown error");
}

// Usage with different types
interface User {
  id: number;
  username: string;
}

interface Product {
  id: number;
  name: string;
  price: number;
}

const userResponse: ApiResponse<User> = {
  success: true,
  data: { id: 1, username: "john" },
  error: null
};

const productResponse: ApiResponse<Product> = {
  success: true,
  data: { id: 1, name: "Laptop", price: 999 },
  error: null
};

const user = handleApiResponse(userResponse);     // User type
const product = handleApiResponse(productResponse); // Product type
```

## Async Functions

```typescript
// Async function with Promise
async function fetchUser(id: number): Promise<User> {
  // Simulate API call
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ id, username: `user${id}`, email: `user${id}@example.com` });
    }, 1000);
  });
}

// Using async/await
async function getUserInfo(id: number): Promise<void> {
  try {
    const user = await fetchUser(id);
    console.log("User:", user);
  } catch (error) {
    console.error("Error fetching user:", error);
  }
}

// Real-world example: Multiple async operations
interface Post {
  id: number;
  userId: number;
  title: string;
  content: string;
}

interface Comment {
  id: number;
  postId: number;
  text: string;
}

async function fetchPost(id: number): Promise<Post> {
  // Simulate API call
  return { id, userId: 1, title: "Sample Post", content: "Content..." };
}

async function fetchComments(postId: number): Promise<Comment[]> {
  // Simulate API call
  return [
    { id: 1, postId, text: "Great post!" },
    { id: 2, postId, text: "Thanks for sharing!" }
  ];
}

async function getPostWithComments(postId: number): Promise<{
  post: Post;
  comments: Comment[];
}> {
  // Parallel execution
  const [post, comments] = await Promise.all([
    fetchPost(postId),
    fetchComments(postId)
  ]);
  
  return { post, comments };
}

// Usage
getPostWithComments(1).then(result => {
  console.log("Post:", result.post);
  console.log("Comments:", result.comments);
});
```

## Real-World Example: Authentication Service

```typescript
interface Credentials {
  username: string;
  password: string;
}

interface AuthToken {
  token: string;
  expiresAt: Date;
}

interface AuthUser {
  id: number;
  username: string;
  roles: string[];
}

class AuthenticationService {
  private tokens: Map<string, AuthUser> = new Map();
  
  // Login with credentials
  async login(credentials: Credentials): Promise<AuthToken> {
    // Validate credentials
    if (!this.validateCredentials(credentials)) {
      throw new Error("Invalid credentials");
    }
    
    // Generate token
    const token = this.generateToken();
    const expiresAt = new Date(Date.now() + 3600000); // 1 hour
    
    // Store user session
    this.tokens.set(token, {
      id: 1,
      username: credentials.username,
      roles: ["user"]
    });
    
    return { token, expiresAt };
  }
  
  // Verify token and get user
  async verifyToken(token: string): Promise<AuthUser | null> {
    return this.tokens.get(token) || null;
  }
  
  // Logout
  async logout(token: string): Promise<void> {
    this.tokens.delete(token);
  }
  
  // Check permission
  hasPermission(user: AuthUser, permission: string): boolean {
    return user.roles.includes("admin") || user.roles.includes(permission);
  }
  
  private validateCredentials(credentials: Credentials): boolean {
    // Simplified validation
    return credentials.username.length > 0 && credentials.password.length >= 6;
  }
  
  private generateToken(): string {
    return Math.random().toString(36).substring(2) + Date.now().toString(36);
  }
}

// Usage
const authService = new AuthenticationService();

async function authenticate() {
  try {
    // Login
    const authToken = await authService.login({
      username: "john",
      password: "password123"
    });
    console.log("Login successful:", authToken);
    
    // Verify token
    const user = await authService.verifyToken(authToken.token);
    if (user) {
      console.log("User authenticated:", user);
      
      // Check permissions
      const canEdit = authService.hasPermission(user, "edit");
      console.log("Can edit:", canEdit);
    }
    
    // Logout
    await authService.logout(authToken.token);
    console.log("Logged out");
    
  } catch (error) {
    console.error("Authentication error:", error);
  }
}
```

## Summary

| Concept | Description | Use Case |
|---------|-------------|----------|
| Type Annotations | Specify parameter and return types | Type safety |
| Optional Parameters | Parameters that may be omitted | Flexible APIs |
| Default Parameters | Default values for parameters | Sensible defaults |
| Rest Parameters | Variable number of arguments | Variadic functions |
| Overloads | Multiple signatures | Flexible function calls |
| Callbacks | Functions passed as arguments | Event handlers |
| Higher-Order Functions | Functions that return functions | Middleware, composition |
| Generics | Type-safe reusable functions | Generic utilities |
| Async/Await | Handle asynchronous operations | API calls, I/O |

## Next Steps

- Learn about interfaces and classes
- Understand advanced type patterns
- Explore decorators and metadata
- Master generic constraints and utility types
