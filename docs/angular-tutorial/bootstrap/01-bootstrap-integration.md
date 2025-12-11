# Bootstrap Installation in Angular

> Timestamp: **53:40** - Bootstrap Installation

## Installation

```bash
npm install bootstrap
```

## Method 1: angular.json (Recommended)

```json
{
  "projects": {
    "your-app": {
      "architect": {
        "build": {
          "options": {
            "styles": [
              "node_modules/bootstrap/dist/css/bootstrap.min.css",
              "src/styles.css"
            ],
            "scripts": [
              "node_modules/bootstrap/dist/js/bootstrap.bundle.min.js"
            ]
          }
        }
      }
    }
  }
}
```

## Method 2: styles.css Import

```css
/* styles.css */
@import '~bootstrap/dist/css/bootstrap.min.css';
```

## Usage Example

```typescript
@Component({
  selector: 'app-bootstrap-demo',
  standalone: true,
  template: `
    <div class="container mt-4">
      <div class="row">
        <div class="col-md-6">
          <div class="card">
            <div class="card-header">
              Bootstrap Card
            </div>
            <div class="card-body">
              <h5 class="card-title">Card Title</h5>
              <p class="card-text">This is a Bootstrap card component.</p>
              <button class="btn btn-primary">Click Me</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  `
})
export class BootstrapDemoComponent { }
```

## Common Components

### Buttons
```html
<button class="btn btn-primary">Primary</button>
<button class="btn btn-secondary">Secondary</button>
<button class="btn btn-success">Success</button>
<button class="btn btn-danger">Danger</button>
```

### Forms
```html
<form>
  <div class="mb-3">
    <label class="form-label">Email</label>
    <input type="email" class="form-control">
  </div>
  <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

### Grid System
```html
<div class="container">
  <div class="row">
    <div class="col-md-4">Column 1</div>
    <div class="col-md-4">Column 2</div>
    <div class="col-md-4">Column 3</div>
  </div>
</div>
```

## Summary

✅ Install via npm
✅ Import in angular.json or styles.css
✅ Use Bootstrap classes in templates
✅ Responsive grid system included

Bootstrap provides ready-to-use UI components!
