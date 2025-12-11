# Attribute Directives in Angular

## What are Attribute Directives?

Attribute directives change the appearance or behavior of elements, components, or other directives. Unlike structural directives, they **don't change the DOM structure**.

## Built-in Attribute Directives

### 1. ngClass - Dynamic Classes

Dynamically add or remove CSS classes based on conditions.

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-ngclass-demo',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="container">
      <h2>ngClass Examples</h2>
      
      <!-- String syntax -->
      <div [ngClass]="'box active'">String syntax</div>
      
      <!-- Array syntax -->
      <div [ngClass]="['box', 'highlighted', 'large']">Array syntax</div>
      
      <!-- Object syntax (most common) -->
      <div [ngClass]="{
        'box': true,
        'active': isActive,
        'disabled': isDisabled,
        'highlighted': isHighlighted
      }">
        Object syntax
      </div>
      
      <!-- Dynamic class from component property -->
      <div [ngClass]="dynamicClasses">Dynamic from property</div>
      
      <!-- Conditional classes -->
      <div [ngClass]="{
        'success': status === 'success',
        'error': status === 'error',
        'warning': status === 'warning'
      }">
        Status: {{ status }}
      </div>
      
      <!-- Controls -->
      <div class="controls">
        <button (click)="isActive = !isActive">Toggle Active</button>
        <button (click)="isDisabled = !isDisabled">Toggle Disabled</button>
        <button (click)="isHighlighted = !isHighlighted">Toggle Highlight</button>
      </div>
      
      <div class="controls">
        <button (click)="status = 'success'">Success</button>
        <button (click)="status = 'error'">Error</button>
        <button (click)="status = 'warning'">Warning</button>
      </div>
      
      <!-- Real-world example: Card component -->
      <div class="card-demo">
        <div class="card" [ngClass]="{
          'card-primary': cardType === 'primary',
          'card-secondary': cardType === 'secondary',
          'card-danger': cardType === 'danger',
          'card-large': cardSize === 'large',
          'card-small': cardSize === 'small',
          'card-elevated': isElevated
        }">
          <h3>Card Title</h3>
          <p>This is a dynamic card with multiple classes</p>
        </div>
        
        <div class="controls">
          <select [(ngModel)]="cardType">
            <option value="primary">Primary</option>
            <option value="secondary">Secondary</option>
            <option value="danger">Danger</option>
          </select>
          <select [(ngModel)]="cardSize">
            <option value="small">Small</option>
            <option value="medium">Medium</option>
            <option value="large">Large</option>
          </select>
          <label>
            <input type="checkbox" [(ngModel)]="isElevated">
            Elevated
          </label>
        </div>
      </div>
      
      <!-- Button states example -->
      <div class="button-demo">
        <h3>Button States</h3>
        <button [ngClass]="{
          'btn': true,
          'btn-primary': buttonState === 'primary',
          'btn-success': buttonState === 'success',
          'btn-danger': buttonState === 'danger',
          'btn-loading': isLoading,
          'btn-disabled': isButtonDisabled
        }"
        [disabled]="isButtonDisabled || isLoading">
          {{ isLoading ? 'Loading...' : 'Click Me' }}
        </button>
        
        <div class="controls">
          <button (click)="buttonState = 'primary'">Primary</button>
          <button (click)="buttonState = 'success'">Success</button>
          <button (click)="buttonState = 'danger'">Danger</button>
          <button (click)="isLoading = !isLoading">Toggle Loading</button>
          <button (click)="isButtonDisabled = !isButtonDisabled">Toggle Disabled</button>
        </div>
      </div>
    </div>
  `,
  styles: [`
    .container {
      padding: 20px;
      max-width: 800px;
      margin: 0 auto;
    }
    
    .box {
      padding: 20px;
      margin: 10px 0;
      border: 2px solid #ddd;
      border-radius: 4px;
    }
    
    .active {
      background: #007bff;
      color: white;
      border-color: #0056b3;
    }
    
    .disabled {
      opacity: 0.5;
      background: #e9ecef;
    }
    
    .highlighted {
      background: #fff3cd;
      border-color: #ffc107;
    }
    
    .large {
      font-size: 1.5em;
      padding: 30px;
    }
    
    .success {
      background: #d4edda;
      border-color: #28a745;
      color: #155724;
    }
    
    .error {
      background: #f8d7da;
      border-color: #dc3545;
      color: #721c24;
    }
    
    .warning {
      background: #fff3cd;
      border-color: #ffc107;
      color: #856404;
    }
    
    .controls {
      margin: 15px 0;
    }
    
    button {
      padding: 8px 16px;
      margin: 5px;
      border: none;
      border-radius: 4px;
      background: #007bff;
      color: white;
      cursor: pointer;
    }
    
    .card {
      padding: 20px;
      margin: 20px 0;
      border-radius: 8px;
      transition: all 0.3s;
    }
    
    .card-primary {
      background: #007bff;
      color: white;
    }
    
    .card-secondary {
      background: #6c757d;
      color: white;
    }
    
    .card-danger {
      background: #dc3545;
      color: white;
    }
    
    .card-small {
      padding: 10px;
      font-size: 0.9em;
    }
    
    .card-large {
      padding: 30px;
      font-size: 1.2em;
    }
    
    .card-elevated {
      box-shadow: 0 4px 8px rgba(0,0,0,0.2);
      transform: translateY(-2px);
    }
    
    .btn {
      padding: 10px 20px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      transition: all 0.3s;
    }
    
    .btn-primary {
      background: #007bff;
      color: white;
    }
    
    .btn-success {
      background: #28a745;
      color: white;
    }
    
    .btn-danger {
      background: #dc3545;
      color: white;
    }
    
    .btn-loading {
      opacity: 0.6;
      cursor: wait;
    }
    
    .btn-disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  `]
})
export class NgClassDemoComponent {
  isActive = false;
  isDisabled = false;
  isHighlighted = false;
  dynamicClasses = 'box active';
  status = 'success';
  
  // Card demo properties
  cardType = 'primary';
  cardSize = 'medium';
  isElevated = false;
  
  // Button demo properties
  buttonState = 'primary';
  isLoading = false;
  isButtonDisabled = false;
}
```

### 2. ngStyle - Dynamic Styles

Dynamically set inline styles.

```typescript
@Component({
  selector: 'app-ngstyle-demo',
  standalone: true,
  imports: [CommonModule, FormsModule],
  template: `
    <div class="container">
      <h2>ngStyle Examples</h2>
      
      <!-- Object syntax -->
      <div [ngStyle]="{
        'color': textColor,
        'font-size': fontSize + 'px',
        'background-color': backgroundColor,
        'padding': '20px',
        'border-radius': '8px'
      }">
        Styled with ngStyle
      </div>
      
      <!-- Conditional styles -->
      <div [ngStyle]="{
        'font-weight': isBold ? 'bold' : 'normal',
        'text-decoration': isUnderlined ? 'underline' : 'none',
        'font-style': isItalic ? 'italic' : 'normal'
      }">
        Conditional styling
      </div>
      
      <!-- Dynamic styles from component -->
      <div [ngStyle]="boxStyles">
        Dynamic box styles
      </div>
      
      <!-- Controls for colors and sizes -->
      <div class="controls">
        <div class="control-group">
          <label>Text Color:</label>
          <input type="color" [(ngModel)]="textColor">
        </div>
        
        <div class="control-group">
          <label>Background Color:</label>
          <input type="color" [(ngModel)]="backgroundColor">
        </div>
        
        <div class="control-group">
          <label>Font Size: {{ fontSize }}px</label>
          <input type="range" [(ngModel)]="fontSize" min="12" max="48">
        </div>
        
        <div class="control-group">
          <label><input type="checkbox" [(ngModel)]="isBold"> Bold</label>
          <label><input type="checkbox" [(ngModel)]="isUnderlined"> Underline</label>
          <label><input type="checkbox" [(ngModel)]="isItalic"> Italic</label>
        </div>
      </div>
      
      <!-- Real-world example: Progress bar -->
      <div class="progress-demo">
        <h3>Progress Bar</h3>
        <div class="progress-container">
          <div class="progress-bar" [ngStyle]="{
            'width': progress + '%',
            'background-color': getProgressColor(),
            'transition': 'width 0.3s ease'
          }">
            {{ progress }}%
          </div>
        </div>
        <div class="controls">
          <button (click)="progress = Math.max(0, progress - 10)">-10%</button>
          <button (click)="progress = Math.min(100, progress + 10)">+10%</button>
          <button (click)="progress = 0">Reset</button>
          <button (click)="progress = 100">Complete</button>
        </div>
      </div>
      
      <!-- Real-world example: Position and size -->
      <div class="position-demo">
        <h3>Draggable Box (Style Demo)</h3>
        <div class="draggable-container">
          <div [ngStyle]="{
            'position': 'absolute',
            'left': boxPosition.x + 'px',
            'top': boxPosition.y + 'px',
            'width': boxSize.width + 'px',
            'height': boxSize.height + 'px',
            'background': boxColor,
            'border-radius': borderRadius + 'px',
            'transform': 'rotate(' + rotation + 'deg)',
            'transition': 'all 0.3s'
          }" class="draggable-box">
            Drag me!
          </div>
        </div>
        
        <div class="controls">
          <div class="control-group">
            <label>X: {{ boxPosition.x }}</label>
            <input type="range" [(ngModel)]="boxPosition.x" min="0" max="300">
          </div>
          <div class="control-group">
            <label>Y: {{ boxPosition.y }}</label>
            <input type="range" [(ngModel)]="boxPosition.y" min="0" max="300">
          </div>
          <div class="control-group">
            <label>Size: {{ boxSize.width }}</label>
            <input type="range" [(ngModel)]="boxSize.width" min="50" max="200" 
                   (ngModelChange)="boxSize.height = boxSize.width">
          </div>
          <div class="control-group">
            <label>Rotation: {{ rotation }}°</label>
            <input type="range" [(ngModel)]="rotation" min="0" max="360">
          </div>
          <div class="control-group">
            <label>Border Radius: {{ borderRadius }}px</label>
            <input type="range" [(ngModel)]="borderRadius" min="0" max="50">
          </div>
          <div class="control-group">
            <label>Color:</label>
            <input type="color" [(ngModel)]="boxColor">
          </div>
        </div>
      </div>
    </div>
  `,
  styles: [`
    .container {
      padding: 20px;
      max-width: 800px;
      margin: 0 auto;
    }
    
    .controls {
      margin: 20px 0;
      padding: 15px;
      background: #f8f9fa;
      border-radius: 4px;
    }
    
    .control-group {
      margin: 10px 0;
    }
    
    label {
      display: inline-block;
      margin-right: 10px;
      font-weight: bold;
    }
    
    input[type="range"],
    input[type="color"] {
      margin: 0 10px;
    }
    
    button {
      padding: 8px 16px;
      margin: 5px;
      border: none;
      border-radius: 4px;
      background: #007bff;
      color: white;
      cursor: pointer;
    }
    
    .progress-container {
      width: 100%;
      height: 30px;
      background: #e9ecef;
      border-radius: 4px;
      overflow: hidden;
      margin: 20px 0;
    }
    
    .progress-bar {
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      font-weight: bold;
    }
    
    .draggable-container {
      position: relative;
      height: 400px;
      background: #f8f9fa;
      border: 2px dashed #ddd;
      border-radius: 8px;
      margin: 20px 0;
    }
    
    .draggable-box {
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      font-weight: bold;
      cursor: move;
    }
  `]
})
export class NgStyleDemoComponent {
  textColor = '#007bff';
  backgroundColor = '#f0f0f0';
  fontSize = 16;
  isBold = false;
  isUnderlined = false;
  isItalic = false;
  
  boxStyles = {
    'color': 'white',
    'background-color': '#28a745',
    'padding': '20px',
    'border-radius': '8px',
    'text-align': 'center'
  };
  
  // Progress bar
  progress = 50;
  Math = Math;
  
  getProgressColor(): string {
    if (this.progress < 33) return '#dc3545';
    if (this.progress < 66) return '#ffc107';
    return '#28a745';
  }
  
  // Draggable box
  boxPosition = { x: 50, y: 50 };
  boxSize = { width: 100, height: 100 };
  boxColor = '#007bff';
  rotation = 0;
  borderRadius = 8;
}
```

## Creating Custom Attribute Directives

### Basic Custom Directive

```typescript
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appHighlight]',
  standalone: true
})
export class HighlightDirective {
  @Input() appHighlight = 'yellow';
  @Input() defaultColor = 'transparent';
  
  constructor(private el: ElementRef) {}
  
  @HostListener('mouseenter')
  onMouseEnter(): void {
    this.highlight(this.appHighlight);
  }
  
  @HostListener('mouseleave')
  onMouseLeave(): void {
    this.highlight(this.defaultColor);
  }
  
  private highlight(color: string): void {
    this.el.nativeElement.style.backgroundColor = color;
  }
}

// Usage
@Component({
  selector: 'app-demo',
  standalone: true,
  imports: [HighlightDirective],
  template: `
    <p appHighlight="lightblue">Hover over me!</p>
    <p appHighlight="lightgreen" defaultColor="white">Different colors!</p>
  `
})
export class DemoComponent {}
```

### Advanced Custom Directive with Renderer2

```typescript
import { Directive, ElementRef, Renderer2, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appTooltip]',
  standalone: true
})
export class TooltipDirective {
  @Input() appTooltip = '';
  private tooltipElement: HTMLElement | null = null;
  
  constructor(
    private el: ElementRef,
    private renderer: Renderer2
  ) {}
  
  @HostListener('mouseenter')
  onMouseEnter(): void {
    if (!this.tooltipElement) {
      this.showTooltip();
    }
  }
  
  @HostListener('mouseleave')
  onMouseLeave(): void {
    if (this.tooltipElement) {
      this.hideTooltip();
    }
  }
  
  private showTooltip(): void {
    this.tooltipElement = this.renderer.createElement('div');
    this.renderer.appendChild(
      this.tooltipElement,
      this.renderer.createText(this.appTooltip)
    );
    
    this.renderer.addClass(this.tooltipElement, 'tooltip');
    this.renderer.appendChild(document.body, this.tooltipElement);
    
    const hostPos = this.el.nativeElement.getBoundingClientRect();
    const tooltipPos = this.tooltipElement.getBoundingClientRect();
    
    const top = hostPos.top - tooltipPos.height - 10;
    const left = hostPos.left + (hostPos.width - tooltipPos.width) / 2;
    
    this.renderer.setStyle(this.tooltipElement, 'top', `${top}px`);
    this.renderer.setStyle(this.tooltipElement, 'left', `${left}px`);
  }
  
  private hideTooltip(): void {
    if (this.tooltipElement) {
      this.renderer.removeChild(document.body, this.tooltipElement);
      this.tooltipElement = null;
    }
  }
}
```

## Real-World Example: Complete UI Library

```typescript
// directives-demo.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-directives-demo',
  standalone: true,
  imports: [CommonModule, FormsModule],
  template: `
    <div class="ui-demo">
      <h1>UI Component Library</h1>
      
      <!-- Alert Component -->
      <section>
        <h2>Alerts</h2>
        <div [ngClass]="{
          'alert': true,
          'alert-success': alertType === 'success',
          'alert-error': alertType === 'error',
          'alert-warning': alertType === 'warning',
          'alert-info': alertType === 'info'
        }">
          <strong>{{ alertTitle }}</strong> {{ alertMessage }}
        </div>
        <button (click)="showAlert('success')">Success</button>
        <button (click)="showAlert('error')">Error</button>
        <button (click)="showAlert('warning')">Warning</button>
        <button (click)="showAlert('info')">Info</button>
      </section>
      
      <!-- Badge Component -->
      <section>
        <h2>Badges</h2>
        <span [ngClass]="{
          'badge': true,
          'badge-primary': badgeType === 'primary',
          'badge-success': badgeType === 'success',
          'badge-danger': badgeType === 'danger'
        }">
          {{ badgeText }}
        </span>
      </section>
      
      <!-- Loading Spinner -->
      <section>
        <h2>Loading States</h2>
        <div [ngStyle]="{
          'display': isLoading ? 'block' : 'none',
          'width': '50px',
          'height': '50px',
          'border': '5px solid #f3f3f3',
          'border-top': '5px solid #007bff',
          'border-radius': '50%',
          'animation': 'spin 1s linear infinite'
        }"></div>
        <button (click)="isLoading = !isLoading">Toggle Loading</button>
      </section>
    </div>
  `,
  styles: [`
    .ui-demo {
      padding: 20px;
      max-width: 800px;
      margin: 0 auto;
    }
    
    section {
      margin: 30px 0;
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 8px;
    }
    
    .alert {
      padding: 15px;
      border-radius: 4px;
      margin: 10px 0;
    }
    
    .alert-success {
      background: #d4edda;
      color: #155724;
      border: 1px solid #c3e6cb;
    }
    
    .alert-error {
      background: #f8d7da;
      color: #721c24;
      border: 1px solid #f5c6cb;
    }
    
    .alert-warning {
      background: #fff3cd;
      color: #856404;
      border: 1px solid #ffeeba;
    }
    
    .alert-info {
      background: #d1ecf1;
      color: #0c5460;
      border: 1px solid #bee5eb;
    }
    
    .badge {
      display: inline-block;
      padding: 5px 10px;
      border-radius: 12px;
      font-size: 12px;
      font-weight: bold;
      color: white;
    }
    
    .badge-primary { background: #007bff; }
    .badge-success { background: #28a745; }
    .badge-danger { background: #dc3545; }
    
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
  `]
})
export class DirectivesDemoComponent {
  alertType = 'success';
  alertTitle = 'Success!';
  alertMessage = 'Operation completed successfully';
  badgeType = 'primary';
  badgeText = 'New';
  isLoading = false;
  
  showAlert(type: string): void {
    this.alertType = type;
    const messages = {
      success: { title: 'Success!', message: 'Operation completed' },
      error: { title: 'Error!', message: 'Something went wrong' },
      warning: { title: 'Warning!', message: 'Please be careful' },
      info: { title: 'Info', message: 'Here is some information' }
    };
    const msg = messages[type as keyof typeof messages];
    this.alertTitle = msg.title;
    this.alertMessage = msg.message;
  }
}
```

## Summary

| Directive | Purpose | Syntax |
|-----------|---------|--------|
| `ngClass` | Dynamic classes | `[ngClass]="expression"` |
| `ngStyle` | Dynamic styles | `[ngStyle]="expression"` |
| Custom | Custom behavior | `@Directive({selector: '[app...]'})` |

## Best Practices

1. **Use ngClass** for multiple class conditions
2. **Use ngStyle** for dynamic inline styles
3. **Prefer CSS classes** over inline styles when possible
4. **Create custom directives** for reusable behaviors
5. **Use Renderer2** instead of direct DOM manipulation
6. **Document custom directives** well
7. **Keep directives focused** on single responsibility

## Next Steps

- Learn about Angular Signals
- Master component lifecycle hooks
- Explore advanced directive patterns
- Build custom directive library
