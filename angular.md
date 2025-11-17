# Angular

Angular is a TypeScript-based web application framework developed by Google. It's a complete rewrite of AngularJS and provides a platform for building scalable web applications.

## Frequently Asked Questions

### 1. What is data binding in angular?

Data binding connects the component's data with the template (view). Angular supports four types of data binding:

```typescript
// Component
export class MyComponent {
  title = 'Hello Angular';
  isVisible = true;
  inputValue = '';
  
  onClick() {
    console.log('Button clicked');
  }
  
  onInputChange(event: any) {
    this.inputValue = event.target.value;
  }
}
```

```html
<!-- 1. Interpolation (One-way: Component → Template) -->
<h1>{{title}}</h1>

<!-- 2. Property Binding (One-way: Component → Template) -->
<div [hidden]="!isVisible">Content</div>
<img [src]="imageUrl" [alt]="imageAlt">

<!-- 3. Event Binding (One-way: Template → Component) -->
<button (click)="onClick()">Click me</button>
<input (input)="onInputChange($event)">

<!-- 4. Two-way Binding (Both directions) -->
<input [(ngModel)]="inputValue">
```

### 2. What are decorators in angular?

Decorators are TypeScript features that add metadata to classes, methods, or properties. Angular uses them extensively for configuration.

```typescript
// Class decorators
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  styleUrls: ['./user.component.css']
})
export class UserComponent { }

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  bootstrap: [AppComponent]
})
export class AppModule { }

// Property decorators
export class UserComponent {
  @Input() user: User;           // Receives data from parent
  @Output() userClick = new EventEmitter<User>(); // Sends data to parent
  @ViewChild('userInput') input: ElementRef; // Access DOM element
  @HostListener('click', ['$event']) onClick(event: Event) { }
}
```

### 3. What are directives in angular?

**Directives** are attributes that add behavior to elements in templates. There are three types:

```typescript
// 1. Component Directives (have templates)
@Component({
  selector: 'app-user-card',
  template: '<div>User Card</div>'
})
export class UserCardComponent { }

// 2. Structural Directives (change DOM structure)
```

```html
<!-- Built-in structural directives -->
<div *ngIf="isLoggedIn">Welcome!</div>
<li *ngFor="let item of items; let i = index">{{i}}: {{item}}</li>
<div [ngSwitch]="userRole">
  <div *ngSwitchCase="'admin'">Admin Panel</div>
  <div *ngSwitchDefault>User Panel</div>
</div>

<!-- 3. Attribute Directives (change appearance/behavior) -->
<div [ngClass]="{'active': isActive, 'disabled': !isEnabled}">
<div [ngStyle]="{'color': textColor, 'font-size': fontSize}">
```

```typescript
// Custom attribute directive
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  @HostListener('mouseenter') onMouseEnter() {
    this.el.nativeElement.style.backgroundColor = 'yellow';
  }
  
  constructor(private el: ElementRef) { }
}
```

### 4. What are pipes in angular?

**Pipes** transform data in templates without changing the original data. They're pure functions for data transformation.

```html
<!-- Built-in pipes -->
<p>{{name | uppercase}}</p>
<p>{{price | currency:'USD':'symbol':'1.2-2'}}</p>
<p>{{birthday | date:'fullDate'}}</p>
<p>{{users | json}}</p>
<p>{{description | slice:0:100}}</p>

<!-- Chaining pipes -->
<p>{{name | lowercase | titlecase}}</p>

<!-- Async pipe for observables -->
<p>{{user$ | async | json}}</p>
```

```typescript
// Custom pipe
@Pipe({name: 'reverse'})
export class ReversePipe implements PipeTransform {
  transform(value: string): string {
    return value.split('').reverse().join('');
  }
}

// Usage: <p>{{'hello' | reverse}}</p> // outputs: olleh
```

### 5. What is View encapsulation?

**View Encapsulation** determines how component styles are scoped and applied.

```typescript
@Component({
  selector: 'app-user',
  template: '<div class="container">User</div>',
  styles: ['.container { color: red; }'],
  encapsulation: ViewEncapsulation.Emulated // Default
})
export class UserComponent { }
```

**Three modes:**
- **Emulated (default)**: Scopes styles to component using attributes
- **None**: No encapsulation, styles apply globally
- **ShadowDom**: Uses native Shadow DOM for true encapsulation

### 6. How does change detection work?

**Change Detection** is Angular's mechanism to update the view when data changes.

```typescript
export class UserComponent {
  users: User[] = [];
  
  // Change detection triggered by:
  // 1. DOM Events
  onClick() {
    this.users.push(new User());
  }
  
  // 2. HTTP Requests
  loadUsers() {
    this.userService.getUsers().subscribe(users => {
      this.users = users; // Change detection runs
    });
  }
  
  // 3. Timers
  ngOnInit() {
    setInterval(() => {
      this.currentTime = new Date(); // Change detection runs
    }, 1000);
  }
}

// Manual change detection
constructor(private cdr: ChangeDetectorRef) {}

updateData() {
  // Detach from change detection
  this.cdr.detach();
  
  // Manually trigger
  this.cdr.detectChanges();
  
  // Reattach
  this.cdr.reattach();
}
```

### 7. What are life cycle hooks?

**Lifecycle hooks** are methods that Angular calls at specific moments in a component's lifecycle.

```typescript
export class UserComponent implements OnInit, OnDestroy, AfterViewInit {
  
  // 1. Constructor - Dependency injection
  constructor(private userService: UserService) { }
  
  // 2. OnInit - Initialize component
  ngOnInit() {
    this.loadUsers();
  }
  
  // 3. AfterViewInit - View and child views initialized
  ngAfterViewInit() {
    this.setupChart();
  }
  
  // 4. OnDestroy - Cleanup before destruction
  ngOnDestroy() {
    this.subscription.unsubscribe();
  }
  
  // Other hooks:
  ngOnChanges(changes: SimpleChanges) { } // Input properties change
  ngDoCheck() { } // Custom change detection
  ngAfterContentInit() { } // Content projection initialized
  ngAfterViewChecked() { } // After view checked for changes
}
```

### 8. Reactive forms vs template driven forms.

**Two approaches** to handle forms in Angular:

```typescript
// Reactive Forms (Model-driven)
export class UserFormComponent {
  userForm = this.fb.group({
    name: ['', [Validators.required, Validators.minLength(2)]],
    email: ['', [Validators.required, Validators.email]],
    address: this.fb.group({
      street: [''],
      city: ['']
    })
  });
  
  constructor(private fb: FormBuilder) {}
  
  onSubmit() {
    if (this.userForm.valid) {
      console.log(this.userForm.value);
    }
  }
}
```

```html
<!-- Reactive Forms Template -->
<form [formGroup]="userForm" (ngSubmit)="onSubmit()">
  <input formControlName="name">
  <div *ngIf="userForm.get('name')?.errors?.['required']">
    Name is required
  </div>
  
  <div formGroupName="address">
    <input formControlName="street" placeholder="Street">
    <input formControlName="city" placeholder="City">
  </div>
  
  <button [disabled]="!userForm.valid">Submit</button>
</form>

<!-- Template Driven Forms -->
<form #userForm="ngForm" (ngSubmit)="onSubmit(userForm)">
  <input name="name" ngModel required minlength="2" #name="ngModel">
  <div *ngIf="name.errors?.['required']">Name is required</div>
  
  <input name="email" ngModel required email #email="ngModel">
  <button [disabled]="!userForm.valid">Submit</button>
</form>
```

**Comparison:**
- **Reactive**: More control, better for complex forms, easier testing
- **Template-driven**: Simpler syntax, good for basic forms

### 9. What is lazy loading? How is it used in angular?

**Lazy loading** loads feature modules only when needed, reducing initial bundle size.

```typescript
// App routing with lazy loading
const routes: Routes = [
  {
    path: 'users',
    loadChildren: () => import('./users/users.module').then(m => m.UsersModule)
  },
  {
    path: 'products',
    loadChildren: () => import('./products/products.module').then(m => m.ProductsModule)
  }
];

// Feature module (users.module.ts)
@NgModule({
  declarations: [UsersComponent, UserDetailComponent],
  imports: [
    CommonModule,
    RouterModule.forChild([
      { path: '', component: UsersComponent },
      { path: ':id', component: UserDetailComponent }
    ])
  ]
})
export class UsersModule { }
```

### 10. What are guards and interceptors in angular?

**Guards** control navigation, **Interceptors** modify HTTP requests/responses.

```typescript
// Route Guard
@Injectable()
export class AuthGuard implements CanActivate {
  constructor(private auth: AuthService, private router: Router) {}
  
  canActivate(): boolean {
    if (this.auth.isLoggedIn()) {
      return true;
    }
    this.router.navigate(['/login']);
    return false;
  }
}

// HTTP Interceptor
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    const authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${this.getToken()}`)
    });
    return next.handle(authReq);
  }
}

// Usage in routing
{
  path: 'dashboard',
  component: DashboardComponent,
  canActivate: [AuthGuard]
}
```

### 11. How does routing work in angular?

**Angular Router** enables navigation between views/components.

```typescript
// App routing configuration
const routes: Routes = [
  { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
  { path: 'dashboard', component: DashboardComponent },
  { path: 'users/:id', component: UserDetailComponent },
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

// Navigation in component
export class AppComponent {
  constructor(private router: Router, private route: ActivatedRoute) {}
  
  navigateToUser(userId: number) {
    this.router.navigate(['/users', userId]);
  }
  
  ngOnInit() {
    // Get route parameters
    this.route.params.subscribe(params => {
      this.userId = params['id'];
    });
  }
}
```

```html
<!-- Router outlet and navigation -->
<nav>
  <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
  <a [routerLink]="['/users', user.id]">User Detail</a>
</nav>

<router-outlet></router-outlet>
```

### 12. What is dependency injection and how does it work in angular?

**Dependency Injection (DI)** is a design pattern where dependencies are provided rather than created by the class.

```typescript
// Service
@Injectable({
  providedIn: 'root' // Singleton across app
})
export class UserService {
  constructor(private http: HttpClient) {}
  
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }
}

// Component using DI
export class UserComponent {
  users: User[] = [];
  
  // Dependencies injected via constructor
  constructor(
    private userService: UserService,
    private logger: LoggerService
  ) {}
  
  ngOnInit() {
    this.userService.getUsers().subscribe(users => {
      this.users = users;
    });
  }
}

// Custom providers
@NgModule({
  providers: [
    { provide: API_URL, useValue: 'https://api.example.com' },
    { provide: UserService, useClass: MockUserService }, // For testing
    { provide: LoggerService, useFactory: loggerFactory, deps: [API_URL] }
  ]
})
export class AppModule { }
```

### 13. What is zonejs and how does angular use it?

**Zone.js** patches asynchronous operations to trigger Angular's change detection automatically.

```typescript
// Zone.js patches these operations:
// - DOM events (click, input, etc.)
// - XMLHttpRequest
// - Promises
// - setTimeout/setInterval

// Running outside Angular zone
export class AppComponent {
  constructor(private ngZone: NgZone) {}
  
  // Heavy computation outside zone (no change detection)
  heavyComputation() {
    this.ngZone.runOutsideAngular(() => {
      setTimeout(() => {
        // This won't trigger change detection
        this.processData();
      }, 1000);
    });
  }
  
  // Re-enter zone to trigger change detection
  updateUI() {
    this.ngZone.run(() => {
      this.data = 'Updated'; // Change detection triggered
    });
  }
}
```

### 14. How to reduce bundle size of angular application?

**Bundle optimization techniques:**

```bash
# Production build with optimizations
ng build --prod

# Analyze bundle size
ng build --stats-json
npx webpack-bundle-analyzer dist/stats.json
```

```typescript
// 1. Lazy loading modules
const routes: Routes = [
  {
    path: 'feature',
    loadChildren: () => import('./feature/feature.module').then(m => m.FeatureModule)
  }
];

// 2. OnPush change detection
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class OptimizedComponent {}

// 3. Tree-shaking friendly imports
import { map } from 'rxjs/operators'; // Good
import * as rxjs from 'rxjs'; // Bad

// 4. Remove unused dependencies
// Check package.json and remove unused packages
```

```json
// angular.json - Build optimizations
{
  "budgets": [
    {
      "type": "initial",
      "maximumWarning": "2mb",
      "maximumError": "5mb"
    }
  ]
}
```

### 15. How to optimize angular application and make it more efficient?

**Performance optimization strategies:**

```typescript
// 1. OnPush Change Detection
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class OptimizedComponent {
  @Input() data: any;
  
  constructor(private cdr: ChangeDetectorRef) {}
  
  updateData() {
    // Manually trigger change detection when needed
    this.cdr.markForCheck();
  }
}

// 2. TrackBy functions for *ngFor
export class ListComponent {
  items: Item[] = [];
  
  trackByFn(index: number, item: Item): any {
    return item.id; // Use unique identifier
  }
}
```

```html
<!-- Use trackBy to avoid re-rendering unchanged items -->
<li *ngFor="let item of items; trackBy: trackByFn">{{item.name}}</li>
```

```typescript
// 3. Async pipe for subscriptions
export class UserComponent {
  users$ = this.userService.getUsers(); // Observable
}
```

```html
<!-- Async pipe handles subscription/unsubscription -->
<div *ngFor="let user of users$ | async">{{user.name}}</div>
```

```typescript
// 4. Preloading strategies
@NgModule({
  imports: [RouterModule.forRoot(routes, {
    preloadingStrategy: PreloadAllModules // or custom strategy
  })]
})
export class AppRoutingModule {}

// 5. Pure pipes
@Pipe({
  name: 'expensive',
  pure: true // Only recalculates when inputs change
})
export class ExpensivePipe implements PipeTransform {
  transform(value: any): any {
    // Expensive computation
    return processedValue;
  }
}
```

**Additional optimizations:**
- Use OnPush change detection
- Implement virtual scrolling for large lists
- Lazy load images and components
- Use service workers for caching
- Optimize bundle size with tree shaking
- Use CDN for static assets

### 16. What is ElementRef in Angular?

**ElementRef** is a wrapper around a DOM element that gives you direct access to the underlying HTML element in your Angular component.

```typescript
import { Component, ElementRef, AfterViewInit } from '@angular/core';

@Component({
  selector: 'app-example',
  template: `
    <div>Hello World!</div>
    <button (click)="changeColor()">Change Color</button>
  `
})
export class ExampleComponent implements AfterViewInit {
  
  constructor(private elementRef: ElementRef) {}
  
  ngAfterViewInit() {
    // Access the native DOM element
    console.log(this.elementRef.nativeElement);
  }
  
  changeColor() {
    // Directly manipulate DOM (use carefully!)
    this.elementRef.nativeElement.style.backgroundColor = 'lightblue';
  }
}
```

**Key points:**
- Provides direct access to DOM elements
- Use with caution as it breaks Angular's abstraction
- Can cause security issues and make testing difficult
- Better to use Renderer2 for DOM manipulation when possible

### 17. What is TemplateRef in Angular?

**TemplateRef** represents an embedded template that can be used to create views dynamically. It's like a blueprint for creating HTML content.

```typescript
import { Component, TemplateRef, ViewChild, ViewContainerRef } from '@angular/core';

@Component({
  selector: 'app-template-example',
  template: `
    <ng-template #myTemplate let-name="name" let-age="age">
      <div class="person-card">
        <h3>{{name}}</h3>
        <p>Age: {{age}}</p>
      </div>
    </ng-template>
    
    <div #container></div>
    <button (click)="showTemplate()">Show Template</button>
    <button (click)="hideTemplate()">Hide Template</button>
  `
})
export class TemplateExampleComponent {
  @ViewChild('myTemplate') template!: TemplateRef<any>;
  @ViewChild('container', { read: ViewContainerRef }) container!: ViewContainerRef;
  
  showTemplate() {
    // Create a view from template with context data
    this.container.createEmbeddedView(this.template, {
      name: 'John Doe',
      age: 30
    });
  }
  
  hideTemplate() {
    this.container.clear();
  }
}
```

**Key points:**
- Represents a template that can be reused
- Can pass data to templates using context
- Used with structural directives like *ngIf, *ngFor
- Useful for creating dynamic content

### 18. What is ViewChild in Angular?

**ViewChild** is a decorator that allows you to access child components, directives, or DOM elements from the parent component.

```typescript
import { Component, ViewChild, ElementRef, AfterViewInit } from '@angular/core';

// Child component
@Component({
  selector: 'app-child',
  template: `<p>I am a child component</p>`
})
export class ChildComponent {
  message = 'Hello from child!';
  
  sayHello() {
    console.log('Child says hello!');
  }
}

// Parent component
@Component({
  selector: 'app-parent',
  template: `
    <input #myInput type="text" placeholder="Type something...">
    <app-child #childComp></app-child>
    <button (click)="accessChild()">Access Child</button>
    <button (click)="focusInput()">Focus Input</button>
  `
})
export class ParentComponent implements AfterViewInit {
  // Access child component
  @ViewChild('childComp') childComponent!: ChildComponent;
  
  // Access DOM element
  @ViewChild('myInput') inputElement!: ElementRef;
  
  ngAfterViewInit() {
    // ViewChild is available after view initialization
    console.log('Child component:', this.childComponent);
    console.log('Input element:', this.inputElement);
  }
  
  accessChild() {
    // Call child component method
    this.childComponent.sayHello();
    console.log(this.childComponent.message);
  }
  
  focusInput() {
    // Focus on input element
    this.inputElement.nativeElement.focus();
  }
}
```

**ViewChildren for multiple elements:**
```typescript
import { ViewChildren, QueryList } from '@angular/core';

@Component({
  template: `
    <app-child #child1></app-child>
    <app-child #child2></app-child>
    <app-child #child3></app-child>
  `
})
export class ParentComponent {
  @ViewChildren(ChildComponent) children!: QueryList<ChildComponent>;
  
  ngAfterViewInit() {
    // Access all child components
    this.children.forEach(child => {
      console.log(child.message);
    });
  }
}
```

**Static option in ViewChild:**
```typescript
import { Component, ViewChild, ElementRef, TemplateRef } from '@angular/core';

@Component({
  selector: 'app-static-example',
  template: `
    <!-- Element always present in template -->
    <input #staticInput type="text" value="Always here">
    
    <!-- Element conditionally rendered -->
    <input #dynamicInput *ngIf="showDynamic" type="text" value="Sometimes here">
    
    <button (click)="toggleDynamic()">Toggle Dynamic Input</button>
    <button (click)="accessElements()">Access Elements</button>
  `
})
export class StaticExampleComponent {
  showDynamic = false;
  
  // Static: true - Available in ngOnInit (for elements always in template)
  @ViewChild('staticInput', { static: true }) staticElement!: ElementRef;
  
  // Static: false (default) - Available in ngAfterViewInit (for dynamic elements)
  @ViewChild('dynamicInput', { static: false }) dynamicElement!: ElementRef;
  
  ngOnInit() {
    // ✅ Works - static element is available
    console.log('Static element in ngOnInit:', this.staticElement);
    
    // ❌ Undefined - dynamic element not available yet
    console.log('Dynamic element in ngOnInit:', this.dynamicElement);
  }
  
  ngAfterViewInit() {
    // ✅ Both elements available (if dynamic element exists)
    console.log('Static element in ngAfterViewInit:', this.staticElement);
    console.log('Dynamic element in ngAfterViewInit:', this.dynamicElement);
  }
  
  toggleDynamic() {
    this.showDynamic = !this.showDynamic;
  }
  
  accessElements() {
    // Static element always available
    if (this.staticElement) {
      this.staticElement.nativeElement.style.backgroundColor = 'lightgreen';
    }
    
    // Dynamic element may or may not be available
    if (this.dynamicElement) {
      this.dynamicElement.nativeElement.style.backgroundColor = 'lightblue';
    } else {
      console.log('Dynamic element not rendered');
    }
  }
}
```

**When to use static: true vs static: false:**
```typescript
// Use static: true when:
@ViewChild('alwaysPresent', { static: true }) element1!: ElementRef;
// - Element is always in the template (no *ngIf, *ngFor)
// - You need access in ngOnInit
// - Element is not inside structural directives

// Use static: false (default) when:
@ViewChild('maybePresent', { static: false }) element2!: ElementRef;
// - Element might be conditionally rendered (*ngIf, *ngFor)
// - Element is inside structural directives
// - You only need access in ngAfterViewInit or later
```

**Key points:**
- Access child components, directives, or DOM elements
- Only available after `ngAfterViewInit` lifecycle hook
- Use template reference variables (#name) to identify elements
- ViewChildren for accessing multiple elements
- Static option determines when query is resolved:
  - `static: true` - Available in ngOnInit, for elements always in template
  - `static: false` (default) - Available in ngAfterViewInit, for dynamic elements

### 19. What is ViewContainerRef in Angular?

**ViewContainerRef** is a container where you can attach one or more views. Think of it as a placeholder in your template where you can dynamically insert components or templates.

```typescript
import { Component, ViewChild, ViewContainerRef, ComponentFactoryResolver, TemplateRef } from '@angular/core';

// Dynamic component to be created
@Component({
  selector: 'app-dynamic',
  template: `
    <div class="dynamic-component">
      <h3>I'm a dynamic component!</h3>
      <p>Created at: {{createdAt}}</p>
    </div>
  `
})
export class DynamicComponent {
  createdAt = new Date().toLocaleTimeString();
}

@Component({
  selector: 'app-container-example',
  template: `
    <div>
      <h2>ViewContainer Example</h2>
      
      <!-- Container for dynamic components -->
      <ng-container #dynamicContainer></ng-container>
      
      <!-- Template to insert -->
      <ng-template #myTemplate>
        <div class="template-content">
          <p>This is from a template!</p>
        </div>
      </ng-template>
      
      <div class="buttons">
        <button (click)="addComponent()">Add Component</button>
        <button (click)="addTemplate()">Add Template</button>
        <button (click)="clearAll()">Clear All</button>
      </div>
    </div>
  `
})
export class ContainerExampleComponent {
  @ViewChild('dynamicContainer', { read: ViewContainerRef }) container!: ViewContainerRef;
  @ViewChild('myTemplate') template!: TemplateRef<any>;
  
  constructor(private componentFactoryResolver: ComponentFactoryResolver) {}
  
  addComponent() {
    // Create component factory
    const componentFactory = this.componentFactoryResolver.resolveComponentFactory(DynamicComponent);
    
    // Create and insert component
    const componentRef = this.container.createComponent(componentFactory);
    console.log('Component created:', componentRef.instance);
  }
  
  addTemplate() {
    // Insert template
    this.container.createEmbeddedView(this.template);
  }
  
  clearAll() {
    // Remove all views from container
    this.container.clear();
  }
}
```

**Modern Angular 13+ approach (without ComponentFactoryResolver):**
```typescript
import { Component, ViewChild, ViewContainerRef, TemplateRef } from '@angular/core';

@Component({
  selector: 'app-modern-container',
  template: `
    <ng-container #container></ng-container>
    <button (click)="addComponent()">Add Component</button>
  `
})
export class ModernContainerComponent {
  @ViewChild('container', { read: ViewContainerRef }) container!: ViewContainerRef;
  
  addComponent() {
    // Modern way - no factory resolver needed
    const componentRef = this.container.createComponent(DynamicComponent);
    componentRef.instance.createdAt = new Date().toLocaleTimeString();
  }
}
```

**Key points:**
- Container for dynamically inserting views and components
- Can hold multiple views at the same time
- Views are inserted in the order they're created
- Use `clear()` to remove all views
- Use `remove(index)` to remove specific view
- Essential for creating dynamic UIs

### 20. What is Renderer2 in Angular?

**Renderer2** is Angular's recommended way to manipulate the DOM safely. It provides platform-independent methods for DOM operations, making your app work in different environments (browser, server, web workers).

```typescript
import { Component, ElementRef, Renderer2, ViewChild, AfterViewInit } from '@angular/core';

@Component({
  selector: 'app-renderer-example',
  template: `
    <div #myDiv class="container">
      <p #myParagraph>Original text</p>
      <button #myButton>Click me</button>
    </div>
    
    <div class="controls">
      <button (click)="addClickListener()">Add Click Listener</button>
    </div>
  `
})
export class RendererExampleComponent implements AfterViewInit {
  @ViewChild('myButton') button!: ElementRef;
  
  private clickListener?: () => void;
  
  constructor(private renderer: Renderer2) {}
  
  addClickListener() {
    // Add event listener safely
    this.clickListener = this.renderer.listen(this.button.nativeElement, 'click', () => {
      alert('Button clicked via Renderer2 listener!');
    });
  }
  
  ngOnDestroy() {
    // Clean up listener if it exists
    if (this.clickListener) {
      this.clickListener();
    }
  }
}
```

**Why use Renderer2 instead of direct DOM manipulation?**
- **Platform independence**: Works in browser, server-side rendering, web workers
- **Security**: Prevents XSS attacks through sanitization
- **Testing**: Easier to test and mock
- **Angular integration**: Works with Angular's change detection
- **Consistency**: Follows Angular's architecture patterns

**Key points:**
- Always prefer Renderer2 over direct DOM manipulation
- Returns cleanup functions for event listeners
- Automatically handles platform differences
- Integrates with Angular's security model
- Essential for server-side rendering (SSR)

### 21. What are resolver functions in router?

**Resolver functions** pre-fetch data before navigating to a route, ensuring the component receives data when it initializes.

```typescript
// Resolver service
@Injectable({
  providedIn: 'root'
})
export class UserResolver implements Resolve<User> {
  constructor(private userService: UserService, private router: Router) {}
  
  resolve(route: ActivatedRouteSnapshot): Observable<User> {
    const userId = route.paramMap.get('id');
    
    return this.userService.getUser(userId).pipe(
      catchError(() => {
        this.router.navigate(['/users']); // Redirect on error
        return EMPTY;
      })
    );
  }
}

// Route configuration
const routes: Routes = [
  {
    path: 'user/:id',
    component: UserDetailComponent,
    resolve: { user: UserResolver } // Key: resolver
  }
];

// Component receives resolved data
export class UserDetailComponent implements OnInit {
  user: User;
  
  constructor(private route: ActivatedRoute) {}
  
  ngOnInit() {
    // Data is already resolved
    this.user = this.route.snapshot.data['user'];
    
    // Or subscribe to data changes
    this.route.data.subscribe(data => {
      this.user = data['user'];
    });
  }
}
```

**Functional resolver (Angular 14+):**
```typescript
// Modern functional approach
export const userResolver: ResolveFn<User> = (route) => {
  const userService = inject(UserService);
  const userId = route.paramMap.get('id');
  
  return userService.getUser(userId);
};

// Route usage
{
  path: 'user/:id',
  component: UserDetailComponent,
  resolve: { user: userResolver }
}
```

**Benefits:**
- **Data availability**: Component gets data immediately
- **Better UX**: No loading states in component
- **Error handling**: Centralized data fetching errors
- **Route protection**: Can prevent navigation if data fails to load