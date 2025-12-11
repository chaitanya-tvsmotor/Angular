# Practical TypeScript Code Examples

## Example 1: Data Validation System

```typescript
// Types and interfaces
interface ValidationRule<T> {
  validate(value: T): boolean;
  errorMessage: string;
}

class RequiredRule<T> implements ValidationRule<T> {
  errorMessage = "This field is required";
  
  validate(value: T): boolean {
    if (typeof value === 'string') {
      return value.trim().length > 0;
    }
    return value !== null && value !== undefined;
  }
}

class MinLengthRule implements ValidationRule<string> {
  errorMessage: string;
  
  constructor(private minLength: number) {
    this.errorMessage = `Minimum length is ${minLength}`;
  }
  
  validate(value: string): boolean {
    return value.length >= this.minLength;
  }
}

class EmailRule implements ValidationRule<string> {
  errorMessage = "Invalid email format";
  
  validate(value: string): boolean {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(value);
  }
}

class RangeRule implements ValidationRule<number> {
  errorMessage: string;
  
  constructor(private min: number, private max: number) {
    this.errorMessage = `Value must be between ${min} and ${max}`;
  }
  
  validate(value: number): boolean {
    return value >= this.min && value <= this.max;
  }
}

// Validator class
class Validator<T> {
  private rules: ValidationRule<T>[] = [];
  
  addRule(rule: ValidationRule<T>): this {
    this.rules.push(rule);
    return this;
  }
  
  validate(value: T): { valid: boolean; errors: string[] } {
    const errors: string[] = [];
    
    for (const rule of this.rules) {
      if (!rule.validate(value)) {
        errors.push(rule.errorMessage);
      }
    }
    
    return {
      valid: errors.length === 0,
      errors
    };
  }
}

// Usage
const usernameValidator = new Validator<string>()
  .addRule(new RequiredRule<string>())
  .addRule(new MinLengthRule(3));

const emailValidator = new Validator<string>()
  .addRule(new RequiredRule<string>())
  .addRule(new EmailRule());

const ageValidator = new Validator<number>()
  .addRule(new RequiredRule<number>())
  .addRule(new RangeRule(18, 100));

// Test validation
console.log(usernameValidator.validate("ab"));
// { valid: false, errors: ["Minimum length is 3"] }

console.log(emailValidator.validate("invalid-email"));
// { valid: false, errors: ["Invalid email format"] }

console.log(ageValidator.validate(25));
// { valid: true, errors: [] }
```

## Example 2: Event Emitter System

```typescript
type EventHandler<T> = (data: T) => void;

class EventEmitter<EventMap extends Record<string, any>> {
  private events: Map<keyof EventMap, EventHandler<any>[]> = new Map();
  
  on<K extends keyof EventMap>(
    event: K,
    handler: EventHandler<EventMap[K]>
  ): void {
    if (!this.events.has(event)) {
      this.events.set(event, []);
    }
    this.events.get(event)!.push(handler);
  }
  
  off<K extends keyof EventMap>(
    event: K,
    handler: EventHandler<EventMap[K]>
  ): void {
    const handlers = this.events.get(event);
    if (handlers) {
      const index = handlers.indexOf(handler);
      if (index !== -1) {
        handlers.splice(index, 1);
      }
    }
  }
  
  emit<K extends keyof EventMap>(event: K, data: EventMap[K]): void {
    const handlers = this.events.get(event);
    if (handlers) {
      handlers.forEach(handler => handler(data));
    }
  }
  
  once<K extends keyof EventMap>(
    event: K,
    handler: EventHandler<EventMap[K]>
  ): void {
    const onceHandler: EventHandler<EventMap[K]> = (data) => {
      handler(data);
      this.off(event, onceHandler);
    };
    this.on(event, onceHandler);
  }
}

// Usage
interface AppEvents {
  userLogin: { userId: string; timestamp: Date };
  userLogout: { userId: string };
  dataUpdate: { type: string; payload: any };
  error: { message: string; code: number };
}

const emitter = new EventEmitter<AppEvents>();

// Subscribe to events
emitter.on('userLogin', (data) => {
  console.log(`User ${data.userId} logged in at ${data.timestamp}`);
});

emitter.on('error', (data) => {
  console.error(`Error ${data.code}: ${data.message}`);
});

// Emit events
emitter.emit('userLogin', {
  userId: 'user123',
  timestamp: new Date()
});

emitter.emit('error', {
  message: 'Network timeout',
  code: 408
});
```

## Example 3: API Client with Retry Logic

```typescript
interface RequestConfig {
  method: 'GET' | 'POST' | 'PUT' | 'DELETE';
  url: string;
  headers?: Record<string, string>;
  body?: any;
  retries?: number;
  retryDelay?: number;
}

interface Response<T> {
  data: T;
  status: number;
  statusText: string;
  headers: Record<string, string>;
}

class ApiClient {
  private baseUrl: string;
  private defaultHeaders: Record<string, string>;
  
  constructor(baseUrl: string, defaultHeaders: Record<string, string> = {}) {
    this.baseUrl = baseUrl;
    this.defaultHeaders = {
      'Content-Type': 'application/json',
      ...defaultHeaders
    };
  }
  
  async request<T>(config: RequestConfig): Promise<Response<T>> {
    const maxRetries = config.retries ?? 0;
    const retryDelay = config.retryDelay ?? 1000;
    let lastError: Error | null = null;
    
    for (let attempt = 0; attempt <= maxRetries; attempt++) {
      try {
        const response = await this.makeRequest<T>(config);
        return response;
      } catch (error) {
        lastError = error as Error;
        
        if (attempt < maxRetries) {
          console.log(`Retry attempt ${attempt + 1} after ${retryDelay}ms`);
          await this.delay(retryDelay);
        }
      }
    }
    
    throw lastError;
  }
  
  private async makeRequest<T>(config: RequestConfig): Promise<Response<T>> {
    const url = `${this.baseUrl}${config.url}`;
    const headers = { ...this.defaultHeaders, ...config.headers };
    
    const response = await fetch(url, {
      method: config.method,
      headers,
      body: config.body ? JSON.stringify(config.body) : undefined
    });
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    const data = await response.json();
    
    return {
      data,
      status: response.status,
      statusText: response.statusText,
      headers: this.parseHeaders(response.headers)
    };
  }
  
  private parseHeaders(headers: Headers): Record<string, string> {
    const result: Record<string, string> = {};
    headers.forEach((value, key) => {
      result[key] = value;
    });
    return result;
  }
  
  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
  
  // Convenience methods
  get<T>(url: string, options?: Partial<RequestConfig>): Promise<Response<T>> {
    return this.request<T>({ method: 'GET', url, ...options });
  }
  
  post<T>(url: string, body: any, options?: Partial<RequestConfig>): Promise<Response<T>> {
    return this.request<T>({ method: 'POST', url, body, ...options });
  }
  
  put<T>(url: string, body: any, options?: Partial<RequestConfig>): Promise<Response<T>> {
    return this.request<T>({ method: 'PUT', url, body, ...options });
  }
  
  delete<T>(url: string, options?: Partial<RequestConfig>): Promise<Response<T>> {
    return this.request<T>({ method: 'DELETE', url, ...options });
  }
}

// Usage
const api = new ApiClient('https://api.example.com', {
  'Authorization': 'Bearer token123'
});

interface User {
  id: string;
  name: string;
  email: string;
}

async function fetchUser(id: string): Promise<User> {
  try {
    const response = await api.get<User>(`/users/${id}`, {
      retries: 3,
      retryDelay: 2000
    });
    return response.data;
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw error;
  }
}
```

## Example 4: State Management System

```typescript
type Listener<T> = (state: T) => void;

class Store<T> {
  private state: T;
  private listeners: Set<Listener<T>> = new Set();
  
  constructor(initialState: T) {
    this.state = initialState;
  }
  
  getState(): T {
    return { ...this.state } as T;
  }
  
  setState(updates: Partial<T>): void {
    this.state = { ...this.state, ...updates };
    this.notify();
  }
  
  subscribe(listener: Listener<T>): () => void {
    this.listeners.add(listener);
    
    // Return unsubscribe function
    return () => {
      this.listeners.delete(listener);
    };
  }
  
  private notify(): void {
    this.listeners.forEach(listener => {
      listener(this.getState());
    });
  }
}

// Usage
interface AppState {
  user: { id: string; name: string } | null;
  isLoading: boolean;
  error: string | null;
  theme: 'light' | 'dark';
}

const initialState: AppState = {
  user: null,
  isLoading: false,
  error: null,
  theme: 'light'
};

const store = new Store<AppState>(initialState);

// Subscribe to state changes
const unsubscribe = store.subscribe((state) => {
  console.log('State updated:', state);
  
  if (state.user) {
    console.log(`Welcome, ${state.user.name}!`);
  }
});

// Update state
store.setState({ isLoading: true });
store.setState({ 
  user: { id: '1', name: 'John' },
  isLoading: false
});
store.setState({ theme: 'dark' });

// Unsubscribe
unsubscribe();
```

## Example 5: Command Pattern Implementation

```typescript
interface Command {
  execute(): void;
  undo(): void;
}

class CommandHistory {
  private history: Command[] = [];
  private currentIndex = -1;
  
  execute(command: Command): void {
    // Remove any commands after current index
    this.history = this.history.slice(0, this.currentIndex + 1);
    
    command.execute();
    this.history.push(command);
    this.currentIndex++;
  }
  
  undo(): void {
    if (this.canUndo()) {
      const command = this.history[this.currentIndex];
      command.undo();
      this.currentIndex--;
    }
  }
  
  redo(): void {
    if (this.canRedo()) {
      this.currentIndex++;
      const command = this.history[this.currentIndex];
      command.execute();
    }
  }
  
  canUndo(): boolean {
    return this.currentIndex >= 0;
  }
  
  canRedo(): boolean {
    return this.currentIndex < this.history.length - 1;
  }
}

// Example: Text Editor Commands
class TextEditor {
  private content: string = '';
  
  getContent(): string {
    return this.content;
  }
  
  setContent(content: string): void {
    this.content = content;
  }
  
  insert(text: string, position: number): void {
    this.content = 
      this.content.slice(0, position) + 
      text + 
      this.content.slice(position);
  }
  
  delete(start: number, length: number): string {
    const deleted = this.content.slice(start, start + length);
    this.content = 
      this.content.slice(0, start) + 
      this.content.slice(start + length);
    return deleted;
  }
}

class InsertTextCommand implements Command {
  private editor: TextEditor;
  private text: string;
  private position: number;
  
  constructor(editor: TextEditor, text: string, position: number) {
    this.editor = editor;
    this.text = text;
    this.position = position;
  }
  
  execute(): void {
    this.editor.insert(this.text, this.position);
  }
  
  undo(): void {
    this.editor.delete(this.position, this.text.length);
  }
}

class DeleteTextCommand implements Command {
  private editor: TextEditor;
  private start: number;
  private length: number;
  private deletedText: string = '';
  
  constructor(editor: TextEditor, start: number, length: number) {
    this.editor = editor;
    this.start = start;
    this.length = length;
  }
  
  execute(): void {
    this.deletedText = this.editor.delete(this.start, this.length);
  }
  
  undo(): void {
    this.editor.insert(this.deletedText, this.start);
  }
}

// Usage
const editor = new TextEditor();
const history = new CommandHistory();

// Type "Hello"
history.execute(new InsertTextCommand(editor, 'Hello', 0));
console.log(editor.getContent()); // "Hello"

// Type " World"
history.execute(new InsertTextCommand(editor, ' World', 5));
console.log(editor.getContent()); // "Hello World"

// Delete "World"
history.execute(new DeleteTextCommand(editor, 6, 5));
console.log(editor.getContent()); // "Hello "

// Undo delete
history.undo();
console.log(editor.getContent()); // "Hello World"

// Redo delete
history.redo();
console.log(editor.getContent()); // "Hello "
```

## Example 6: Debounce and Throttle Utilities

```typescript
type DebounceFunction<T extends any[]> = (...args: T) => void;

function debounce<T extends any[]>(
  func: (...args: T) => void,
  delay: number
): DebounceFunction<T> {
  let timeoutId: NodeJS.Timeout | null = null;
  
  return function(...args: T): void {
    if (timeoutId) {
      clearTimeout(timeoutId);
    }
    
    timeoutId = setTimeout(() => {
      func(...args);
      timeoutId = null;
    }, delay);
  };
}

function throttle<T extends any[]>(
  func: (...args: T) => void,
  limit: number
): DebounceFunction<T> {
  let inThrottle: boolean = false;
  
  return function(...args: T): void {
    if (!inThrottle) {
      func(...args);
      inThrottle = true;
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}

// Usage
const searchAPI = (term: string) => {
  console.log(`Searching for: ${term}`);
  // API call here
};

const debouncedSearch = debounce(searchAPI, 300);

// Only the last call will execute after 300ms
debouncedSearch('a');
debouncedSearch('ab');
debouncedSearch('abc'); // Only this will execute

const handleScroll = () => {
  console.log('Scroll event');
  // Update UI
};

const throttledScroll = throttle(handleScroll, 100);

// Will execute at most once per 100ms
window.addEventListener('scroll', throttledScroll);
```

## Example 7: Type-Safe Builder Pattern

```typescript
class QueryBuilder {
  private query: {
    select: string[];
    from: string;
    where: string[];
    orderBy: string[];
    limit?: number;
  } = {
    select: [],
    from: '',
    where: [],
    orderBy: []
  };
  
  select(...columns: string[]): this {
    this.query.select.push(...columns);
    return this;
  }
  
  from(table: string): this {
    this.query.from = table;
    return this;
  }
  
  where(condition: string): this {
    this.query.where.push(condition);
    return this;
  }
  
  orderBy(column: string, direction: 'ASC' | 'DESC' = 'ASC'): this {
    this.query.orderBy.push(`${column} ${direction}`);
    return this;
  }
  
  limit(count: number): this {
    this.query.limit = count;
    return this;
  }
  
  build(): string {
    const select = this.query.select.length > 0 
      ? this.query.select.join(', ')
      : '*';
    
    let sql = `SELECT ${select} FROM ${this.query.from}`;
    
    if (this.query.where.length > 0) {
      sql += ` WHERE ${this.query.where.join(' AND ')}`;
    }
    
    if (this.query.orderBy.length > 0) {
      sql += ` ORDER BY ${this.query.orderBy.join(', ')}`;
    }
    
    if (this.query.limit) {
      sql += ` LIMIT ${this.query.limit}`;
    }
    
    return sql;
  }
}

// Usage
const query = new QueryBuilder()
  .select('id', 'name', 'email')
  .from('users')
  .where('age > 18')
  .where('status = "active"')
  .orderBy('name', 'ASC')
  .limit(10)
  .build();

console.log(query);
// SELECT id, name, email FROM users WHERE age > 18 AND status = "active" ORDER BY name ASC LIMIT 10
```

## Example 8: Memoization Cache

```typescript
type CacheKey = string | number;

class MemoizationCache<T> {
  private cache: Map<CacheKey, T> = new Map();
  private maxSize: number;
  
  constructor(maxSize: number = 100) {
    this.maxSize = maxSize;
  }
  
  get(key: CacheKey): T | undefined {
    return this.cache.get(key);
  }
  
  set(key: CacheKey, value: T): void {
    if (this.cache.size >= this.maxSize && !this.cache.has(key)) {
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    this.cache.set(key, value);
  }
  
  has(key: CacheKey): boolean {
    return this.cache.has(key);
  }
  
  clear(): void {
    this.cache.clear();
  }
}

function memoize<T extends any[], R>(
  fn: (...args: T) => R,
  keyGenerator?: (...args: T) => CacheKey
): (...args: T) => R {
  const cache = new MemoizationCache<R>();
  
  return function(...args: T): R {
    const key = keyGenerator 
      ? keyGenerator(...args)
      : JSON.stringify(args);
    
    if (cache.has(key)) {
      console.log('Cache hit for:', key);
      return cache.get(key)!;
    }
    
    console.log('Cache miss for:', key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

// Usage
const fibonacci = memoize((n: number): number => {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(10)); // Calculates
console.log(fibonacci(10)); // Cache hit
console.log(fibonacci(11)); // Reuses cached values
```

These examples demonstrate real-world patterns and techniques that are commonly used in TypeScript applications. They show how to build reusable, type-safe utilities and implement common design patterns.
