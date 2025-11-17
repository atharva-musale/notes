# Cascading style sheets (CSS)

## Frequently asked questions

### 1. What is box model?

The **CSS Box Model** defines how elements are structured and how their dimensions are calculated. Every element is essentially a rectangular box with four main components:

**Box Model Components (from inside out):**

1. **Content**: The actual content (text, images, etc.)
2. **Padding**: Space between content and border
3. **Border**: Line around the padding and content
4. **Margin**: Space outside the border (between elements)

**Visual representation:**
```
┌─────────────── margin (15px) ────────────────┐
│  ┌─────────── border (5px) ──────────────┐   │
│  │  ┌─────── padding (20px) ──────────┐  │   │
│  │  │                                 │  │   │
│  │  │        Content (300x200)        │  │   │
│  │  │                                 │  │   │
│  │  └─────────────────────────────────┘  │   │
│  └───────────────────────────────────────┘   │
└──────────────────────────────────────────────┘
```

**Box Sizing:**
Box sizing is a CSS property that controls how the total width and height of an element are calculated. It determines whether padding and border are included in the element's specified dimensions or added to them.

The Two Main Values:
1. **content-box (Default):**
Width and height apply only to the content area
Padding and border are added outside the specified dimensions

2. **border-box (Recommended):**
Width and height include content, padding, and border
The specified dimensions are the total final size
Much more intuitive for layout design


### 2. How do you handle browser compatibility and fallbacks in CSS?

Browser compatibility ensures your CSS works across different browsers and versions. Here are key strategies:

**1. CSS Feature Detection and Fallbacks:**

```css
/* Progressive Enhancement - start with basic styles */
.button {
    /* Fallback for older browsers */
    background-color: #blue;
    color: white;
    padding: 10px 20px;
    
    /* Modern browsers get enhanced styles */
    background: linear-gradient(45deg, #007bff, #0056b3);
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

/* CSS Grid with Flexbox fallback */
.container {
    /* Fallback: Flexbox (supported in older browsers) */
    display: flex;
    flex-wrap: wrap;
    
    /* Modern: CSS Grid (if supported) */
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 20px;
}

/* Custom properties with fallbacks */
.element {
    /* Fallback value */
    color: #333333;
    /* Modern browsers */
    color: var(--text-color, #333333);
}
```

**2. @supports Feature Queries:**

```css
/* Check if browser supports CSS Grid */
@supports (display: grid) {
    .grid-container {
        display: grid;
        grid-template-columns: 1fr 2fr 1fr;
    }
}

@supports not (display: grid) {
    .grid-container {
        display: flex;
    }
}

/* Multiple property support */
@supports (display: grid) and (gap: 20px) {
    .advanced-grid {
        display: grid;
        gap: 20px;
    }
}

/* Check for specific values */
@supports (position: sticky) {
    .header {
        position: sticky;
        top: 0;
    }
}
```

**3. Vendor Prefixes:**

```css
.transform-element {
    /* Old WebKit browsers */
    -webkit-transform: translateX(100px);
    /* Old Mozilla browsers */
    -moz-transform: translateX(100px);
    /* Old Internet Explorer */
    -ms-transform: translateX(100px);
    /* Standard (modern browsers) */
    transform: translateX(100px);
}

.animation {
    -webkit-animation: slideIn 0.5s ease-in-out;
    animation: slideIn 0.5s ease-in-out;
}

@-webkit-keyframes slideIn {
    from { opacity: 0; }
    to { opacity: 1; }
}

@keyframes slideIn {
    from { opacity: 0; }
    to { opacity: 1; }
}
```

**4. Browser-Specific Hacks (Use Sparingly):**

```css
/* Internet Explorer specific */
.ie-specific {
    color: blue;
    color: red\9; /* IE8 and below */
}

/* Safari-specific using media query */
@media not all and (min-resolution:.001dpcm) {
    @supports (-webkit-appearance:none) {
        .safari-only {
            background: yellow;
        }
    }
}
```

**5. Mobile and Cross-Device Compatibility:**

```css
/* Touch-friendly targets */
.touch-target {
    min-height: 44px;
    min-width: 44px;
}

/* High-DPI displays */
@media (-webkit-min-device-pixel-ratio: 2),
       (min-resolution: 192dpi) {
    .high-dpi-image {
        background-image: url('image@2x.png');
        background-size: 100px 100px;
    }
}

/* Safe area for notched devices */
.header {
    padding-top: env(safe-area-inset-top);
    padding-left: env(safe-area-inset-left);
    padding-right: env(safe-area-inset-right);
}
```

**Tools and Best Practices:**

- **Autoprefixer**: Automatically adds vendor prefixes
- **Can I Use**: Check browser support for CSS features
- **CSS Reset/Normalize**: Consistent baseline across browsers
- **Progressive Enhancement**: Start simple, add advanced features
- **Testing**: Use tools like BrowserStack for cross-browser testing

### 3. What is CSS preprocessor and why should it be used?

A **CSS preprocessor** is a tool that extends CSS with programming-like features and compiles to standard CSS. Popular preprocessors include **Sass/SCSS**, **Less**, and **Stylus**.

**Key Features and Benefits:**

**1. Variables:**

```scss
// SCSS - Variables for consistency and maintainability
$primary-color: #007bff;
$secondary-color: #6c757d;
$font-size-base: 16px;
$border-radius: 8px;

.button {
    background-color: $primary-color;
    color: white;
    font-size: $font-size-base;
    border-radius: $border-radius;
    
    &:hover {
        background-color: darken($primary-color, 10%);
    }
}

// Compiled CSS:
.button {
    background-color: #007bff;
    color: white;
    font-size: 16px;
    border-radius: 8px;
}

.button:hover {
    background-color: #0056b3;
}
```

**2. Nesting:**

```scss
// SCSS - Nested selectors
.navbar {
    background: $primary-color;
    padding: 1rem;
    
    .nav-list {
        list-style: none;
        margin: 0;
        
        .nav-item {
            display: inline-block;
            margin-right: 1rem;
            
            .nav-link {
                color: white;
                text-decoration: none;
                
                &:hover {
                    color: lighten(white, 10%);
                    text-decoration: underline;
                }
            }
        }
    }
}
```

**3. Mixins (Reusable Code Blocks):**

```scss
// Define mixin
@mixin button-style($bg-color, $text-color: white) {
    background-color: $bg-color;
    color: $text-color;
    padding: 10px 20px;
    border: none;
    border-radius: $border-radius;
    cursor: pointer;
    
    &:hover {
        background-color: darken($bg-color, 10%);
    }
}

// Use mixin
.primary-button {
    @include button-style($primary-color);
}

.secondary-button {
    @include button-style($secondary-color);
}

// Advanced mixin with responsive breakpoints
@mixin responsive($breakpoint) {
    @if $breakpoint == mobile {
        @media (max-width: 767px) { @content; }
    }
    @if $breakpoint == tablet {
        @media (min-width: 768px) and (max-width: 1023px) { @content; }
    }
    @if $breakpoint == desktop {
        @media (min-width: 1024px) { @content; }
    }
}

// Usage
.container {
    width: 100%;
    
    @include responsive(mobile) {
        padding: 1rem;
    }
    
    @include responsive(desktop) {
        max-width: 1200px;
        margin: 0 auto;
    }
}
```

**4. Functions and Operations:**

```scss
// Built-in functions
.card {
    background: lighten($primary-color, 40%);
    border: 1px solid darken($primary-color, 20%);
    color: complement($primary-color);
}

// Custom functions
@function calculate-rem($pixels) {
    @return $pixels / 16px * 1rem;
}

.heading {
    font-size: calculate-rem(24px); // 1.5rem
}

// Math operations
.grid-item {
    width: (100% / 3) - 2%; // 31.33333%
    margin-right: 2%;
}
```

**5. Imports and Partials:**

```scss
// _variables.scss (partial file)
$primary-color: #007bff;
$font-stack: 'Helvetica Neue', sans-serif;

// _mixins.scss
@mixin clearfix {
    &::after {
        content: "";
        display: table;
        clear: both;
    }
}

// main.scss
@import 'variables';
@import 'mixins';
@import 'components/buttons';
@import 'components/forms';
@import 'layout/grid';
```

**6. Control Directives:**

```scss
// Loops
@for $i from 1 through 12 {
    .col-#{$i} {
        width: (100% / 12) * $i;
    }
}

// Each loop
$colors: (
    primary: #007bff,
    secondary: #6c757d,
    success: #28a745,
    danger: #dc3545
);

@each $name, $color in $colors {
    .btn-#{$name} {
        background-color: $color;
        
        &:hover {
            background-color: darken($color, 10%);
        }
    }
}

// Conditional
@mixin theme($theme: light) {
  @if $theme == dark {
    background-color: #333;
    color: #fff;
  } @else {
    background-color: #fff;
    color: #333;
  }
}
```

**Why Use CSS Preprocessors:**

**Advantages:**
- **Better Organization**: Modular code with imports
- **DRY Principle**: Reusable mixins and functions
- **Maintainability**: Variables for consistent theming
- **Advanced Features**: Loops, conditions, calculations
- **Productivity**: Faster development with shortcuts
- **Team Collaboration**: Consistent coding standards

**Popular Preprocessors Comparison:**

| Feature | Sass/SCSS | Less | Stylus |
|---------|-----------|------|---------|
| Syntax | CSS-like (SCSS) or indented (Sass) | CSS-like | Flexible |
| Variables | `$variable` | `@variable` | `variable` |
| Nesting | ✅ | ✅ | ✅ |
| Mixins | ✅ | ✅ | ✅ |
| Functions | ✅ | ✅ | ✅ |
| Control Flow | ✅ | Limited | ✅ |
| Ecosystem | Largest | Good | Smaller |

### 4. What's the difference between Flexbox and Grid?

**Flexbox** and **CSS Grid** are both layout systems, but they serve different purposes and excel in different scenarios.

**Flexbox (1-dimensional layout):**
- Designed for **one-dimensional** layouts (either row or column)
- Perfect for **component-level** layouts
- Content-based sizing and alignment
- Items flow in a single direction

**CSS Grid (2-dimensional layout):**
- Designed for **two-dimensional** layouts (rows and columns simultaneously)
- Perfect for **page-level** layouts
- Container-based sizing with explicit grid areas
- Items can span multiple rows/columns

**Key Differences:**

| Aspect | Flexbox | CSS Grid |
|--------|---------|----------|
| **Dimensions** | 1D (row OR column) | 2D (rows AND columns) |
| **Use Case** | Components, navigation, centering | Page layouts, complex grids |
| **Content Flow** | Content determines layout | Layout is predefined |
| **Item Sizing** | Content-based | Grid-area based |
| **Browser Support** | Excellent (IE10+) | Good (IE11+ with prefixes) |

**Quick Decision Guide:**

- **Flexbox**: Navigation bars, button groups, centering, equal-height columns, component layouts
- **Grid**: Page layouts, card grids, magazine layouts, dashboard layouts, overlapping content
- **Both**: Use Grid for overall page structure and Flexbox for individual components within grid areas

**Modern Best Practice:**
Use CSS Grid for the big picture (page layout) and Flexbox for the details (component layout). They complement each other perfectly!