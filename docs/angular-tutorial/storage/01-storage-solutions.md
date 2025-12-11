# Browser Storage Solutions

> Timestamps: **8:50:03** - Session Storage | **9:00:45** - Local Storage | **9:07:26** - Cookies

## 1. Session Storage

**Lifetime**: Data persists only for the session (tab/window)
**Storage Limit**: ~5-10MB

```typescript
@Injectable({ providedIn: 'root' })
export class SessionStorageService {
  // Set item
  setItem(key: string, value: any): void {
    sessionStorage.setItem(key, JSON.stringify(value));
  }
  
  // Get item
  getItem<T>(key: string): T | null {
    const item = sessionStorage.getItem(key);
    return item ? JSON.parse(item) : null;
  }
  
  // Remove item
  removeItem(key: string): void {
    sessionStorage.removeItem(key);
  }
  
  // Clear all
  clear(): void {
    sessionStorage.clear();
  }
}

// Usage in component
@Component({
  selector: 'app-session-demo',
  standalone: true,
  template: `
    <input [(ngModel)]="username" placeholder="Username">
    <button (click)="save()">Save</button>
    <button (click)="load()">Load</button>
    <p>Loaded: {{ loadedUsername }}</p>
  `
})
export class SessionDemoComponent {
  private storage = inject(SessionStorageService);
  username = '';
  loadedUsername = '';
  
  save(): void {
    this.storage.setItem('username', this.username);
  }
  
  load(): void {
    this.loadedUsername = this.storage.getItem('username') || '';
  }
}
```

## 2. Local Storage

**Lifetime**: Data persists until explicitly deleted
**Storage Limit**: ~5-10MB

```typescript
@Injectable({ providedIn: 'root' })
export class LocalStorageService {
  // Set item
  setItem(key: string, value: any): void {
    localStorage.setItem(key, JSON.stringify(value));
  }
  
  // Get item
  getItem<T>(key: string): T | null {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) : null;
  }
  
  // Remove item
  removeItem(key: string): void {
    localStorage.removeItem(key);
  }
  
  // Clear all
  clear(): void {
    localStorage.clear();
  }
}

// Practical example: Theme preference
@Component({
  selector: 'app-theme-toggle',
  standalone: true,
  template: `
    <button (click)="toggleTheme()">
      Current: {{ theme }}
    </button>
  `
})
export class ThemeToggleComponent implements OnInit {
  private storage = inject(LocalStorageService);
  theme = 'light';
  
  ngOnInit(): void {
    this.theme = this.storage.getItem('theme') || 'light';
    this.applyTheme();
  }
  
  toggleTheme(): void {
    this.theme = this.theme === 'light' ? 'dark' : 'light';
    this.storage.setItem('theme', this.theme);
    this.applyTheme();
  }
  
  applyTheme(): void {
    document.body.className = this.theme;
  }
}
```

## 3. Cookies

**Lifetime**: Configurable expiration
**Storage Limit**: ~4KB per cookie
**Advantage**: Sent with HTTP requests

```typescript
@Injectable({ providedIn: 'root' })
export class CookieService {
  // Set cookie
  setCookie(name: string, value: string, days: number = 7): void {
    const date = new Date();
    date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
    const expires = `expires=${date.toUTCString()}`;
    document.cookie = `${name}=${value};${expires};path=/`;
  }
  
  // Get cookie
  getCookie(name: string): string | null {
    const nameEQ = name + "=";
    const ca = document.cookie.split(';');
    for(let i = 0; i < ca.length; i++) {
      let c = ca[i];
      while (c.charAt(0) == ' ') c = c.substring(1, c.length);
      if (c.indexOf(nameEQ) == 0) return c.substring(nameEQ.length, c.length);
    }
    return null;
  }
  
  // Delete cookie
  deleteCookie(name: string): void {
    this.setCookie(name, '', -1);
  }
}

// Usage
@Component({
  selector: 'app-cookie-demo',
  standalone: true,
  template: `
    <input [(ngModel)]="token" placeholder="Auth Token">
    <button (click)="saveToken()">Save</button>
    <button (click)="loadToken()">Load</button>
    <p>{{ loadedToken }}</p>
  `
})
export class CookieDemoComponent {
  private cookieService = inject(CookieService);
  token = '';
  loadedToken = '';
  
  saveToken(): void {
    this.cookieService.setCookie('authToken', this.token, 7);
  }
  
  loadToken(): void {
    this.loadedToken = this.cookieService.getCookie('authToken') || '';
  }
}
```

## Comparison

| Feature | Session Storage | Local Storage | Cookies |
|---------|----------------|---------------|---------|
| **Lifetime** | Session only | Permanent | Configurable |
| **Capacity** | ~5-10MB | ~5-10MB | ~4KB |
| **Scope** | Tab/Window | Browser | Browser |
| **HTTP** | Not sent | Not sent | Sent automatically |
| **Use Case** | Temporary data | User preferences | Authentication |

## Summary

✅ **Session Storage**: Temporary session data
✅ **Local Storage**: Persistent user preferences  
✅ **Cookies**: Authentication tokens, small data sent with requests

Choose based on data lifetime and size requirements!
