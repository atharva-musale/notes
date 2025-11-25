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