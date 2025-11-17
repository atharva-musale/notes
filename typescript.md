# Typescript

TypeScript is a strongly typed programming language that builds on JavaScript by adding static type definitions. It compiles to plain JavaScript and runs anywhere JavaScript runs - browsers, Node.js, or any JavaScript engine.

To configure how it does it, you can use `tsconfig` configuration file and set some configurations. For example `target` property determines which version of javascript to transpile your typescript code into.

## Types in Typescript

- Union: accepts any of the types mentioned in the type declaration. Example:
```typescript
// can be string OR number
let id: string | number; 
```

- Tuple: An array where type per position is fixed. Example:
```typescript
let myTuple: [string, number, boolean] = ['foo', 5, false];
```

- Enum: A way to define named constants.

```typescript
// Numeric enum (default starts at 0)
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}

// String enum
enum Color {
  Red = "red",
  Green = "green",
  Blue = "blue"
}
```
### Type alias (`type`) and interface
It is a keyword that allows you to make custom types. For example:
```typescript
type person = {
  firstname: string,
  lastname: string
}
```
Both `type` and `interface` can define object shapes, but they have key differences:

**Key Differences:**

| Feature | `interface` | `type` |
|---------|-------------|---------|
| Extension | `extends` keyword | Intersection (`&`) |
| Declaration Merging | ✅ Supported | ❌ Not supported |
| Computed Properties | ❌ Not supported | ✅ Supported |
| Union/Intersection | ❌ Cannot define unions | ✅ Can define unions |
| Primitives | ❌ Only objects | ✅ Any type |

**When to use:**
- **Interface**: For object shapes that might be extended or merged
- **Type**: For unions, intersections, computed properties, or simple aliases 

## Index signatures

Sometimes you get an error when you try to use `object[prop]` notation to get the value.
Can be fixed by using `keyof`, example:
```typescript
// can be done with defining type of key
interface Foo {
  [key: string]: number;
}

// better way
interface Student {
  name: string;
  id: number;
  classes: string[];
}

const student: Student = { id: 1, name: 'John', classes: ['Physics'] };

Object.keys(student).forEach((key) => {
  console.log(student[key keyof Student]);
});
```

## Generics

**Generics** allow you to write reusable code that works with different types while maintaining type safety. Think of them as "type variables" that get filled in when you use the function/class.

```typescript
// Generic function - T is a placeholder for any type
function identity<T>(arg: T): T {
  return arg;
}

// Usage - TypeScript infers the type
const result1 = identity("hello");    // result1 is string
const result2 = identity(42);         // result2 is number
const result3 = identity(true);       // result3 is boolean

// Generic array function
function getFirstItem<T>(items: T[]): T {
  return items[0];
}

const firstNumber = getFirstItem([1, 2, 3]);      // number
const firstName = getFirstItem(["a", "b", "c"]);  // string
```

**Generic Interfaces:**
```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

const userResponse: ApiResponse<User> = {
  data: { name: "John", age: 30 },
  status: 200,
  message: "Success"
};

const numbersResponse: ApiResponse<number[]> = {
  data: [1, 2, 3],
  status: 200,
  message: "Success"
};
```

**Generic Constraints:**
```typescript
// Constrain T to have a 'length' property
function logLength<T extends { length: number }>(item: T): T {
  console.log(item.length);
  return item;
}

logLength("hello");      // Works - string has length
logLength([1, 2, 3]);    // Works - array has length
// logLength(123);       // Error - number doesn't have length
```

**Why use generics?**
- **Reusability**: One function works with many types
- **Type Safety**: Maintains type checking
- **No `any`**: Avoids losing type information

## Utility Types

**Utility types** are built-in TypeScript types that help transform existing types. They make working with types easier and more flexible.

**Common Utility Types:**

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age?: number;
}

// Partial<T> - Makes all properties optional
type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; age?: number; }

const updateUser = (id: number, updates: Partial<User>) => {
  // Can update just some fields
};

// Required<T> - Makes all properties required
type RequiredUser = Required<User>;
// { id: number; name: string; email: string; age: number; }

// Pick<T, K> - Select specific properties
type UserSummary = Pick<User, 'id' | 'name'>;
// { id: number; name: string; }

// Omit<T, K> - Exclude specific properties
type UserWithoutId = Omit<User, 'id'>;
// { name: string; email: string; age?: number; }

// Record<K, T> - Create object type with specific keys and values
type UserRoles = Record<'admin' | 'user' | 'guest', boolean>;
// { admin: boolean; user: boolean; guest: boolean; }

// Readonly<T> - Make all properties read-only
type ReadonlyUser = Readonly<User>;
// { readonly id: number; readonly name: string; ... }
```

**Practical Examples:**

```typescript
// API response handling
interface ApiUser {
  id: number;
  name: string;
  email: string;
  createdAt: string;
}

// Form data (no id, no createdAt)
type CreateUserData = Omit<ApiUser, 'id' | 'createdAt'>;

// Update form (all optional except id)
type UpdateUserData = { id: number } & Partial<Omit<ApiUser, 'id'>>;

// Configuration objects
type Theme = 'light' | 'dark' | 'auto';
type Config = Record<Theme, { background: string; text: string }>;

const themeConfig: Config = {
  light: { background: '#fff', text: '#000' },
  dark: { background: '#000', text: '#fff' },
  auto: { background: '#f5f5f5', text: '#333' }
};
```

**When to use utility types:**
- **Partial**: Form updates, optional configurations
- **Pick/Omit**: API data transformation, component props
- **Record**: Mapping types, configuration objects
- **Required/Readonly**: Ensuring data integrity

