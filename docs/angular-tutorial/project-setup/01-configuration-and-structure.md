# Angular Project Configuration & Directory Structure

## Creating a New Angular Project

### Using Angular CLI

```bash
# Install Angular CLI globally
npm install -g @angular/cli

# Create a new project
ng new my-angular-app

# Options during creation:
# - Would you like to add Angular routing? (y/N) - Select Y
# - Which stylesheet format would you like to use? - Select CSS/SCSS/SASS

# Navigate to project
cd my-angular-app

# Start development server
ng serve --open
```

## Project Directory Structure

```
my-angular-app/
├── .angular/                 # Angular build cache
├── .vscode/                  # VS Code settings
├── node_modules/             # Dependencies
├── src/                      # Source code
│   ├── app/                  # Application code
│   │   ├── app.component.ts      # Root component
│   │   ├── app.component.html    # Root template
│   │   ├── app.component.css     # Root styles
│   │   ├── app.component.spec.ts # Unit tests
│   │   ├── app.config.ts         # App configuration
│   │   └── app.routes.ts         # Route definitions
│   ├── assets/               # Static files (images, fonts)
│   ├── index.html           # Main HTML file
│   ├── main.ts              # Bootstrap file
│   └── styles.css           # Global styles
├── angular.json             # Angular CLI configuration
├── package.json             # npm dependencies
├── tsconfig.json           # TypeScript configuration
├── tsconfig.app.json       # App-specific TS config
└── README.md               # Project documentation
```

## Key Configuration Files

### 1. angular.json

This is the main configuration file for Angular CLI projects.

```json
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "my-angular-app": {
      "projectType": "application",
      "root": "",
      "sourceRoot": "src",
      "prefix": "app",
      "architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:application",
          "options": {
            "outputPath": "dist/my-angular-app",
            "index": "src/index.html",
            "browser": "src/main.ts",
            "polyfills": ["zone.js"],
            "tsConfig": "tsconfig.app.json",
            "assets": ["src/favicon.ico", "src/assets"],
            "styles": ["src/styles.css"],
            "scripts": []
          }
        },
        "serve": {
          "builder": "@angular-devkit/build-angular:dev-server",
          "options": {
            "port": 4200,
            "open": true
          }
        }
      }
    }
  }
}
```

**Key Properties:**
- `outputPath`: Where compiled files go
- `index`: Main HTML file
- `browser`: Application entry point
- `assets`: Static files to copy
- `styles`: Global stylesheets
- `port`: Development server port

### 2. tsconfig.json

TypeScript compiler configuration.

```json
{
  "compileOnSave": false,
  "compilerOptions": {
    "outDir": "./dist/out-tsc",
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "sourceMap": true,
    "declaration": false,
    "experimentalDecorators": true,
    "moduleResolution": "node",
    "importHelpers": true,
    "target": "ES2022",
    "module": "ES2022",
    "useDefineForClassFields": false,
    "lib": ["ES2022", "dom"]
  },
  "angularCompilerOptions": {
    "enableI18nLegacyMessageIdFormat": false,
    "strictInjectionParameters": true,
    "strictInputAccessModifiers": true,
    "strictTemplates": true
  }
}
```

**Important Options:**
- `strict`: Enable all strict type checking
- `target`: JavaScript version to compile to
- `experimentalDecorators`: Required for Angular decorators
- `strictTemplates`: Type checking in templates

### 3. package.json

Project dependencies and scripts.

```json
{
  "name": "my-angular-app",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test",
    "serve:ssr": "node dist/my-angular-app/server/server.mjs"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "^18.0.0",
    "@angular/common": "^18.0.0",
    "@angular/compiler": "^18.0.0",
    "@angular/core": "^18.0.0",
    "@angular/forms": "^18.0.0",
    "@angular/platform-browser": "^18.0.0",
    "@angular/platform-browser-dynamic": "^18.0.0",
    "@angular/router": "^18.0.0",
    "rxjs": "~7.8.0",
    "tslib": "^2.3.0",
    "zone.js": "~0.14.0"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "^18.0.0",
    "@angular/cli": "^18.0.0",
    "@angular/compiler-cli": "^18.0.0",
    "typescript": "~5.4.0"
  }
}
```

## App Directory Structure

### Organizing Your Application

```
src/app/
├── core/                    # Singleton services, guards, interceptors
│   ├── services/
│   ├── guards/
│   ├── interceptors/
│   └── models/
├── shared/                  # Shared components, directives, pipes
│   ├── components/
│   ├── directives/
│   └── pipes/
├── features/                # Feature modules
│   ├── home/
│   ├── products/
│   └── users/
├── app.component.ts        # Root component
├── app.config.ts           # Application configuration
└── app.routes.ts           # Route configuration
```

## Entry Point Files

### main.ts

Bootstrap file that starts the application.

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { appConfig } from './app/app.config';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, appConfig)
  .catch((err) => console.error(err));
```

### app.config.ts

Application configuration with providers.

```typescript
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient()
  ]
};
```

### index.html

Main HTML file.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MyAngularApp</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

## Environment Configuration

### Creating Environment Files

```bash
# Create environments folder
mkdir src/environments

# Create environment files
touch src/environments/environment.ts
touch src/environments/environment.development.ts
```

### environment.ts (Production)

```typescript
export const environment = {
  production: true,
  apiUrl: 'https://api.production.com',
  apiKey: 'prod-api-key'
};
```

### environment.development.ts (Development)

```typescript
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000',
  apiKey: 'dev-api-key'
};
```

### Using Environment Variables

```typescript
import { environment } from '../environments/environment';

export class ApiService {
  private apiUrl = environment.apiUrl;
  
  getData() {
    return this.http.get(`${this.apiUrl}/data`);
  }
}
```

## Angular CLI Commands

### Project Management

```bash
# Create new project
ng new project-name

# Serve application
ng serve
ng serve --port 4300
ng serve --open

# Build for production
ng build
ng build --configuration production

# Run tests
ng test
ng test --code-coverage

# Run e2e tests
ng e2e

# Lint code
ng lint
```

### Code Generation

```bash
# Generate component
ng generate component component-name
ng g c component-name

# Generate service
ng generate service service-name
ng g s service-name

# Generate module
ng generate module module-name
ng g m module-name

# Generate directive
ng generate directive directive-name
ng g d directive-name

# Generate pipe
ng generate pipe pipe-name
ng g p pipe-name

# Generate guard
ng generate guard guard-name
ng g g guard-name

# Generate interface
ng generate interface interface-name
ng g i interface-name

# Generate class
ng generate class class-name
ng g cl class-name
```

## Project Structure Best Practices

### 1. Feature-Based Organization

```
src/app/
├── features/
│   ├── products/
│   │   ├── components/
│   │   ├── services/
│   │   ├── models/
│   │   └── products.routes.ts
│   └── users/
│       ├── components/
│       ├── services/
│       ├── models/
│       └── users.routes.ts
```

### 2. Core Module Pattern

```
src/app/core/
├── services/
│   ├── auth.service.ts
│   └── api.service.ts
├── guards/
│   └── auth.guard.ts
├── interceptors/
│   └── auth.interceptor.ts
└── models/
    └── user.model.ts
```

### 3. Shared Module Pattern

```
src/app/shared/
├── components/
│   ├── header/
│   ├── footer/
│   └── loading-spinner/
├── directives/
│   └── highlight.directive.ts
└── pipes/
    └── truncate.pipe.ts
```

## Practical Exercise

### Create a New Angular Project

1. **Create Project**
```bash
ng new my-first-app --routing --style=css
cd my-first-app
```

2. **Explore Structure**
```bash
# List all files
ls -la

# View angular.json
cat angular.json

# View package.json
cat package.json
```

3. **Start Development Server**
```bash
ng serve --open
```

4. **Generate Components**
```bash
ng g c components/header
ng g c components/footer
ng g c pages/home
```

5. **Create Feature Module**
```bash
ng g m features/products --routing
ng g c features/products/product-list
ng g c features/products/product-detail
```

## Summary

| Configuration File | Purpose |
|-------------------|---------|
| `angular.json` | Angular CLI configuration |
| `tsconfig.json` | TypeScript compiler settings |
| `package.json` | Dependencies and scripts |
| `main.ts` | Application bootstrap |
| `app.config.ts` | App-level providers |
| `index.html` | Main HTML template |

## Next Steps

- Understand standalone components
- Learn about data binding
- Explore directives
- Master routing concepts
