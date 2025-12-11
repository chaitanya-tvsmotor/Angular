# Component Communication - 5 Different Ways

> Timestamp: **8:05:57** - @Input(), @Output() & @ViewChild()

## 1. @Input() - Parent to Child

```typescript
// Child component
@Component({
  selector: 'app-child',
  standalone: true,
  template: `<p>{{ message }}</p>`
})
export class ChildComponent {
  @Input() message: string = '';
}

// Parent component
@Component({
  selector: 'app-parent',
  standalone: true,
  imports: [ChildComponent],
  template: `<app-child [message]="parentMessage"></app-child>`
})
export class ParentComponent {
  parentMessage = 'Hello from Parent';
}
```

## 2. @Output() - Child to Parent

```typescript
// Child component
@Component({
  selector: 'app-child',
  standalone: true,
  template: `<button (click)="sendData()">Send to Parent</button>`
})
export class ChildComponent {
  @Output() dataEvent = new EventEmitter<string>();
  
  sendData(): void {
    this.dataEvent.emit('Data from child');
  }
}

// Parent component
@Component({
  selector: 'app-parent',
  standalone: true,
  imports: [ChildComponent],
  template: `
    <app-child (dataEvent)="receiveData($event)"></app-child>
    <p>{{ receivedData }}</p>
  `
})
export class ParentComponent {
  receivedData = '';
  
  receiveData(data: string): void {
    this.receivedData = data;
  }
}
```

## 3. @ViewChild() - Parent Access to Child

```typescript
@Component({
  selector: 'app-child',
  standalone: true,
  template: `<p>{{ count }}</p>`
})
export class ChildComponent {
  count = 0;
  
  increment(): void {
    this.count++;
  }
}

@Component({
  selector: 'app-parent',
  standalone: true,
  imports: [ChildComponent],
  template: `
    <app-child></app-child>
    <button (click)="callChild()">Increment Child</button>
  `
})
export class ParentComponent {
  @ViewChild(ChildComponent) child!: ChildComponent;
  
  callChild(): void {
    this.child.increment();
  }
}
```

## 4. Service - Any to Any

```typescript
@Injectable({ providedIn: 'root' })
export class DataService {
  private dataSubject = new BehaviorSubject<string>('');
  data$ = this.dataSubject.asObservable();
  
  updateData(data: string): void {
    this.dataSubject.next(data);
  }
}

@Component({
  selector: 'app-sender',
  standalone: true,
  template: `<button (click)="send()">Send Data</button>`
})
export class SenderComponent {
  private dataService = inject(DataService);
  
  send(): void {
    this.dataService.updateData('Hello from Sender');
  }
}

@Component({
  selector: 'app-receiver',
  standalone: true,
  template: `<p>{{ data$ | async }}</p>`
})
export class ReceiverComponent {
  private dataService = inject(DataService);
  data$ = this.dataService.data$;
}
```

## 5. Signals - Modern Approach

```typescript
@Injectable({ providedIn: 'root' })
export class StateService {
  counter = signal(0);
  
  increment(): void {
    this.counter.update(v => v + 1);
  }
}

@Component({
  selector: 'app-display',
  standalone: true,
  template: `<p>Count: {{ stateService.counter() }}</p>`
})
export class DisplayComponent {
  stateService = inject(StateService);
}

@Component({
  selector: 'app-controls',
  standalone: true,
  template: `<button (click)="stateService.increment()">+</button>`
})
export class ControlsComponent {
  stateService = inject(StateService);
}
```

## Summary

✅ **@Input()**: Parent to child data flow
✅ **@Output()**: Child to parent events  
✅ **@ViewChild()**: Direct child access
✅ **Services**: Share data across any components
✅ **Signals**: Modern reactive state sharing

Choose based on your component relationship!
