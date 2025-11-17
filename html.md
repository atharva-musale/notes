# Hyper Text Markup Language (HTML)

**HTML (HyperText Markup Language)** is the standard markup language used to create web pages. It describes the structure and content of web documents using elements represented by tags.

## What is HTML?

HTML is **not a programming language** - it's a **markup language** that:
- Defines the structure and content of web pages
- Uses tags to mark up different types of content
- Provides semantic meaning to content (headings, paragraphs, links, etc.)
- Works alongside CSS (styling) and JavaScript (functionality)

## How HTML Works

HTML documents are text files that browsers interpret and render as web pages. The browser reads HTML tags and converts them into visual elements.

### Basic HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
</head>
<body>
    <h1>Main Heading</h1>
    <p>This is a paragraph of text.</p>
</body>
</html>
```

## HTML Attributes

Attributes provide additional information about elements:

```html
<!-- Common attributes -->
<div id="unique-id" class="css-class" data-value="custom">Content</div>

<!-- Image attributes -->
<img src="path/to/image.jpg" alt="Description" title="Tooltip">

<!-- Link attributes -->
<a href="https://example.com" target="_blank" rel="noopener">Link</a>

<!-- Form input attributes -->
<input type="text" name="username" placeholder="Enter username" required>
```

## Semantic HTML

Semantic elements provide meaning to content structure:

```html
<header>
    <nav>
        <ul>
            <li><a href="#home">Home</a></li>
            <li><a href="#about">About</a></li>
        </ul>
    </nav>
</header>

<main>
    <article>
        <section>
            <h2>Article Title</h2>
            <p>Article content...</p>
        </section>
    </article>
    
    <aside>
        <p>Sidebar content</p>
    </aside>
</main>

<footer>
    <p>&copy; 2025 Website Name</p>
</footer>
```

## HTML Document Structure

### **DOCTYPE Declaration:**
```html
<!DOCTYPE html>  <!-- HTML5 declaration -->
```

### **HTML Element:**
```html
<html lang="en">  <!-- Root element with language -->
```

### **Head Section:**
```html
<head>
    <meta charset="UTF-8">                    <!-- Character encoding -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Page description">
    <title>Page Title</title>                <!-- Browser tab title -->
    <link rel="stylesheet" href="style.css"> <!-- External CSS -->
    <script src="script.js"></script>        <!-- External JavaScript -->
</head>
```

## How Browsers Process HTML

1. **Parsing**: Browser reads HTML from top to bottom
2. **DOM Creation**: Creates Document Object Model (DOM) tree
3. **Rendering**: Converts DOM into visual representation
4. **Layout**: Calculates element positions and sizes
5. **Painting**: Draws elements on screen

## HTML Best Practices

### **1. Use Semantic Elements:**
```html
<!-- Good - semantic meaning -->
<nav>Navigation menu</nav>
<article>Main content</article>

<!-- Avoid - no semantic meaning -->
<div class="navigation">Navigation menu</div>
<div class="content">Main content</div>
```

### **2. Proper Nesting:**
```html
<!-- Correct nesting -->
<ul>
    <li><a href="#">Link</a></li>
</ul>

<!-- Incorrect nesting -->
<ul>
    <a href="#"><li>Link</li></a>  <!-- Wrong! -->
</ul>
```

### **3. Accessibility:**
```html
<!-- Use alt text for images -->
<img src="chart.png" alt="Sales increased by 25% in Q4">

<!-- Use labels for form inputs -->
<label for="email">Email Address:</label>
<input type="email" id="email" name="email">

<!-- Use heading hierarchy -->
<h1>Main Title</h1>
    <h2>Section Title</h2>
        <h3>Subsection Title</h3>
```

### **4. Validation:**
```html
<!-- Use proper HTML5 input types -->
<input type="email" name="email">      <!-- Email validation -->
<input type="tel" name="phone">        <!-- Phone number -->
<input type="date" name="birthday">    <!-- Date picker -->
<input type="number" min="1" max="10"> <!-- Number with range -->
```

## Key Concepts

- **Elements**: Building blocks of HTML (paragraphs, headings, links)
- **Tags**: Markup that defines elements (`<p>`, `</p>`)
- **Attributes**: Additional properties of elements (`id`, `class`, `src`)
- **Nesting**: Elements can contain other elements
- **Hierarchy**: Document structure with parent-child relationships
- **Semantic Meaning**: Using appropriate elements for content type

## Frequently asked questions

### 1. What's the difference between HTML elements and tags?

**Tags** are the markup syntax used to define elements, while **elements** are the actual components of the document.

- **Tag**: The markup code like `<p>` (opening tag) and `</p>` (closing tag)
- **Element**: The complete structure including opening tag, content, and closing tag: `<p>This is content</p>`

### 2. What do `DOCTYPE` and character encoding declarations do?

**DOCTYPE Declaration (`<!DOCTYPE html>`):**
- Tells the browser which version of HTML to expect
- Ensures the browser renders in standards mode (not quirks mode)
- HTML5 uses the simple `<!DOCTYPE html>` declaration
- Must be the very first line in an HTML document

**Character Encoding (`<meta charset="UTF-8">`):**
- Specifies how text characters are encoded and decoded
- UTF-8 supports virtually all characters and symbols worldwide
- Prevents character display issues (like question marks or boxes)
- Should be placed early in the `<head>` section

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <!-- Other head content -->
</head>
```

### 3. How does the browser render a HTML page?

Browser rendering follows these steps:

1. **HTML Parsing**: Browser reads HTML and creates DOM (Document Object Model) tree
2. **CSS Parsing**: Browser reads CSS and creates CSSOM (CSS Object Model)
3. **Render Tree**: Combines DOM and CSSOM to create render tree
4. **Layout/Reflow**: Calculates exact position and size of each element
5. **Paint**: Draws the actual pixels on screen
6. **Composite**: Combines different layers if needed

### 4. What is SVG, and how is it used in HTML?

**SVG (Scalable Vector Graphics)** is a vector-based image format that can be embedded directly in HTML.

**Advantages:**
- Scalable without quality loss
- Small file sizes for simple graphics
- Can be styled with CSS
- Interactive with JavaScript
- Accessible and searchable

### 5. How do you ensure a website is accessible?

**Key accessibility practices:**

**Semantic HTML:**
```html
<nav>Navigation content</nav>
<main>Main content</main>
<aside>Sidebar content</aside>
```

**Alt text for images:**
```html
<img src="chart.png" alt="Sales increased 25% from Q3 to Q4">
```

**Proper headings hierarchy:**
```html
<h1>Page Title</h1>
  <h2>Section Title</h2>
    <h3>Subsection</h3>
```

**Form labels:**
```html
<label for="email">Email Address:</label>
<input type="email" id="email" name="email">
```

**Keyboard navigation:**
```html
<button>Clickable with keyboard</button>
<a href="#content">Skip to content</a>
```

**ARIA attributes:**
```html
<div role="button" aria-label="Close dialog" tabindex="0">×</div>
```

### 6. How can HTML impact a website's load time and performance? What are some strategies to improve it?

**HTML Performance Impact:**

**Issues:**
- Large DOM trees slow down rendering
- Excessive nested elements increase parsing time
- Inline styles/scripts block rendering
- Missing optimizations increase file sizes

**Optimization Strategies:**

**1. Minimize DOM complexity:**
```html
<!-- Good: Simple structure -->
<article class="post">
    <h2>Title</h2>
    <p>Content</p>
</article>

<!-- Avoid: Excessive nesting -->
<div><div><div><div><div>Content</div></div></div></div></div>
```

**2. Optimize resource loading:**
```html
<!-- Preload critical resources -->
<link rel="preload" href="critical.css" as="style">

<!-- Defer non-critical JavaScript -->
<script src="script.js" defer></script>

<!-- Lazy load images -->
<img src="image.jpg" loading="lazy" alt="Description">
```

**3. Minify and compress:**
```html
<!-- Before: Readable but larger -->
<div class="container">
    <p class="description">
        This is a description
    </p>
</div>

<!-- After: Minified (done by build tools) -->
<div class="container"><p class="description">This is a description</p></div>
```

**4. Use appropriate HTML5 elements:**
```html
<!-- Semantic elements help browsers optimize -->
<header>Header content</header>
<nav>Navigation</nav>
<main>Main content</main>
<footer>Footer content</footer>
```

**5. Optimize images:**
```html
<!-- Responsive images -->
<img src="small.jpg" 
     srcset="medium.jpg 800w, large.jpg 1200w"
     sizes="(max-width: 800px) 100vw, 50vw"
     alt="Description">

<!-- Modern formats with fallbacks -->
<picture>
    <source srcset="image.webp" type="image/webp">
    <img src="image.jpg" alt="Description">
</picture>
```

# Web Accessibility

**Web Accessibility** means designing and developing websites, tools, and technologies so they can be used by people with disabilities. This includes visual, auditory, physical, speech, cognitive, and neurological disabilities.

## Why Web Accessibility Matters

- **15% of the global population** has some form of disability
- **Legal requirement** in many countries (ADA, AODA, European Accessibility Act)
- **Better UX for everyone** - accessibility improvements benefit all users
- **SEO benefits** - accessible sites often rank better
- **Larger audience** - don't exclude potential users/customers

## Web Content Accessibility Guidelines (WCAG)

### WCAG Versions and Evolution:

#### **WCAG 1.0 (1999)**
- First international standard for web accessibility
- 14 guidelines with priority levels (1, 2, 3)
- Focused mainly on HTML accessibility
- Now obsolete

#### **WCAG 2.0 (2008)**
- Technology-agnostic approach
- Introduced POUR principles
- 12 guidelines organized under 4 principles
- Three conformance levels: A, AA, AAA

#### **WCAG 2.1 (2018)**
- Added 17 new success criteria
- Enhanced mobile accessibility
- Better support for cognitive disabilities
- Improved low vision accessibility

#### **WCAG 2.2 (2023)**
- Added 9 new success criteria
- Focus on mobile accessibility improvements
- Better cognitive accessibility support
- Enhanced authentication processes

#### **WCAG 3.0 (In Development)**
- Expected around 2025-2026
- New scoring model (0-4 scale instead of A/AA/AAA)
- More comprehensive testing methods
- Will address emerging technologies (AR/VR, IoT)

## WCAG 2.1/2.2 Principles (POUR)

### **1. Perceivable**
Information must be presentable in ways users can perceive:

```html
<!-- Text alternatives for images -->
<img src="chart.jpg" alt="Sales increased 25% from Q3 to Q4 2024">

<!-- Captions for videos -->
<video controls>
    <source src="video.mp4" type="video/mp4">
    <track kind="captions" src="captions.vtt" srclang="en" label="English">
</video>

<!-- Color is not the only indicator -->
<span class="error" aria-label="Error">❌ Invalid email format</span>

<!-- Sufficient color contrast -->
<p style="color: #333; background: #fff;">High contrast text</p>
```

### **2. Operable**
Interface components must be operable:

```html
<!-- Keyboard accessible -->
<button onclick="toggleMenu()" aria-expanded="false">
    Menu
</button>

<!-- Skip links -->
<a href="#main-content" class="skip-link">Skip to main content</a>

<!-- Focus indicators -->
<style>
button:focus {
    outline: 3px solid #4A90E2;
    outline-offset: 2px;
}
</style>

<!-- No seizure-inducing content -->
<div class="animation" style="animation-duration: 3s;"> <!-- Not too fast -->
```

### **3. Understandable**
Information and UI operation must be understandable:

```html
<!-- Page language -->
<html lang="en">

<!-- Form labels and instructions -->
<label for="password">Password:</label>
<input type="password" id="password" 
       aria-describedby="pwd-help" required>
<div id="pwd-help">Must be at least 8 characters</div>

<!-- Error identification -->
<div role="alert" aria-live="polite">
    Please correct the errors below:
</div>
```

### **4. Robust**
Content must be robust enough for various assistive technologies:

```html
<!-- Valid HTML -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Page Title</title>
</head>

<!-- ARIA labels -->
<nav aria-label="Main navigation">
    <ul>
        <li><a href="/" aria-current="page">Home</a></li>
        <li><a href="/about">About</a></li>
    </ul>
</nav>

<!-- Semantic HTML -->
<main>
    <article>
        <h1>Article Title</h1>
        <p>Article content...</p>
    </article>
</main>
```

## Conformance Levels

### **Level A (Minimum)**
- Basic accessibility features
- Essential for any public website
- Examples: Alt text, keyboard access, proper headings

### **Level AA (Standard)**
- **Most common legal requirement**
- Good accessibility for most users
- Examples: Color contrast 4.5:1, captions for videos

### **Level AAA (Enhanced)**
- Highest level of accessibility
- Often not feasible for entire websites
- Examples: Sign language interpretation, 7:1 color contrast

## ARIA (Accessible Rich Internet Applications)

### Common ARIA Attributes:

```html
<!-- Roles -->
<div role="button" tabindex="0">Custom button</div>
<nav role="navigation">Navigation menu</nav>

<!-- Properties -->
<input type="email" aria-required="true" aria-invalid="false">
<button aria-haspopup="true" aria-expanded="false">Menu</button>

<!-- States -->
<button aria-pressed="false">Toggle</button>
<div aria-hidden="true">Decorative content</div>

<!-- Labels and descriptions -->
<input aria-label="Search products">
<input aria-describedby="help-text">
<div id="help-text">Enter your search term</div>

<!-- Live regions -->
<div aria-live="polite" id="status"></div>
<div aria-live="assertive" id="errors"></div>
```

## Testing Web Accessibility

### **1. Automated Testing Tools**

#### **Browser Extensions:**
- **axe DevTools**: Comprehensive accessibility testing
- **WAVE**: Visual accessibility evaluation
- **Lighthouse**: Includes accessibility audit
- **Accessibility Insights**: Microsoft's testing tool

#### **Command Line Tools:**
```bash
# axe-cli for automated testing
npm install -g @axe-core/cli
axe https://example.com

# Pa11y for accessibility testing
npm install -g pa11y
pa11y https://example.com
```

#### **Testing Libraries:**
```javascript
// Jest + axe for unit testing
import { axe, toHaveNoViolations } from 'jest-axe';
expect.extend(toHaveNoViolations);

test('should not have accessibility violations', async () => {
    const { container } = render(<MyComponent />);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
});
```

### **2. Manual Testing**

#### **Keyboard Navigation:**
```
Tab: Move forward through interactive elements
Shift + Tab: Move backward
Enter/Space: Activate buttons/links
Arrow keys: Navigate within components
Escape: Close modals/menus
```

#### **Screen Reader Testing:**
- **NVDA** (Windows, Free)
- **JAWS** (Windows, Commercial)
- **VoiceOver** (Mac/iOS, Built-in)
- **TalkBack** (Android, Built-in)

#### **Testing Checklist:**
```
□ All images have appropriate alt text
□ All form inputs have labels
□ Color contrast meets AA standards (4.5:1)
□ All interactive elements are keyboard accessible
□ Focus indicators are visible
□ Page has proper heading structure (h1-h6)
□ Error messages are clearly associated with inputs
□ Skip links are present and functional
□ Content is logical without CSS
□ No keyboard traps exist
```

### **3. User Testing**
- Test with actual users who have disabilities
- Use diverse assistive technologies
- Include users with various types of disabilities

## European Accessibility Mandate

### **European Accessibility Act (EAA)**
- **Effective Date**: June 28, 2025
- **Scope**: Digital products and services in EU
- **Impact**: Legal requirement for accessibility compliance

#### **Covered Services:**
- E-commerce websites
- Banking services
- E-books
- Computer operating systems
- ATMs and ticketing machines
- TV equipment with interactive features

### **EN 301 549 Standard**
- **European Standard** for ICT accessibility
- **Based on WCAG 2.1 Level AA**
- Includes additional requirements for:
  - Software applications
  - Mobile applications
  - Hardware interfaces

### **Web Accessibility Directive (WAD)**
- **Applies to**: Public sector websites and mobile apps
- **Requirements**: WCAG 2.1 Level AA compliance
- **Includes**: Accessibility statements and feedback mechanisms

### **Penalties and Enforcement**
- Fines up to **€100,000** for non-compliance
- Varies by EU member state
- Regular monitoring and audits
- User complaint mechanisms

## Accessibility Implementation Strategy

### **1. Design Phase**
```html
<!-- Plan for accessibility from start -->
<style>
/* Design with sufficient contrast */
.primary-button {
    background: #0066cc;
    color: white; /* 7:1 contrast ratio */
}

/* Ensure touch targets are large enough */
.touch-target {
    min-height: 44px;
    min-width: 44px;
}
</style>
```

### **2. Development Phase**
```html
<!-- Use semantic HTML -->
<form>
    <fieldset>
        <legend>Personal Information</legend>
        <label for="name">Full Name:</label>
        <input type="text" id="name" required 
               aria-describedby="name-help">
        <div id="name-help">Enter your first and last name</div>
    </fieldset>
</form>

<!-- Implement proper focus management -->
<script>
function openModal() {
    const modal = document.getElementById('modal');
    modal.style.display = 'block';
    modal.focus();
    // Trap focus within modal
}
</script>
```

### **3. Testing and Maintenance**
```javascript
// Regular automated testing
const accessibilityTest = async () => {
    const results = await axe.run();
    if (results.violations.length > 0) {
        console.error('Accessibility violations found:', results.violations);
    }
};

// Run tests in CI/CD pipeline
// Schedule regular accessibility audits
// Monitor user feedback and complaints
```

## Common Accessibility Patterns

### **Modal Dialog:**
```html
<div role="dialog" aria-modal="true" aria-labelledby="modal-title">
    <h2 id="modal-title">Confirm Action</h2>
    <p>Are you sure you want to delete this item?</p>
    <button onclick="confirmDelete()">Yes, Delete</button>
    <button onclick="closeModal()">Cancel</button>
</div>
```

### **Data Tables:**
```html
<table>
    <caption>Monthly Sales Report</caption>
    <thead>
        <tr>
            <th scope="col">Month</th>
            <th scope="col">Sales ($)</th>
            <th scope="col">Growth (%)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">January</th>
            <td>$50,000</td>
            <td>+15%</td>
        </tr>
    </tbody>
</table>
```

### **Loading States:**
```html
<button aria-busy="true" aria-describedby="loading-text">
    <span aria-hidden="true">🔄</span>
    Loading...
</button>
<div id="loading-text" class="visually-hidden">
    Please wait while we process your request
</div>
```

Web accessibility is not just a legal requirement—it's about creating an inclusive web that works for everyone, regardless of their abilities or the technologies they use to access it.