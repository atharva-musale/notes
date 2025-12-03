# Understanding React

## Project Overview
This project explores React concepts through practical examples, comparing a custom React-like implementation with actual React code.

## Key React Concepts Demonstrated

### 1. Component-Based Architecture
- **Function Components**: The code uses function components (`App`, `Counter`) which are the modern standard
- **Reusability**: The `Counter` component is reused multiple times with different props
- **Pure Functions**: Components are implemented as pure functions that return JSX elements

### 2. Props System
- **Immutability**: Props are frozen objects and cannot be modified (demonstrated in `Counter` component)
- **Data Flow**: Props enable parent-to-child data passing (`index` prop in Counter)
- **Component Communication**: Props allow components to receive external data and configuration

### 3. React.createElement API
- **Core Building Block**: All components use `React.createElement()` instead of JSX
- **Element Structure**: Takes three parameters: element type, props object, and children
- **Virtual DOM**: These function calls create virtual DOM elements before rendering

### 4. React DOM and Rendering
- **Root Creation**: Uses `ReactDOM.createRoot()` for React 18's concurrent features
- **Rendering Process**: `root.render()` initiates the rendering of the component tree
- **DOM Mounting**: Components mount to a specific DOM element (`mainContent`)

### 5. Event Handling
- **Event Binding**: Uses arrow functions for event handlers (`onClick: () => console.log('Increment')`)
- **Event System**: React provides a synthetic event system for cross-browser compatibility

### 6. Virtual DOM Concept (Prototype Implementation)
The `simple-react-prototype.js` file demonstrates the core concepts React uses:

#### Declarative UI Structure
```javascript
// Markup object represents the desired UI state
const markup = {
  type: 'div',
  classes: ['container'],
  children: [...]
};
```

#### DOM Manipulation
- **Imperative Rendering**: Custom `addElement()` function converts markup to real DOM
- **Recursive Structure**: Handles nested elements and text nodes
- **Performance Insight**: Highlights why full re-rendering is inefficient

## JSX
One of the ways to represent trees is to use XML or markup like structure. If that is continued in javascript file, it starts going to jsx domain, which is this system to be able to use markup in js files to make writing react code easier. A transpiler is used to achieve that. It converts the xml like markup into javascript which can then be compiled by browser.

### JSX Deep Dive

JSX (JavaScript XML) is a syntax extension that allows you to write HTML-like markup directly in JavaScript. It's not a separate templating language but rather a more intuitive way to create React elements.

This project uses `React.createElement()` calls directly, which demonstrates the underlying mechanism that JSX compiles to. 

#### JSX vs React.createElement Comparison

**Current Code (React.createElement):**
```javascript
function Counter(props) {
  return (
    React.createElement('article', { className: 'container' },
      React.createElement('h2', null, `Counter ${props.index}`),
      React.createElement('p', null, 'Current count: ', 
        React.createElement('span', { className: 'outfit count' }, '0')
      ),
      React.createElement('button', { 
        className: 'btn btn-orange increment-button', 
        onClick: () => console.log('Increment') 
      }, 'Increment')
    )
  );
}
```

**JSX Equivalent:**
```jsx
function Counter(props) {
  return (
    <article className="container">
      <h2>Counter {props.index}</h2>
      <p>
        Current count: 
        <span className="outfit count">0</span>
      </p>
      <button 
        className="btn btn-orange increment-button"
        onClick={() => console.log('Increment')}>
        Increment
      </button>
    </article>
  );
}
```

#### Essential JSX Rules

1. **Single Parent Element**: Must return one parent element or React Fragment
2. **Attribute Names**: Use `className` not `class`, `htmlFor` not `for`
3. **Self-Closing Tags**: Must end with `/` (e.g., `<img />`, `<br />`)
4. **Event Handlers**: Use camelCase (`onClick`, `onChange`)
5. **JavaScript Expressions**: Use curly braces `{expression}`

#### JavaScript in JSX
```jsx
function Greeting({ name, count }) {
  return (
    <div>
      <h1>Hello {name}!</h1>
      <p>You have {count} items</p>
      {count > 0 && <button>View Items</button>}
    </div>
  );
}
```

#### Conditional Rendering Patterns
```jsx
// Ternary operator
{isLoading ? <Spinner /> : <Content />}

// Logical AND
{user && <WelcomeMessage user={user} />}

// Variable assignment
const content = isLoggedIn ? <Dashboard /> : <Login />;
```

#### React Fragments
```jsx
// Short syntax
function Component() {
  return (
    <>
      <h1>Title</h1>
      <p>Description</p>
    </>
  );
}

// Explicit syntax (when you need keys)
function Component() {
  return (
    <React.Fragment key="fragment-key">
      <h1>Title</h1>
      <p>Description</p>
    </React.Fragment>
  );
}
```

#### Transpilation Process
1. **Developer writes JSX**: Human-friendly syntax
2. **Babel/transpiler converts**: JSX → `React.createElement()` calls
3. **React processes**: Creates virtual DOM elements
4. **ReactDOM renders**: Virtual DOM → Real DOM

#### Common JSX Pitfalls
```jsx
// ❌ Multiple root elements
function Wrong() {
  return (
    <h1>Title</h1>
    <p>Text</p>  // Error!
  );
}

// ✅ Single root or Fragment
function Correct() {
  return (
    <div>
      <h1>Title</h1>
      <p>Text</p>
    </div>
  );
}

// ❌ JavaScript statements in JSX
{if (condition) return <div />}  // Error!

// ✅ Expressions only
{condition && <div />}  // Correct!
```

## Fiber Tree

React Fiber is like React's internal "to-do list" system. It helps React keep track of what components need to be updated and in what order, making your app feel smooth and responsive.

### What is Fiber?

Think of **Fiber** as React's way of organizing work. Each component in your app gets its own "fiber node" - like a sticky note that contains:
- What the component is (div, Counter, etc.)
- What data it needs (props)
- What it should do next

### The Alternate Branch - React's Magic Trick

Here's the key concept that makes React fast: **React keeps TWO copies of your component tree**.

#### How the Alternate Branch Works

Imagine you're editing a document:
- **Current Version**: What's currently displayed on your screen
- **Draft Version**: The new version you're working on

React does the same thing:

```javascript
// Every fiber node has two versions
const fiberNode = {
  type: 'Counter',
  props: { index: 'One' },
  
  // This is the "alternate branch" - the key to React's performance
  alternate: null,  // Points to the other version of this same component
  
  // Other important stuff
  child: null,      // First child component
  sibling: null,    // Next component at same level
  return: null,     // Parent component
};
```

#### The Two-Tree System

```
Current Tree (what user sees)     Work-in-Progress Tree (being built)
      App                               App*
    /     \                          /      \
Counter1  Counter2      →        Counter1*  Counter2*
   |        |                       |         |
 count:0  count:0                 count:1   count:0
```

**The alternate branch connects these trees:**
- Current Counter1 ↔ Work-in-Progress Counter1 (via `alternate`)
- Current Counter2 ↔ Work-in-Progress Counter2 (via `alternate`)

### Why This Matters for Your Project

Even with your simple Counter components, React is doing this behind the scenes:

1. **User clicks button** → React starts building new tree
2. **Builds Work-in-Progress** → Updates Counter1 from 0 to 1
3. **Compares via alternate** → "Old Counter1 had count:0, new has count:1"
4. **Swaps trees** → Work-in-Progress becomes Current
5. **Updates DOM** → Button click appears on screen

### Simple Example

```javascript
// When your Counter button is clicked:

// BEFORE (Current Tree)
const currentCounterFiber = {
  type: Counter,
  props: { index: 'One' },
  stateNode: { count: 0 },        // Current count
  alternate: null                 // No alternate yet
};

// DURING UPDATE (Building Work-in-Progress)
const newCounterFiber = {
  type: Counter,
  props: { index: 'One' },
  stateNode: { count: 1 },        // New count
  alternate: currentCounterFiber  // Points back to current version
};

// Now currentCounterFiber.alternate = newCounterFiber (both point to each other)

// AFTER (Trees Swapped)
// newCounterFiber becomes the current tree
// The old current tree becomes the alternate
```

### Key Benefits

1. **Smooth Updates**: React can prepare changes without disrupting what's shown
2. **Can Pause/Resume**: If something urgent happens, React can pause the update
3. **Efficient Comparison**: React compares old vs new versions easily
4. **Rollback**: If something goes wrong, React can go back to the previous version

### The Big Picture

The alternate branch is React's secret to being fast and smooth:
- **Draft first, publish later**: Build complete changes before showing them
- **Compare efficiently**: Always know what changed by comparing alternates  
- **Stay responsive**: Can pause work if user needs immediate attention
- **Reliable updates**: Always have a fallback version if something breaks

This is why React feels so smooth compared to directly manipulating the DOM - it's like having a professional editor who prepares perfect drafts before publishing them!
 
## Hooks and State

### What is State?

Think of **state** as your component's memory. It's where React components remember things that can change over time.

### What are Hooks?

**Hooks** are special React functions that let you "hook into" React's features. They always start with `use` (like `useState`, `useEffect`).

Think of hooks like tools in a toolbox:
- `useState`: For remembering things (like count values)
- `useEffect`: For doing things when something changes (like updating the page title)
- `useContext`: For sharing data between components

### useState - The Memory Hook

`useState` is like giving your component a notepad to write things down and change them later.

### How useState Works

1. **Initial Value**: `useState(0)` starts the count at 0
2. **Current Value**: `count` holds the current number
3. **Update Function**: `setCount(newValue)` changes the number
4. **Re-render**: When state changes, React re-draws the component

```javascript
// Step by step what happens:
const [count, setCount] = useState(0);

// 1. First render: count = 0
// 2. User clicks button: setCount(1) is called
// 3. React re-renders: count = 1
// 4. User clicks again: setCount(2) is called  
// 5. React re-renders: count = 2
```

### Multiple State Values

You can have multiple pieces of state in one component:

```javascript
function Counter(props) {
  const [count, setCount] = useState(0);           // For the number
  const [isActive, setIsActive] = useState(false); // For active/inactive
  const [name, setName] = useState('Counter');     // For the title
  
  return (
    React.createElement('div', null,
      React.createElement('h2', null, name),
      React.createElement('p', null, `Count: ${count}`),
      React.createElement('p', null, isActive ? 'Active' : 'Inactive'),
      React.createElement('button', {
        onClick: () => setCount(count + 1)
      }, 'Increment')
    )
  );
}
```

### State Rules (Important!)

1. **Only in Function Components**: You can only use hooks in function components
2. **Top Level Only**: Don't put hooks inside loops, conditions, or nested functions
3. **Same Order**: Hooks must be called in the same order every time

```javascript
// ✅ Good - hooks at the top
function Counter() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('Counter');
  
  // Your component logic here...
}

// ❌ Bad - hook inside condition
function Counter() {
  if (someCondition) {
    const [count, setCount] = useState(0);  // Don't do this!
  }
}
```

### useEffect - The Side Effect Hook

`useEffect` lets you do things when your component mounts, updates, or unmounts.

#### Basic useEffect
```javascript
function Counter() {
  const [count, setCount] = useState(0);
  
  // This runs after every render
  useEffect(() => {
    document.title = `Count: ${count}`;  // Update page title
  });
  
  return /* your JSX */;
}
```

#### useEffect with Dependencies
```javascript
function Counter() {
  const [count, setCount] = useState(0);
  
  // This runs only when count changes
  useEffect(() => {
    console.log(`Count changed to: ${count}`);
  }, [count]);  // Only run when count changes
  
  return /* your JSX */;
}
```

#### useEffect for Setup/Cleanup
```javascript
function Counter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    // Setup: runs once when component mounts
    const timer = setInterval(() => {
      console.log('Timer tick');
    }, 1000);
    
    // Cleanup: runs when component unmounts
    return () => {
      clearInterval(timer);
    };
  }, []); // Empty array = run once only
  
  return /* your JSX */;
}
```

### Updating Your Project

To add state to your current Counter component, you would change:

```javascript
// FROM: Static counter (current code)
function Counter(props) {
  return (
    React.createElement('article', { className: 'container' },
      React.createElement('span', { className: 'outfit count' }, '0'),  // Always 0
      React.createElement('button', {
        onClick: () => console.log('Increment')  // Just logs
      }, 'Increment')
    )
  );
}

// TO: Dynamic counter with state
function Counter(props) {
  const [count, setCount] = React.useState(0);  // Add state
  
  return (
    React.createElement('article', { className: 'container' },
      React.createElement('span', { className: 'outfit count' }, count),  // Shows actual count
      React.createElement('button', {
        onClick: () => setCount(count + 1)  // Actually increments
      }, 'Increment')
    )
  );
}
```

### Common Patterns

#### Counter with Increment/Decrement
```javascript
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    React.createElement('div', null,
      React.createElement('button', {
        onClick: () => setCount(count - 1)
      }, '-'),
      React.createElement('span', null, count),
      React.createElement('button', {
        onClick: () => setCount(count + 1)
      }, '+')
    )
  );
}
```

#### Form Input State
```javascript
function NameForm() {
  const [name, setName] = useState('');
  
  return (
    React.createElement('form', null,
      React.createElement('input', {
        type: 'text',
        value: name,
        onChange: (e) => setName(e.target.value)
      }),
      React.createElement('p', null, `Hello, ${name}!`)
    )
  );
}
```

### Key Takeaways

1. **State = Memory**: Components can remember and change values
2. **useState = Basic Memory**: For simple values like numbers, strings, booleans
3. **useEffect = Side Effects**: For doing things when state changes
4. **Hooks Rules**: Always at the top level, same order every time
5. **Re-rendering**: When state changes, React re-draws your component

State and hooks transform your components from static displays into interactive, dynamic parts of your application. They're the foundation that makes React components truly powerful!

---

## React 19 Hooks Complete Reference

### 1. useState - State Management
**Purpose**: Store and update component data

```jsx
function UserProfile() {
  const [username, setUsername] = useState('');
  const [age, setAge] = useState(0);
  
  const handleSubmit = () => {
    console.log({ username, age });
  };
  
  return (
    <div>
      <input 
        value={username}
        onChange={(e) => setUsername(e.target.value)}
        placeholder="Enter name"
      />
      <button onClick={() => setAge(age + 1)}>
        Age: {age}
      </button>
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
}
```

---

### 2. useEffect - Side Effects & Lifecycle
**Purpose**: Handle side effects like data fetching, subscriptions, timers

```jsx
function ProfilePage({ userId }) {
  const [profile, setProfile] = useState(null);
  
  useEffect(() => {
    // Fetch on mount or when userId changes
    fetchUserProfile(userId).then(setProfile);
  }, [userId]);
  
  useEffect(() => {
    // Subscribe to updates
    const subscription = subscribeToUserUpdates(userId);
    
    // Cleanup on unmount
    return () => subscription.cancel();
  }, [userId]);
  
  return <div>{profile?.name}</div>;
}
```

---

### 3. useContext - Share Data Globally
**Purpose**: Access shared data without prop drilling

```jsx
const UserContext = createContext(null);

function App() {
  const [currentUser, setCurrentUser] = useState({ name: 'Alice', role: 'admin' });
  
  return (
    <UserContext.Provider value={currentUser}>
      <Dashboard />
    </UserContext.Provider>
  );
}

function Dashboard() {
  const user = useContext(UserContext);
  
  return <h1>Welcome, {user.name}!</h1>;
}
```

---

### 4. useReducer - Complex State Logic
**Purpose**: Manage state with reducer pattern for complex updates

```jsx
function shoppingCartReducer(cart, action) {
  switch (action.type) {
    case 'add_item':
      return [...cart, action.item];
    case 'remove_item':
      return cart.filter(item => item.id !== action.id);
    case 'clear_cart':
      return [];
    default:
      return cart;
  }
}

function ShoppingCart() {
  const [cart, dispatch] = useReducer(shoppingCartReducer, []);
  
  const addProduct = (product) => {
    dispatch({ type: 'add_item', item: product });
  };
  
  return (
    <div>
      <button onClick={() => addProduct({ id: 1, name: 'Book' })}>
        Add Book
      </button>
      <p>Items: {cart.length}</p>
    </div>
  );
}
```

---

### 5. useRef - Persist Values & Access DOM
**Purpose**: Reference DOM elements or persist values without triggering re-render

```jsx
function VideoPlayer() {
  const videoRef = useRef(null);
  const playCountRef = useRef(0);
  
  const handlePlay = () => {
    videoRef.current.play();
    playCountRef.current += 1;
    console.log(`Played ${playCountRef.current} times`);
  };
  
  return (
    <div>
      <video ref={videoRef} src="movie.mp4" />
      <button onClick={handlePlay}>Play Video</button>
    </div>
  );
}
```

---

### 6. useMemo - Cache Expensive Calculations
**Purpose**: Memoize computed values to avoid recalculation

```jsx
function ProductList({ products, searchTerm }) {
  const filteredProducts = useMemo(() => {
    console.log('Filtering products...');
    return products.filter(p => 
      p.name.toLowerCase().includes(searchTerm.toLowerCase())
    );
  }, [products, searchTerm]);
  
  return (
    <ul>
      {filteredProducts.map(p => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}
```

---

### 7. useCallback - Memoize Functions
**Purpose**: Cache function references to prevent unnecessary re-renders

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState('all');
  
  const addTodo = useCallback((text) => {
    setTodos(prev => [...prev, { id: Date.now(), text }]);
  }, []);
  
  const removeTodo = useCallback((id) => {
    setTodos(prev => prev.filter(t => t.id !== id));
  }, []);
  
  return (
    <div>
      <TodoForm onAdd={addTodo} />
      <TodoItems items={todos} onRemove={removeTodo} />
    </div>
  );
}
```

---

### 8. useLayoutEffect - Synchronous Effects
**Purpose**: Run effects synchronously before browser paint

```jsx
function Tooltip({ children }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const tooltipRef = useRef(null);
  
  useLayoutEffect(() => {
    // Measure DOM before paint
    const rect = tooltipRef.current.getBoundingClientRect();
    setPosition({ x: rect.left, y: rect.top - 40 });
  }, [children]);
  
  return (
    <div ref={tooltipRef} style={{ left: position.x, top: position.y }}>
      {children}
    </div>
  );
}
```

---

### 9. useImperativeHandle - Customize Ref Exposure
**Purpose**: Customize the instance value exposed through ref

```jsx
const CustomInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  
  useImperativeHandle(ref, () => ({
    focusInput: () => {
      inputRef.current.focus();
    },
    clearInput: () => {
      inputRef.current.value = '';
    },
    getValue: () => {
      return inputRef.current.value;
    }
  }));
  
  return <input ref={inputRef} {...props} />;
});

function Form() {
  const inputRef = useRef();
  
  return (
    <div>
      <CustomInput ref={inputRef} />
      <button onClick={() => inputRef.current.focusInput()}>
        Focus
      </button>
      <button onClick={() => inputRef.current.clearInput()}>
        Clear
      </button>
    </div>
  );
}
```

---

### 10. useDebugValue - DevTools Label
**Purpose**: Display custom hook label in React DevTools

```jsx
function useNetworkStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  
  useDebugValue(isOnline ? 'Connected' : 'Disconnected');
  
  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);
    
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  
  return isOnline;
}
```

---

### 11. useTransition (React 18+) - Mark Updates as Non-Urgent
**Purpose**: Mark state updates as non-urgent to keep UI responsive

```jsx
function SearchResults() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();
  
  const handleSearch = (value) => {
    setQuery(value);
    
    // Mark expensive update as transition
    startTransition(() => {
      const filtered = expensiveFilterFunction(value);
      setResults(filtered);
    });
  };
  
  return (
    <div>
      <input value={query} onChange={(e) => handleSearch(e.target.value)} />
      {isPending && <p>Searching...</p>}
      <ul>
        {results.map(r => <li key={r.id}>{r.name}</li>)}
      </ul>
    </div>
  );
}
```

---

### 12. useDeferredValue (React 18+) - Defer Value Updates
**Purpose**: Defer updating a value to keep UI responsive

```jsx
function ProductGrid({ products }) {
  const [searchTerm, setSearchTerm] = useState('');
  const deferredSearchTerm = useDeferredValue(searchTerm);
  
  // Expensive filtering with deferred value
  const filteredProducts = useMemo(() => {
    return products.filter(p =>
      p.name.toLowerCase().includes(deferredSearchTerm.toLowerCase())
    );
  }, [products, deferredSearchTerm]);
  
  return (
    <div>
      <input 
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search..."
      />
      <div className="grid">
        {filteredProducts.map(p => (
          <ProductCard key={p.id} product={p} />
        ))}
      </div>
    </div>
  );
}
```

---

### 13. useId (React 18+) - Generate Unique IDs
**Purpose**: Generate stable unique IDs for accessibility

```jsx
function FormField({ label }) {
  const id = useId();
  
  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input id={id} type="text" />
    </div>
  );
}

function RegistrationForm() {
  return (
    <form>
      <FormField label="Username" />
      <FormField label="Email" />
      <FormField label="Password" />
    </form>
  );
}
```

---

### 14. useSyncExternalStore (React 18+) - Subscribe to External Stores
**Purpose**: Subscribe to external data stores

```jsx
function useBrowserWidth() {
  const width = useSyncExternalStore(
    (callback) => {
      window.addEventListener('resize', callback);
      return () => window.removeEventListener('resize', callback);
    },
    () => window.innerWidth,
    () => 0 // Server-side fallback
  );
  
  return width;
}

function ResponsiveComponent() {
  const width = useBrowserWidth();
  
  return (
    <div>
      <p>Window width: {width}px</p>
      {width < 768 ? <MobileView /> : <DesktopView />}
    </div>
  );
}
```

---

### 15. useInsertionEffect (React 18+) - CSS-in-JS Libraries
**Purpose**: Insert styles before DOM mutations (for CSS-in-JS)

```jsx
function useCSS(rule) {
  useInsertionEffect(() => {
    const style = document.createElement('style');
    style.textContent = rule;
    document.head.appendChild(style);
    
    return () => {
      document.head.removeChild(style);
    };
  }, [rule]);
}

function StyledComponent() {
  useCSS('.dynamic-class { color: red; }');
  
  return <div className="dynamic-class">Styled text</div>;
}
```

---

### 16. use (React 19) - Read Resources
**Purpose**: Read promises and context directly in render

```jsx
function UserProfile({ userPromise }) {
  // React 19 feature - suspends until promise resolves
  const user = use(userPromise);
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}

function App() {
  const userPromise = fetchUser(123);
  
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

---

### 17. useOptimistic (React 19) - Optimistic Updates
**Purpose**: Show optimistic state while async action completes

```jsx
function TodoList({ todos }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (state, newTodo) => [...state, newTodo]
  );
  
  const handleAdd = async (text) => {
    const tempTodo = { id: 'temp', text, pending: true };
    addOptimisticTodo(tempTodo);
    
    await addTodoToServer(text);
  };
  
  return (
    <ul>
      {optimisticTodos.map(todo => (
        <li key={todo.id} style={{ opacity: todo.pending ? 0.5 : 1 }}>
          {todo.text}
        </li>
      ))}
    </ul>
  );
}
```

---

### 18. useActionState (React 19) - Form Actions with State
**Purpose**: Manage form actions with built-in state

```jsx
function ContactForm() {
  const [state, submitAction, isPending] = useActionState(
    async (previousState, formData) => {
      const name = formData.get('name');
      const email = formData.get('email');
      
      try {
        await sendContactEmail({ name, email });
        return { success: true, message: 'Sent!' };
      } catch (error) {
        return { success: false, message: 'Failed!' };
      }
    },
    { success: null, message: '' }
  );
  
  return (
    <form action={submitAction}>
      <input name="name" required />
      <input name="email" type="email" required />
      <button disabled={isPending}>
        {isPending ? 'Sending...' : 'Send'}
      </button>
      {state.message && <p>{state.message}</p>}
    </form>
  );
}
```

---

## Custom Hooks

Custom hooks let you extract and reuse component logic across components.

### Rules for Custom Hooks

1. **Name must start with "use"**: `useCustomHook`
2. **Can call other hooks**: Follow all hooks rules
3. **Return anything**: Values, objects, arrays, functions
4. **Reusable logic**: Extract common patterns

### Basic Custom Hook Pattern

```jsx
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  
  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  const reset = () => setCount(initialValue);
  
  return { count, increment, decrement, reset };
}

// Usage
function CounterApp() {
  const counter = useCounter(10);
  
  return (
    <div>
      <p>Count: {counter.count}</p>
      <button onClick={counter.increment}>+</button>
      <button onClick={counter.decrement}>-</button>
      <button onClick={counter.reset}>Reset</button>
    </div>
  );
}
```

---

### useLocalStorage - Persist State

```jsx
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });
  
  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };
  
  return [storedValue, setValue];
}

// Usage
function PreferencesPanel() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [fontSize, setFontSize] = useLocalStorage('fontSize', 16);
  
  return (
    <div>
      <select value={theme} onChange={(e) => setTheme(e.target.value)}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
      </select>
      <input 
        type="number" 
        value={fontSize}
        onChange={(e) => setFontSize(Number(e.target.value))}
      />
    </div>
  );
}
```

---

### useFetch - Data Fetching

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let cancelled = false;
    
    setLoading(true);
    fetch(url)
      .then(res => res.json())
      .then(data => {
        if (!cancelled) {
          setData(data);
          setError(null);
        }
      })
      .catch(err => {
        if (!cancelled) {
          setError(err);
        }
      })
      .finally(() => {
        if (!cancelled) {
          setLoading(false);
        }
      });
    
    return () => {
      cancelled = true;
    };
  }, [url]);
  
  return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { data, loading, error } = useFetch(`/api/users/${userId}`);
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  
  return (
    <div>
      <h2>{data.name}</h2>
      <p>{data.email}</p>
    </div>
  );
}
```

---

### useToggle - Boolean State Management

```jsx
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  
  const toggle = useCallback(() => {
    setValue(v => !v);
  }, []);
  
  const setTrue = useCallback(() => {
    setValue(true);
  }, []);
  
  const setFalse = useCallback(() => {
    setValue(false);
  }, []);
  
  return [value, { toggle, setTrue, setFalse }];
}

// Usage
function Sidebar() {
  const [isOpen, { toggle, setTrue, setFalse }] = useToggle(false);
  
  return (
    <div>
      <button onClick={toggle}>Toggle Sidebar</button>
      <button onClick={setTrue}>Open</button>
      <button onClick={setFalse}>Close</button>
      {isOpen && <div className="sidebar">Sidebar Content</div>}
    </div>
  );
}
```

---

### useDebounce - Debounced Values

```jsx
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);
  
  return debouncedValue;
}

// Usage
function SearchBox() {
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearchTerm = useDebounce(searchTerm, 500);
  const [results, setResults] = useState([]);
  
  useEffect(() => {
    if (debouncedSearchTerm) {
      searchAPI(debouncedSearchTerm).then(setResults);
    }
  }, [debouncedSearchTerm]);
  
  return (
    <div>
      <input 
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search..."
      />
      <ul>
        {results.map(r => <li key={r.id}>{r.name}</li>)}
      </ul>
    </div>
  );
}
```

---

### usePrevious - Access Previous Value

```jsx
function usePrevious(value) {
  const ref = useRef();
  
  useEffect(() => {
    ref.current = value;
  }, [value]);
  
  return ref.current;
}

// Usage
function Counter() {
  const [count, setCount] = useState(0);
  const previousCount = usePrevious(count);
  
  return (
    <div>
      <p>Current: {count}</p>
      <p>Previous: {previousCount}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

---

### useInterval - Declarative Intervals

```jsx
function useInterval(callback, delay) {
  const savedCallback = useRef();
  
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);
  
  useEffect(() => {
    if (delay !== null) {
      const id = setInterval(() => savedCallback.current(), delay);
      return () => clearInterval(id);
    }
  }, [delay]);
}

// Usage
function Timer() {
  const [count, setCount] = useState(0);
  const [isRunning, setIsRunning] = useState(true);
  
  useInterval(() => {
    setCount(count + 1);
  }, isRunning ? 1000 : null);
  
  return (
    <div>
      <p>Timer: {count}s</p>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? 'Pause' : 'Start'}
      </button>
    </div>
  );
}
```

---

### useMediaQuery - Responsive Hooks

```jsx
function useMediaQuery(query) {
  const [matches, setMatches] = useState(false);
  
  useEffect(() => {
    const media = window.matchMedia(query);
    
    if (media.matches !== matches) {
      setMatches(media.matches);
    }
    
    const listener = () => setMatches(media.matches);
    media.addEventListener('change', listener);
    
    return () => media.removeEventListener('change', listener);
  }, [matches, query]);
  
  return matches;
}

// Usage
function ResponsiveLayout() {
  const isMobile = useMediaQuery('(max-width: 768px)');
  const isTablet = useMediaQuery('(min-width: 769px) and (max-width: 1024px)');
  const isDesktop = useMediaQuery('(min-width: 1025px)');
  
  return (
    <div>
      {isMobile && <MobileLayout />}
      {isTablet && <TabletLayout />}
      {isDesktop && <DesktopLayout />}
    </div>
  );
}
```

---

### Custom Hook Best Practices

1. **Single Responsibility**: Each hook should do one thing well
2. **Descriptive Names**: Clear names that describe what the hook does
3. **Return Consistent Values**: Object or array, but be consistent
4. **Handle Cleanup**: Always clean up subscriptions and timers
5. **Dependencies**: Be explicit about dependencies in useEffect
6. **Composability**: Custom hooks can use other custom hooks

```jsx
// Good: Focused, composable custom hook
function useAuth() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const unsubscribe = auth.onAuthStateChanged(user => {
      setUser(user);
      setLoading(false);
    });
    
    return unsubscribe;
  }, []);
  
  const login = useCallback(async (email, password) => {
    await auth.signIn(email, password);
  }, []);
  
  const logout = useCallback(async () => {
    await auth.signOut();
  }, []);
  
  return { user, loading, login, logout };
}
```

These hooks and patterns give you a complete toolkit for building React 19 applications efficiently and maintaining clean, reusable code!