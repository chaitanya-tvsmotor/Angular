# Reusable Components

> Timestamp: **7:43:29** - Reusable Components

## Creating Reusable Components

```typescript
// reusable-button.component.ts
@Component({
  selector: 'app-button',
  standalone: true,
  template: `
    <button 
      [class]="'btn btn-' + variant + ' ' + (fullWidth ? 'w-100' : '')"
      [disabled]="disabled"
      (click)="handleClick()"
    >
      <ng-content></ng-content>
    </button>
  `,
  styles: [`
    button {
      padding: 0.5rem 1rem;
      border-radius: 4px;
      cursor: pointer;
    }
    button:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  `]
})
export class ReusableButtonComponent {
  @Input() variant: 'primary' | 'secondary' | 'danger' = 'primary';
  @Input() disabled = false;
  @Input() fullWidth = false;
  @Output() clicked = new EventEmitter<void>();
  
  handleClick(): void {
    if (!this.disabled) {
      this.clicked.emit();
    }
  }
}

// Usage
@Component({
  selector: 'app-example',
  standalone: true,
  imports: [ReusableButtonComponent],
  template: `
    <app-button variant="primary" (clicked)="handlePrimary()">
      Primary Action
    </app-button>
    
    <app-button variant="danger" (clicked)="handleDelete()">
      Delete
    </app-button>
    
    <app-button [disabled]="true">
      Disabled Button
    </app-button>
  `
})
export class ExampleComponent {
  handlePrimary(): void {
    console.log('Primary clicked');
  }
  
  handleDelete(): void {
    console.log('Delete clicked');
  }
}
```

## Card Component

```typescript
@Component({
  selector: 'app-card',
  standalone: true,
  template: `
    <div class="card">
      <div class="card-header" *ngIf="title">
        <h3>{{ title }}</h3>
      </div>
      <div class="card-body">
        <ng-content></ng-content>
      </div>
      <div class="card-footer" *ngIf="hasFooter">
        <ng-content select="[footer]"></ng-content>
      </div>
    </div>
  `,
  styles: [`
    .card {
      border: 1px solid #ddd;
      border-radius: 8px;
      overflow: hidden;
    }
    .card-header {
      background: #f5f5f5;
      padding: 1rem;
    }
    .card-body {
      padding: 1rem;
    }
    .card-footer {
      background: #f5f5f5;
      padding: 0.5rem 1rem;
    }
  `]
})
export class CardComponent {
  @Input() title?: string;
  @Input() hasFooter = false;
}

// Usage
<app-card title="User Profile" [hasFooter]="true">
  <p>Card content goes here</p>
  <div footer>
    <button>Action</button>
  </div>
</app-card>
```

## Summary

✅ **@Input()**: Accept configuration
✅ **@Output()**: Emit events
✅ **<ng-content>**: Project content
✅ **Variants**: Support multiple styles
✅ **Reusability**: Use across entire app

Create components once, use everywhere!
