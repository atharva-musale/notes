# Frontend Web development

## SOLID principles

### 1. Single Responsibility Principle (SRP)

**Definition:** A class/module should have only one reason to change (one responsibility).

**Bad: One class doing too much**

```ts
class UserService {
  createUser(data: { name: string; email: string }) {
    // 1. Validate input
    if (!data.email.includes('@')) {
      throw new Error('Invalid email');
    }

    // 2. Save to DB (fake)
    console.log('Saving user to DB', data);

    // 3. Send welcome email
    console.log('Sending email to', data.email);
  }
}
```

**Better: Split responsibilities**

```ts
class UserValidator {
  validate(data: { name: string; email: string }) {
    if (!data.email.includes('@')) {
      throw new Error('Invalid email');
    }
  }
}

class UserRepository {
  save(data: { name: string; email: string }) {
    console.log('Saving user to DB', data);
  }
}

class EmailService {
  sendWelcome(email: string) {
    console.log('Sending welcome email to', email);
  }
}

class UserService {
  constructor(
    private validator: UserValidator,
    private repo: UserRepository,
    private emailService: EmailService
  ) {}

  createUser(data: { name: string; email: string }) {
    this.validator.validate(data);
    this.repo.save(data);
    this.emailService.sendWelcome(data.email);
  }
}
```

---

### 2. Open/Closed Principle (OCP)

**Definition:** Software entities should be open for extension, but closed for modification.

**Goal:** Add new behavior by extending, not by editing existing, tested code.

**Without OCP: Big `if`/`switch` that keeps growing and new function needs to be added if RefundData supports a new property later**

```ts
class RefundData {
  constructor(refundAmount: number, paidAmount: number, usedAmount: number) {
    this.refundAmount = refundAmount;
    this.paidAmount = paidAmount;
    this.usedAmount = usedAmount;
  }
}

const refundData1 = new RefundData(100, 1000, 900);
const refundData2 = new RefundData(200, 1000, 800);
const refundDetailsForPNR = [refundData1, refundData2]

function addRefundAmounts(refundDetails: RefundData[]) {
  return refundDetails.map(data => data.refundAmount)..reduce((acc, curr) => acc + curr);
}

function addPaidAmounts(refundDetails: RefundData[]) {
  return refundDetails.map(data => data.paidAmount)..reduce((acc, curr) => acc + curr);
}

function addUsedAmounts(refundDetails: RefundData[]) {
  return refundDetails.map(data => data.usedAmount)..reduce((acc, curr) => acc + curr);
}

const refundDetailsToDisplay = {
  totalRefundAmount: addRefundAmounts(refundDetailsForPNR),
  totalPaidAmounts: addPaidAmounts(refundDetailsForPNR),
  totalUsedAmounts: addUsedAmounts(refundDetailsForPNR),
}
```

**With OCP: Abstract use of things**

```ts
class RefundData {
  constructor(refundAmount: number, paidAmount: number, usedAmount: number) {
    this.refundAmount = refundAmount;
    this.paidAmount = paidAmount;
    this.usedAmount = usedAmount;
  }
}

const refundData1 = new RefundData(100, 1000, 900);
const refundData2 = new RefundData(200, 1000, 800);
const refundDetailsForPNR = [refundData1, refundData2]

function addAmountsByProperty(refundDetails: RefundData[], property: string) {
  return refundDetails.map(data => data[property])..reduce((acc, curr) => acc + curr);
}

const refundDetailsToDisplay = {
  totalRefundAmount: addAmountsByProperty(refundDetailsForPNR, 'refundAmount'),
  totalPaidAmounts: addAmountsByProperty(refundDetailsForPNR, 'paidAmount'),
  totalUsedAmounts: addAmountsByProperty(refundDetailsForPNR, 'usedAmount'),
}
```

---

### 3. Liskov Substitution Principle (LSP)

**Definition:** Subtypes must be substitutable for their base types without breaking correctness.

**Goal:** Anywhere you use a base type, you should be able to use a subtype and behavior should still make sense.

**Bad: Subclass violates expectations**

```ts
class Rectangle {
  constructor(public width: number, public height: number) {}

  setWidth(width: number) {
    this.width = width;
  }

  setHeight(height: number) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width: number) {
    this.width = width;
    this.height = width; // violates Rectangle expectation
  }

  setHeight(height: number) {
    this.height = height;
    this.width = height;
  }
}
```

Code that expects a `Rectangle` might behave unexpectedly with `Square`.

**Better: Separate abstraction**

```ts
interface Shape {
  getArea(): number;
}

class Rectangle2 implements Shape {
  constructor(public width: number, public height: number) {}

  getArea() {
    return this.width * this.height;
  }
}

class Square2 implements Shape {
  constructor(public side: number) {}

  getArea() {
    return this.side * this.side;
  }
}

function printArea(shape: Shape) {
  console.log('Area:', shape.getArea());
}

printArea(new Rectangle2(10, 20));
printArea(new Square2(10));
```

Both `Rectangle2` and `Square2` can be used wherever `Shape` is expected without breaking behavior.

---

### 4. Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they do not use.

**Goal:** Many small, specific interfaces instead of one "fat" interface.

**Bad: Fat interface**

```ts
interface IWorker {
  work(): void;
  eat(): void;
  attendMeeting(): void;
}

class Robot implements IWorker {
  work() {
    console.log('Robot working');
  }

  eat() {
    // Robot doesn't eat, but must implement
    throw new Error('Robots do not eat');
  }

  attendMeeting() {
    // Maybe no-op
  }
}
```

**Better: Split interfaces**

```ts
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

interface AttendsMeetings {
  attendMeeting(): void;
}

class Human implements Workable, Eatable, AttendsMeetings {
  work() {
    console.log('Human working');
  }

  eat() {
    console.log('Human eating');
  }

  attendMeeting() {
    console.log('Human in meeting');
  }
}

class Robot2 implements Workable {
  work() {
    console.log('Robot working');
  }
}
```

Clients now depend only on what they actually need.

---

### 5. Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend on low-level modules; both should depend on abstractions.

**Goal:** Reduce coupling, make code easier to test and change.

**Without DIP: High-level depends on low-level concrete class**

```ts
class HttpClient {
  get(url: string): Promise<any> {
    // real HTTP call
    return fetch(url).then(r => r.json());
  }
}

class UserApi {
  private http = new HttpClient(); // hard dependency

  getUsers() {
    return this.http.get('/api/users');
  }
}
```

Hard to mock `HttpClient` for tests or swap transport.

**With DIP: Depend on abstraction**

```ts
interface HttpLike {
  get(url: string): Promise<any>;
}

class FetchHttpClient implements HttpLike {
  get(url: string): Promise<any> {
    return fetch(url).then(r => r.json());
  }
}

class MockHttpClient implements HttpLike {
  get(url: string): Promise<any> {
    return Promise.resolve([{ id: 1, name: 'Test User' }]);
  }
}

class UserApi2 {
  constructor(private http: HttpLike) {}

  getUsers() {
    return this.http.get('/api/users');
  }
}

// Production
const userApiProd = new UserApi2(new FetchHttpClient());

// Test
const userApiTest = new UserApi2(new MockHttpClient());
```

The high-level `UserApi2` only knows about `HttpLike` (an abstraction), not about any concrete HTTP implementation.

---

### SOLID Quick Reference

| Principle | Key Idea | Benefit |
|-----------|----------|---------|
| **SRP** | One class, one responsibility | Easier to maintain and test |
| **OCP** | Open for extension, closed for modification | Add features without breaking existing code |
| **LSP** | Subtypes must be substitutable for base types | Reliable inheritance hierarchies |
| **ISP** | Many small interfaces > one large interface | Clients depend only on what they need |
| **DIP** | Depend on abstractions, not concretions | Loose coupling, easier testing |

## Code Organization Architectures

### 1. Monorepo Architecture

**Definition:** A single repository containing multiple related projects/packages (apps, libraries, tools) managed together.

**Structure (Nx + Angular):**
```
my-nx-workspace/
├── apps/
│   ├── web/                    # Main Angular web application
│   │   ├── src/
│   │   │   ├── app/
│   │   │   ├── assets/
│   │   │   └── main.ts
│   │   ├── project.json
│   │   └── tsconfig.app.json
│   ├── mobile/                 # Ionic/Capacitor mobile app
│   │   └── src/
│   └── admin/                  # Admin dashboard
│       └── src/
├── libs/
│   ├── shared/
│   │   ├── ui/                 # Shared UI component library
│   │   │   ├── src/
│   │   │   │   ├── lib/
│   │   │   │   │   ├── button/
│   │   │   │   │   │   ├── button.component.ts
│   │   │   │   │   │   ├── button.component.html
│   │   │   │   │   │   └── button.component.scss
│   │   │   │   │   ├── input/
│   │   │   │   │   └── shared-ui.module.ts
│   │   │   │   └── index.ts
│   │   │   ├── project.json
│   │   │   └── tsconfig.lib.json
│   │   ├── data-access/        # API services and state management
│   │   │   └── src/
│   │   ├── utils/              # Utility functions
│   │   │   └── src/
│   │   └── models/             # Shared TypeScript interfaces/types
│   │       └── src/
│   └── feature/
│       ├── auth/               # Auth feature library
│       ├── products/           # Products feature library
│       └── cart/               # Cart feature library
├── nx.json                     # Nx configuration
├── package.json                # Root package.json
└── tsconfig.base.json          # Base TypeScript config
```

**Creating Nx workspace:**

```bash
# Create new Nx workspace with Angular
npx create-nx-workspace@latest my-nx-workspace \
  --preset=angular-monorepo \
  --appName=web \
  --style=scss

# Generate additional Angular app
nx g @nx/angular:app admin

# Generate Angular library
nx g @nx/angular:library ui --directory=shared --buildable

# Generate component in library
nx g @nx/angular:component button \
  --project=shared-ui \
  --export
```

**Nx Configuration:**

```json
// nx.json
{
  "targetDefaults": {
    "build": {
      "dependsOn": ["^build"],
      "cache": true
    },
    "test": {
      "cache": true
    }
  },
  "namedInputs": {
    "default": ["{projectRoot}/**/*"],
    "production": ["!{projectRoot}/**/*.spec.ts"]
  }
}

// tsconfig.base.json - Path mappings for libraries
{
  "compilerOptions": {
    "paths": {
      "@mycompany/shared/ui": ["libs/shared/ui/src/index.ts"],
      "@mycompany/shared/data-access": ["libs/shared/data-access/src/index.ts"],
      "@mycompany/shared/utils": ["libs/shared/utils/src/index.ts"],
      "@mycompany/shared/models": ["libs/shared/models/src/index.ts"]
    }
  }
}
```

**Creating shared UI library:**

```typescript
// libs/shared/ui/src/lib/button/button.component.ts
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'mycompany-button',
  standalone: true,
  imports: [CommonModule],
  template: `
    <button 
      [class]="'btn ' + variant" 
      [disabled]="disabled"
      (click)="onClick.emit()">
      {{ label }}
    </button>
  `,
  styles: [`
    .btn { padding: 8px 16px; border-radius: 4px; }
    .primary { background: blue; color: white; }
    .secondary { background: gray; color: white; }
  `]
})
export class ButtonComponent {
  @Input() label = '';
  @Input() variant: 'primary' | 'secondary' = 'primary';
  @Input() disabled = false;
  @Output() onClick = new EventEmitter<void>();
}

// libs/shared/ui/src/lib/input/input.component.ts
import { Component, Input, forwardRef } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'mycompany-input',
  standalone: true,
  imports: [CommonModule, FormsModule],
  template: `
    <input 
      [type]="type"
      [placeholder]="placeholder"
      [disabled]="disabled"
      [(ngModel)]="value"
      (blur)="onTouched()"
    />
  `,
  providers: [{
    provide: NG_VALUE_ACCESSOR,
    useExisting: forwardRef(() => InputComponent),
    multi: true
  }]
})
export class InputComponent implements ControlValueAccessor {
  @Input() placeholder = '';
  @Input() type = 'text';
  @Input() disabled = false;
  
  value = '';
  onChange: any = () => {};
  onTouched: any = () => {};
  
  writeValue(value: any): void {
    this.value = value;
  }
  
  registerOnChange(fn: any): void {
    this.onChange = fn;
  }
  
  registerOnTouched(fn: any): void {
    this.onTouched = fn;
  }
}

// libs/shared/ui/src/index.ts - Public API
export * from './lib/button/button.component';
export * from './lib/input/input.component';
```

**Creating shared data-access library:**

```typescript
// libs/shared/data-access/src/lib/services/api.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { User } from '@mycompany/shared/models';

@Injectable({ providedIn: 'root' })
export class ApiService {
  constructor(private http: HttpClient) {}
  
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }
}

// libs/shared/data-access/src/index.ts
export * from './lib/services/api.service';
```

**Creating shared models library:**

```typescript
// libs/shared/models/src/lib/user.model.ts
export interface User {
  id: string;
  name: string;
  email: string;
}

export interface Product {
  id: string;
  name: string;
  price: number;
}

// libs/shared/models/src/index.ts
export * from './lib/user.model';
```

**Using libraries in Angular app:**

```typescript
// apps/web/src/app/app.component.ts
import { Component, OnInit } from '@angular/core';
import { ButtonComponent, InputComponent } from '@mycompany/shared/ui';
import { ApiService } from '@mycompany/shared/data-access';
import { User } from '@mycompany/shared/models';
import { formatDate } from '@mycompany/shared/utils';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [ButtonComponent, InputComponent],
  template: `
    <h1>Welcome to {{ title }}!</h1>
    
    <mycompany-button 
      label="Click me" 
      variant="primary"
      (onClick)="handleClick()">
    </mycompany-button>
    
    <mycompany-input 
      placeholder="Enter text"
      [(ngModel)]="inputValue">
    </mycompany-input>
    
    <p>Current date: {{ formattedDate }}</p>
    
    <div *ngFor="let user of users">
      {{ user.name }} - {{ user.email }}
    </div>
  `
})
export class AppComponent implements OnInit {
  title = 'web';
  inputValue = '';
  users: User[] = [];
  formattedDate = formatDate(new Date());
  
  constructor(private apiService: ApiService) {}
  
  ngOnInit() {
    this.apiService.getUsers().subscribe(users => {
      this.users = users;
    });
  }
  
  handleClick() {
    console.log('Button clicked!');
  }
}
```

**Running Nx commands:**

```bash
# Serve a specific app
nx serve web
nx serve admin

# Build with dependencies
nx build web  # Automatically builds shared libraries first

# Run tests
nx test shared-ui
nx test web

# Run all tests
nx run-many --target=test --all

# Build all apps
nx run-many --target=build --all

# Lint specific project
nx lint shared-ui

# Generate dependency graph
nx graph

# Run affected commands (only what changed)
nx affected:test
nx affected:build
nx affected:lint

# Add dependency to specific project
npm install lodash --save
# Then import in the specific library/app
```

**Nx project configuration:**

```json
// libs/shared/ui/project.json
{
  "name": "shared-ui",
  "sourceRoot": "libs/shared/ui/src",
  "projectType": "library",
  "targets": {
    "build": {
      "executor": "@nx/angular:package",
      "outputs": ["{workspaceRoot}/dist/libs/shared/ui"],
      "options": {
        "project": "libs/shared/ui/ng-package.json"
      }
    },
    "test": {
      "executor": "@nx/jest:jest",
      "options": {
        "jestConfig": "libs/shared/ui/jest.config.ts"
      }
    },
    "lint": {
      "executor": "@nx/linter:eslint",
      "options": {
        "lintFilePatterns": ["libs/shared/ui/**/*.ts"]
      }
    }
  },
  "tags": ["scope:shared", "type:ui"]
}
```

**Pros:**
- ✅ Code sharing simplified (no versioning hassle)
- ✅ Atomic commits across projects
- ✅ Easier refactoring (change propagates immediately)
- ✅ Single CI/CD pipeline
- ✅ Consistent tooling and dependencies
- ✅ Better visibility into entire codebase

**Cons:**
- ❌ Large repository size
- ❌ Slower clone/checkout times
- ❌ Complex CI (need to detect what changed)
- ❌ Access control harder (all or nothing)
- ❌ Tooling needs to scale

**Use cases:** Google, Facebook, Microsoft, large product families, design systems

**Popular tools:** Turborepo, Nx, Lerna, pnpm workspaces, Yarn workspaces

---

### 2. Polyrepo (Multi-repo) Architecture

**Definition:** Each project/package lives in its own separate repository.

**Structure:**
```
@mycompany/web (repo 1)
├── src/
└── package.json
    └── dependencies: 
        └── "@mycompany/ui": "^2.3.0"  // Published npm package

@mycompany/ui (repo 2)
├── src/
└── package.json
    └── version: "2.3.0"

@mycompany/mobile (repo 3)
├── src/
└── package.json
```

**How it works:**

```typescript
// Repo: @mycompany/ui
// package.json
{
  "name": "@mycompany/ui",
  "version": "2.3.0",
  "main": "dist/index.js"
}

// Build and publish to npm
// npm publish

// Repo: @mycompany/web
// package.json
{
  "name": "@mycompany/web",
  "dependencies": {
    "@mycompany/ui": "^2.3.0"  // Install from npm registry
  }
}

// Use like any other npm package
import { Button } from '@mycompany/ui';
```

**Versioning and updates:**

```bash
# In UI repo - make changes and publish
cd ui-repo
npm version patch  # 2.3.0 -> 2.3.1
npm publish

# In web repo - update dependency
cd web-repo
npm update @mycompany/ui
# or manually edit package.json and run npm install
```

**Pros:**
- ✅ Clear boundaries and ownership
- ✅ Independent versioning
- ✅ Granular access control
- ✅ Smaller, faster clones
- ✅ Easier to open-source individual packages
- ✅ Simpler CI per repo

**Cons:**
- ❌ Version coordination overhead
- ❌ Harder to refactor across repos
- ❌ Changes require multiple PRs
- ❌ Dependency hell risk
- ❌ Duplicate tooling config
- ❌ Testing integration harder

**Use cases:** Open-source libraries, loosely coupled services, small teams

---

### 3. Hybrid Approach (Modular Monorepo)

**Definition:** Monorepo with clear module boundaries, treating internal packages like external ones.

**Structure:**
```
monorepo/
├── apps/
│   └── web/
├── packages/
│   ├── core/           # Core business logic (versioned)
│   ├── ui/             # UI library (versioned)
│   └── features/       # Feature modules
│       ├── auth/
│       ├── dashboard/
│       └── analytics/
└── internal/           # Internal tools (not versioned)
    ├── build-tools/
    └── test-utils/
```

**Versioning strategy:**

```json
// packages/ui/package.json
{
  "name": "@mycompany/ui",
  "version": "3.1.0",      // Semantic versioning even in monorepo
  "publishConfig": {
    "access": "public"
  }
}

// apps/web/package.json
{
  "dependencies": {
    "@mycompany/ui": "workspace:^3.0.0",  // Use workspace protocol
    "@mycompany/core": "workspace:*"       // Always latest in workspace
  }
}
```

**Pros:**
- ✅ Best of both worlds
- ✅ Can publish packages independently
- ✅ Clear module contracts
- ✅ Migration path to polyrepo

**Cons:**
- ❌ More complex setup
- ❌ Need discipline to maintain boundaries

---

### 4. Microfrontend Architecture

**Definition:** Breaking frontend into independently deployable micro-applications.

**Structure:**
```
shell-app/              # Container/orchestrator
├── src/
│   ├── App.tsx        # Loads microfrontends
│   └── routes.tsx

header-mfe/             # Separate repo/deployment
├── src/
└── webpack.config.js  # Module Federation config

products-mfe/           # Separate repo/deployment
cart-mfe/               # Separate repo/deployment
checkout-mfe/           # Separate repo/deployment
```

**Module Federation setup:**

```javascript
// products-mfe/webpack.config.js
const { ModuleFederationPlugin } = require('webpack').container;

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'products',
      filename: 'remoteEntry.js',
      exposes: {
        './ProductList': './src/ProductList',
        './ProductDetail': './src/ProductDetail'
      },
      shared: {
        react: { singleton: true },
        'react-dom': { singleton: true }
      }
    })
  ]
};

// shell-app/webpack.config.js
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'shell',
      remotes: {
        products: 'products@http://localhost:3001/remoteEntry.js',
        cart: 'cart@http://localhost:3002/remoteEntry.js',
        checkout: 'checkout@http://localhost:3003/remoteEntry.js'
      },
      shared: {
        react: { singleton: true },
        'react-dom': { singleton: true }
      }
    })
  ]
};
```

**Usage in shell:**

```typescript
// shell-app/src/App.tsx
import React, { lazy, Suspense } from 'react';

const ProductList = lazy(() => import('products/ProductList'));
const Cart = lazy(() => import('cart/Cart'));
const Checkout = lazy(() => import('checkout/Checkout'));

function App() {
  return (
    <BrowserRouter>
      <Header />
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/products" element={<ProductList />} />
          <Route path="/cart" element={<Cart />} />
          <Route path="/checkout" element={<Checkout />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

**Communication between microfrontends:**

```typescript
// Shared event bus
// packages/shared/src/eventBus.ts
class EventBus {
  private events: Map<string, Function[]> = new Map();

  subscribe(event: string, callback: Function) {
    if (!this.events.has(event)) {
      this.events.set(event, []);
    }
    this.events.get(event)!.push(callback);
  }

  publish(event: string, data: any) {
    if (this.events.has(event)) {
      this.events.get(event)!.forEach(cb => cb(data));
    }
  }
}

export const eventBus = new EventBus();

// products-mfe: Publish event
import { eventBus } from '@shared/eventBus';

function ProductCard({ product }) {
  const handleAddToCart = () => {
    eventBus.publish('cart:add', product);
  };
  
  return <button onClick={handleAddToCart}>Add to Cart</button>;
}

// cart-mfe: Subscribe to event
import { eventBus } from '@shared/eventBus';

function Cart() {
  const [items, setItems] = useState([]);
  
  useEffect(() => {
    eventBus.subscribe('cart:add', (product) => {
      setItems(prev => [...prev, product]);
    });
  }, []);
  
  return <div>Cart: {items.length} items</div>;
}
```

**Pros:**
- ✅ True team independence
- ✅ Deploy microfrontends separately
- ✅ Technology diversity possible
- ✅ Parallel development
- ✅ Fault isolation

**Cons:**
- ❌ Complex orchestration
- ❌ Shared state management hard
- ❌ Potential for duplicate code/dependencies
- ❌ Runtime performance overhead
- ❌ Debugging complexity

**Use cases:** Large enterprise apps, multi-team products, gradual migration strategies

---

### 5. Feature-based Architecture

**Definition:** Organize code by features/domains rather than technical layers.

**Structure:**
```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   │   ├── LoginForm.tsx
│   │   │   └── SignupForm.tsx
│   │   ├── hooks/
│   │   │   └── useAuth.ts
│   │   ├── api/
│   │   │   └── authApi.ts
│   │   ├── store/
│   │   │   └── authSlice.ts
│   │   ├── types/
│   │   │   └── auth.types.ts
│   │   └── index.ts
│   ├── products/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api/
│   │   └── index.ts
│   └── cart/
├── shared/              # Truly shared code
│   ├── components/
│   ├── hooks/
│   └── utils/
└── App.tsx
```

**Feature module example:**

```typescript
// features/auth/index.ts
export { LoginForm, SignupForm } from './components';
export { useAuth, useLogin } from './hooks';
export { authApi } from './api';
export type { User, AuthState } from './types';

// features/auth/components/LoginForm.tsx
import { useLogin } from '../hooks/useAuth';
import { authApi } from '../api/authApi';

export function LoginForm() {
  const { login, isLoading } = useLogin();
  
  const handleSubmit = async (data) => {
    await login(data);
  };
  
  return <form onSubmit={handleSubmit}>...</form>;
}

// App.tsx - Import entire feature
import { LoginForm, useAuth } from './features/auth';
import { ProductList } from './features/products';

function App() {
  const { user } = useAuth();
  
  return user ? <ProductList /> : <LoginForm />;
}
```

**Pros:**
- ✅ Easy to locate feature code
- ✅ Easier to delete features
- ✅ Clear feature boundaries
- ✅ Reduced coupling
- ✅ Team ownership per feature

**Cons:**
- ❌ Risk of code duplication
- ❌ Unclear where truly shared code goes
- ❌ Might over-engineer small projects

**Use cases:** Medium to large apps, feature-flag driven development, domain-driven design

---

### Architecture Comparison

| Architecture | Team Size | Complexity | Code Sharing | Deployment | Best For |
|-------------|-----------|------------|--------------|------------|----------|
| **Monorepo** | Medium-Large | Medium | Easy | Single | Product families, design systems |
| **Polyrepo** | Any | Low-Medium | Hard (versioning) | Independent | Microservices, OSS libraries |
| **Hybrid Monorepo** | Large | High | Easy + Versioned | Flexible | Enterprise with external packages |
| **Microfrontend** | Large | Very High | Medium | Independent | Multi-team enterprise apps |
| **Feature-based** | Small-Medium | Low | Easy | Single | Domain-driven apps |

---

### Decision Framework

**Choose Monorepo when:**
- Multiple related apps share significant code
- You want atomic cross-project changes
- Team collaborates closely
- CI/CD can handle the scale

**Choose Polyrepo when:**
- Projects are truly independent
- Different teams own different products
- You need granular access control
- Projects have different release cycles

**Choose Microfrontend when:**
- Multiple autonomous teams
- Need independent deployments
- Gradual migration from legacy
- Different tech stacks required

**Choose Feature-based when:**
- Building a single application
- Want clear feature boundaries
- Domain-driven design approach
- Medium complexity project