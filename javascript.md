# Javascript

ECMAScript (ES) is the standardized specification that defines the syntax, semantics, and core features of scripting languages. It's maintained by Ecma International and serves as the blueprint for JavaScript implementations. Think of ECMAScript as the "recipe" or "rulebook" that tells programming language implementations what features they should have and how they should behave.
JavaScript is the most popular implementation of the ECMAScript specification. It's the actual programming language that runs in browsers, Node.js, and other environments

## Lexical environment and Execution context

- **Lexical Environment:** It's just a fancy term which means where your code sits. The environment where the code sits. For Example:
```javascript
// Global Lexical Environment
var globalVar = "I'm global";

function outerFunction() {
  // outerFunction's Lexical Environment
  var outerVar = "I'm in outer function";
  
  function innerFunction() {
    // innerFunction's Lexical Environment
    var innerVar = "I'm in inner function";
  }
  
  innerFunction();
}
```

- **Execution context:** It's just a wrapper around your code which is built by the javascript compiler. It has all the information neccessary to run a piece of code. It can be global, created when a function is called, or when `eval()` function is called. When it is created, a `this` variable is created. It is initially assigned as window object in global execution context.

## Hoisting and Temporal dead zone

During the creation phase of an execution context, JavaScript:
1. Scans through the code for variable and function declarations
2. Allocates memory for these identifiers
3. Initializes them with default values, which is undefined for variables and `f` for functions (stored as code).

There are 3 ways to declare a variable in javascript: `var`, `let` and `const`. For `let` and `const`, hoisting happens, but the compiler denies access to the memory location of these variables unless in the same scope of those variables. This restricted access is called temporal dead zone.

How the variables get their value can also be determined based on the lexical environment of the variable and code. For example:
```javascript

// Example one
var a = 1;

function otherFoo() {
  console.log(a); // logs 1 since lexical environment is global context
}

function foo() {
  var a = 2;
  otherFoo();
}

foo();


// Example 2:
var a = 1;

function foo() {
  function otherFoo() {
    console.log(a); // logs 2 since lexical environment is functional (foo) context
  }

  var a = 2;
  otherFoo();
}

foo();
```

One more pitfall while working with JS can be coercion. This is when the JS engine converts a type to another to execute an operator, for example:
```js
// You would expect this to be false, but it actually computes to true
var a = 3 < 2 < 1;
/**
 * The reason for above behavior is less than is left to right operator, which means 3 < 2 is executed first, so intermediate value:
 * var a = false < 1;
 * Here false is converted to 0, and 0 < 1 is true
 */ 
```

## Functions and Objects

Javascript has **first class functions**, which means functions are treated like any other value. You can:
- Assign functions to variables
- Pass functions as arguments to other functions
- Return functions from other functions
- Store functions in data structures (arrays, objects)

### Built-in Function Properties:

In JavaScript, functions are objects, which means they have built-in properties that provide useful information about the function itself:

```javascript
function calculateSum(a, b, c) {
    return a + b + c;
}

// name - Returns the function's name as a string
console.log(calculateSum.name); // "calculateSum"

// length - Returns the number of formal parameters (excluding rest parameters)
console.log(calculateSum.length); // 3

// prototype - References the function's prototype object (for constructor functions)
console.log(calculateSum.prototype); // {}

// toString() - Returns the function's source code as a string
console.log(calculateSum.toString()); 
// "function calculateSum(a, b, c) { return a + b + c; }"
```

These properties are automatically available on every function and are useful for reflection, debugging, and metaprogramming techniques.

### Functions and `this`

The value of `this` is dependent on where the function sits. If function is normally written in a JS file inside the function `this` points to the global window object. If function is declared inside an object, it points to the object. But if there are nested functions, it points to global window object again. Example:
```javascript

function a() {
  console.log(this); // points to window
}

var b =  {
  name: 'This is the b object.',
  foo: function () {
    console.log(this) // points to b
  }
}

b.foo();

var c = {
  name: 'This is the c object.',
  foo: function () {
    // var self = this; // use self everywhere inside instead of this to avoid problems
    console.log(this); // points to b

    function innerFoo() {
      console.log(this); // points to window
    }

    innerFoo()
  }
}

c.foo();
```

### Immediately invoked function expressions (IIFE)

 It's a JavaScript design pattern that allows you to execute a function immediately after it's created, without storing it in a variable or calling it later. It gives some capabilities and are useful because:
 - Avoid Global Pollution: Creates a private scope, preventing variables from polluting the global namespace
 - Module Pattern: Helps create modules with private and public methods
 - One-time Initialization: Perfect for code that needs to run once when the page loads
 - Variable Privacy: Keeps variables private within the function scope

Example:
```javascript
var myModule = (function() {
  var privateCounter = 0;
  
  // Private function
  function privateFunction() {
    console.log("This is private");
  }
    
  // Return public interface
  return {
    increment: function() {
      privateCounter++;
    },
    getCount: function() {
      return privateCounter;
    },
    reset: function() {
      privateCounter = 0;
    }
  };
})();

myModule.increment();
myModule.increment();
console.log(myModule.getCount()); // 2
```

### Closures

A closure is a function that has access to variables from its outer (enclosing) lexical scope even after the outer function has finished executing. In simpler terms, a closure gives you access to an outer function's scope from an inner function.

```javascript
function buildFunctions() {
  let arr = [];

  for(var i = 0; i < 3; i++) {
    arr.push(function () {
      console.log(i)
    });
  }

  return arr;
}

var fns = buildFunctions();
fns[0]();
fns[1]();
fns[2]();

// logs 3, 3, 3
// To avoid this IIFC can be used:
function buildFunctions2() {
  let arr = [];

  for(var i = 0; i < 3; i++) {
    (function (j) {
      arr.push(function () {
        console.log(j)
      });
    })(i);
  }

  return arr;
}
```

### Function currying

Function object has some methods like `call`, `apply` and `bind` which are used to pass context to the function. These can be used to make other functions with some binded parameters. Example:

```javascript
function multiply(a, b) {
  return a * b;
}

// creates a new function with first param set as 2 permenantly
var multiplyByTwo = multiply.bind(this, 2);
console.log(multiplyByTwo(5)); // gives 10
```

### Functional programming

Functional Programming (FP) is a programming paradigm that treats computation as the evaluation of mathematical functions. It emphasizes writing programs using pure functions, avoiding shared state, mutable data, and side effects.

```javascript
function myMap (arr, fn) {
  const result = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(fn(arr[i], i, arr));
  }
  return result;
}

var arr = [1, 2, 3, 4, 5];
var foo = (item, value) => item * value;
var ans = myMap(arr, foo.bind(2));
console.log(ans); 
```

### Object oriented javascript

This section deals with how objects are created in javascript. It uses a concept called Prototypal inheritence. It implements by using a prototype property which is common across all objects.
When we search for a property in any object, it first looks for it in the object properties. If it doesn't find it, js starts looking for it in the prototype of that object (denoted by `__proto__`), and it keeps on checking down the prototype chain. Example"

```javascript
var defaultName = {
  firstname: 'default',
  lastname: 'default',
  getFullName: function () {
    return `${this.firstname} ${this.lastname}`;
  }
}

var john = {
  firstname: 'John',
  lastname: 'Doe',
}

john.__proto__ = defaultName;

console.log(john.getFullName());
```

#### Building objects

**Function Constructor**

This came into use to attract JAVA developers, who were used to creating objects with the `new` keyword. Hence javascript added the support to that and classes. 
When `new` is used, it creates an empty object, sends it to the function as `this` and then the assignments inside get attached to that object. Hence the variable gets those properties.
All functions have a prototype property. They become useful in case of `new` keyword. You can add keys and methods to that which then adds that to every object created from that function constructor.

```js
function Person(firstname, lastname) {
  this.firstname = firstname || 'defaultFirstname';
  this.lastname = lastname || 'defaultLastname';
}

Person.prototype.getFullName = function() {
    return `${this.firstname} ${this.lastname}`;
  }

const person = new Person();
console.log(person.getFullName());
```

- If something is returned from the function constructor, then that thing becomes the value of the variable.
- Another way to create an object is `Object.create()`, the argument passed to create becomes the prototype of the object. 
- Latest way to create an object is `class`, in that case to set the prototype you can use `extends` keyword.

## Javascript questions / FAQs

### Debounce

**Debounce** is a technique that limits how often a function can be executed. It ensures a function only runs after a specified delay period has passed since its last call. If called again before the delay ends, the timer resets.

```javascript
function debounce(func, delay) {
    let timeoutId;
    
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}

// Example: Search input that only triggers after user stops typing
const searchInput = document.getElementById('search');
const debouncedSearch = debounce((query) => {
    console.log('Searching for:', query);
    // API call here
}, 300);

searchInput.addEventListener('input', (e) => {
    debouncedSearch(e.target.value);
});
```

### Throttle

**Throttle** limits function execution to at most once per specified time interval. Unlike debounce, it executes the function at regular intervals during continuous calls.

```javascript
function throttle(func, delay) {
    let lastCall = 0;
    
    return function(...args) {
        const now = Date.now();
        if (now - lastCall >= delay) {
            lastCall = now;
            func.apply(this, args);
        }
    };
}

// Example: Scroll event that only triggers every 100ms
const throttledScroll = throttle(() => {
    console.log('Scroll position:', window.scrollY);
}, 100);

window.addEventListener('scroll', throttledScroll);
```

### Memoization

**Memoization** is a caching technique that stores the results of expensive function calls. When the function is called again with the same arguments, it returns the cached result instead of recalculating.

```javascript
function memoize(func) {
    const cache = {};
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (key in cache) {
            return cache[key];
        }
        
        const result = func.apply(this, args);
        cache[key] = result;
        return result;
    };
}

// Example: Expensive fibonacci calculation with memoization
const fibonacci = memoize((n) => {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(40)); // Fast after first calculation
```

## Event Loop
The Event Loop is the mechanism that allows JavaScript to perform non-blocking operations despite being single-threaded. It's responsible for executing code, collecting and processing events, and executing queued sub-tasks.
Components of the Event Loop System:
1. Call Stack
  - Where function calls are stacked
  - Follows LIFO (Last In, First Out) principle
  - When a function is called, it's pushed onto the stack
  - When a function returns, it's popped from the stack

2. Web APIs / Node.js APIs
  - Browser provides APIs like setTimeout, DOM events, fetch
  - Node.js provides APIs like fs, http, timers
  - These run outside the main JavaScript thread

3. Callback Queue (Task Queue)
  - Where callbacks from completed async operations wait
  - Follows FIFO (First In, First Out) principle

4. Microtask Queue
  - Higher priority queue for Promise callbacks and queueMicrotask
  - Always processed before the Callback Queue

**Event Loop Algorithm:**
1. Execute synchronous code from Call Stack
2. When Call Stack is empty:
  - Process all Microtasks (Promises, queueMicrotask)
  - Process one Macrotask (setTimeout, setInterval, I/O)
  - Repeat the cycle

## WebSockets

**WebSockets** provide full-duplex communication between client and server over a single TCP connection. Unlike HTTP requests, WebSockets maintain a persistent connection allowing real-time bidirectional data exchange.

```javascript
// Client-side WebSocket implementation
const socket = new WebSocket('ws://localhost:8080');

// Connection opened
socket.onopen = function(event) {
    console.log('Connected to WebSocket server');
    socket.send('Hello Server!');
};

// Receive messages from server
socket.onmessage = function(event) {
    console.log('Message from server:', event.data);
};

// Handle connection close
socket.onclose = function(event) {
    console.log('WebSocket connection closed');
};

// Handle errors
socket.onerror = function(error) {
    console.error('WebSocket error:', error);
};

// Send message to server
function sendMessage(message) {
    if (socket.readyState === WebSocket.OPEN) {
        socket.send(message);
    }
}
```

## Web and Service Workers

**Web Workers** and **Service Workers** are JavaScript APIs that allow running scripts in background threads, separate from the main UI thread. This enables non-blocking operations and better performance.

### Web Workers

Web Workers run JavaScript in background threads, allowing CPU-intensive tasks without blocking the main thread.

#### Types of Web Workers:

1. **Dedicated Workers**: Private to the script that created them
2. **Shared Workers**: Can be accessed by multiple scripts
3. **Service Workers**: Act as proxy between web app and network

#### Creating a Web Worker:

**Main Thread (main.js):**
```javascript
// Check if Web Workers are supported
if (typeof Worker !== 'undefined') {
  // Create a new worker
  const worker = new Worker('worker.js');
  
  // Send data to worker
  worker.postMessage({
    command: 'calculate',
    data: [1, 2, 3, 4, 5]
  });
  
  // Listen for messages from worker
  worker.onmessage = function(event) {
    console.log('Result from worker:', event.data);
    document.getElementById('result').textContent = event.data;
  };
  
  // Handle worker errors
  worker.onerror = function(error) {
    console.error('Worker error:', error);
  };
  
  // Terminate worker when done
  // worker.terminate();
} else {
  console.log('Web Workers not supported');
}
```

**Worker Thread (worker.js):**
```javascript
// Listen for messages from main thread
self.onmessage = function(event) {
  const { command, data } = event.data;
  
  if (command === 'calculate') {
    // Perform CPU-intensive task
    const result = heavyCalculation(data);
    
    // Send result back to main thread
    self.postMessage(result);
  }
};

function heavyCalculation(numbers) {
  // Simulate heavy computation
  let sum = 0;
  for (let i = 0; i < 1000000; i++) {
    sum += numbers.reduce((acc, num) => acc + num, 0);
  }
  return sum;
}

// Import external scripts (if needed)
// importScripts('utils.js', 'math-library.js');
```

#### Practical Web Worker Example:

**Image Processing Worker:**
```javascript
// Main thread - image-processor.js
function processImage(imageData) {
  const worker = new Worker('image-worker.js');
  
  return new Promise((resolve, reject) => {
    worker.postMessage({
        imageData: imageData,
        filter: 'blur',
        intensity: 5
    });
    
    worker.onmessage = (event) => {
      resolve(event.data.processedImageData);
      worker.terminate();
    };
    
    worker.onerror = reject;
  });
}

// Usage
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

processImage(imageData).then(processedData => {
  ctx.putImageData(processedData, 0, 0);
});
```

### Service Workers

**Service Workers** act as a proxy between your web app and the network, enabling features like offline functionality, push notifications, and background sync.

#### **Service Worker Lifecycle:**

1. **Registration**: Register the service worker
2. **Installation**: Download and install the worker
3. **Activation**: Activate and take control
4. **Fetch Events**: Intercept network requests

#### **Registering a Service Worker:**

**Main Thread:**
```javascript
// Register service worker
if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker.register('/sw.js')
      .then((registration) => {
        console.log('SW registered successfully:', registration.scope);    
        // Listen for updates
        registration.addEventListener('updatefound', () => {
          const newWorker = registration.installing;
          newWorker.addEventListener('statechange', () => {
            if (newWorker.state === 'installed') {
                if (navigator.serviceWorker.controller) {
                    // New update available
                    showUpdateAvailable();
                }
            }
          });
        });
    }).catch((error) => {
      console.log('SW registration failed:', error);
    });
  });
} else {
  console.log('Service Workers not supported');
}
```

#### Service Worker Implementation (sw.js):

```javascript
const CACHE_NAME = 'my-app-v1';
const urlsToCache = [
    '/',
    '/index.html',
    '/styles.css',
    '/app.js',
    '/offline.html'
];

// Install event - cache resources
self.addEventListener('install', (event) => {
    console.log('Service Worker installing');
    
    event.waitUntil(
        caches.open(CACHE_NAME)
            .then((cache) => {
                console.log('Caching app shell');
                return cache.addAll(urlsToCache);
            })
    );
});

// Activate event - clean up old caches
self.addEventListener('activate', (event) => {
    console.log('Service Worker activated');
    
    event.waitUntil(
        caches.keys().then((cacheNames) => {
            return Promise.all(
                cacheNames.map((cacheName) => {
                    if (cacheName !== CACHE_NAME) {
                        console.log('Deleting old cache:', cacheName);
                        return caches.delete(cacheName);
                    }
                })
            );
        })
    );
});

// Fetch event - serve from cache or network
self.addEventListener('fetch', (event) => {
    event.respondWith(
        caches.match(event.request)
            .then((response) => {
                // Return cached version or fetch from network
                if (response) {
                    return response;
                }
                
                return fetch(event.request).then((response) => {
                    // Don't cache invalid responses
                    if (!response || response.status !== 200 || response.type !== 'basic') {
                        return response;
                    }
                    
                    // Clone response for caching
                    const responseToCache = response.clone();
                    
                    caches.open(CACHE_NAME)
                        .then((cache) => {
                            cache.put(event.request, responseToCache);
                        });
                    
                    return response;
                });
            })
            .catch(() => {
                // Show offline page for navigation requests
                if (event.request.destination === 'document') {
                    return caches.match('/offline.html');
                }
            })
    );
});
```

### Comparison: Web Workers vs Service Workers

| Feature | Web Workers | Service Workers |
|---------|-------------|-----------------|
| **Purpose** | CPU-intensive tasks | Network proxy, offline functionality |
| **Scope** | Single page/tab | Entire origin |
| **Lifetime** | Tied to page | Persistent across sessions |
| **Network Access** | Limited | Full control over requests |
| **DOM Access** | No | No |
| **Use Cases** | Heavy calculations, image processing | Caching, push notifications, offline |

### Shared Workers

**Shared Workers** can be accessed by multiple scripts, tabs, or windows from the same origin:

**Creating Shared Worker:**
```javascript
// Main thread
const sharedWorker = new SharedWorker('shared-worker.js');
const port = sharedWorker.port;

port.start();

port.postMessage({
    command: 'increment',
    value: 1
});

port.onmessage = (event) => {
    console.log('Shared worker response:', event.data);
};
```

**Shared Worker Implementation:**
```javascript
// shared-worker.js
let connections = [];
let counter = 0;

self.addEventListener('connect', (event) => {
    const port = event.ports[0];
    connections.push(port);
    
    port.addEventListener('message', (event) => {
        const { command, value } = event.data;
        
        if (command === 'increment') {
            counter += value;
            
            // Broadcast to all connections
            connections.forEach(connection => {
                connection.postMessage({
                    counter: counter
                });
            });
        }
    });
    
    port.start();
});
```

### Best Practices

**Web Workers:**
- Use for CPU-intensive tasks (calculations, data processing)
- Keep worker files small and focused
- Handle errors gracefully
- Terminate workers when no longer needed

**Service Workers:**
- Implement proper caching strategies
- Handle offline scenarios
- Keep service worker file updated
- Test across different network conditions
- Use HTTPS in production (required for service workers)

**Common Pitfalls:**
```javascript
// Don't do this in workers
// Workers can't access DOM
// document.getElementById('myElement'); // Error!

// Don't block the main thread
// Heavy synchronous operations should go in workers

// Proper error handling
worker.onerror = (error) => {
  console.error('Worker error:', error.filename, error.lineno, error.message);
};
```

Web Workers and Service Workers enable powerful background processing and offline capabilities, making web applications more performant and resilient.

## Push and Pull Notifications

**Push and Pull notifications** are two different approaches for delivering updates to users. They differ in who initiates the communication and when data is delivered.

### Pull Notifications (Polling)

**Pull notifications** require the client to actively request updates from the server at regular intervals.


### Push Notifications

Push notifications are initiated by the server and delivered to the client without the client requesting them.

### Comparison: Push vs Pull

| Aspect | Pull Notifications | Push Notifications |
|--------|-------------------|-------------------|
| **Initiation** | Client requests updates | Server sends updates |
| **Timing** | Scheduled intervals | Real-time when events occur |
| **Resource Usage** | Higher (frequent requests) | Lower (event-driven) |
| **Latency** | Higher (polling interval) | Lower (immediate) |
| **Complexity** | Simpler to implement | More complex setup |
| **Battery Impact** | Higher (mobile) | Lower (mobile) |
| **Offline Support** | No | Yes (with service workers) |

### When to Use Each:

**Pull Notifications (Polling):**
- Simple applications with infrequent updates
- When real-time updates aren't critical
- Legacy systems without push support
- When you need to control update frequency

**Push Notifications:**
- Real-time applications (chat, alerts, news)
- Mobile applications (battery efficiency)
- When immediate user engagement is needed
- Progressive Web Apps (PWAs)

Both approaches have their place in modern web development, and the choice depends on your application's requirements for real-time communication, resource efficiency, and user experience.