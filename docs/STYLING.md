# Styling Guide: Tailwind + shadcn/ui + CSS Classes

A comprehensive guide to styling in the coin-view project.

## Table of Contents
1. [Tailwind CSS Basics](#tailwind-css-basics)
2. [shadcn/ui Components](#shadcnui-components)
3. [Class Name Utilities](#class-name-utilities)
4. [Creating Named Styles](#creating-named-styles)
5. [Dark Mode](#dark-mode)
6. [Best Practices](#best-practices)

---

## Tailwind CSS Basics

### What is Tailwind CSS?

Tailwind is a **utility-first CSS framework**. Instead of writing custom CSS, you:
1. Add predefined class names to your HTML elements
2. Combine classes to create designs
3. Avoid writing CSS files for styling

### Example: Styling the Header

```typescript
// Without Tailwind (traditional CSS)
const Header = () => {
  return (
    <header className="my-custom-header">
      <h1 className="header-title">Coin View</h1>
    </header>
  )
}

// styles.css
/*
.my-custom-header {
  background-color: #205;
  padding: 1rem;
  border-bottom: 1px solid #ddd;
}
.header-title {
  font-size: 1.5rem;
  color: white;
  margin: 0;
}
*/
```

```typescript
// With Tailwind (modern approach)
const Header = () => {
  return (
    <header className="bg-primary p-4 border-b border-border">
      <h1 className="text-2xl font-bold text-primary-foreground">Coin View</h1>
    </header>
  )
}
// No CSS file needed! The classes are predefined by Tailwind.
```

### Common Tailwind Classes

| Purpose | Classes | Example |
|---------|---------|---------|
| **Spacing** | `p-*`, `m-*`, `w-*`, `h-*` | `p-4` = padding 1rem, `m-2` = margin 0.5rem |
| **Colors** | `bg-*`, `text-*`, `border-*` | `bg-primary`, `text-white`, `border-gray-200` |
| **Typography** | `text-*`, `font-*`, `font-bold` | `text-lg`, `font-bold`, `text-center` |
| **Layout** | `flex`, `grid`, `block`, `hidden` | `flex`, `flex-col`, `grid-cols-3` |
| **Responsive** | `sm:`, `md:`, `lg:`, `xl:` | `md:flex` = flex on medium+ screens |
| **Hover/Active** | `hover:`, `focus:` | `hover:bg-primary`, `focus:ring` |

### Understanding the Color System

Your `globals.css` defines CSS variables for colors:

```css
:root {
  --primary: oklch(0.205 0 0);        /* Dark color */
  --primary-foreground: oklch(0.985 0 0); /* Light text on dark bg */
  --background: oklch(1 0 0);          /* Light background */
  --foreground: oklch(0.145 0 0);      /* Dark text */
}

.dark {
  --primary: oklch(0.922 0 0);         /* Light primary */
  --primary-foreground: oklch(0.205 0 0); /* Dark text on light bg */
  --background: oklch(0.145 0 0);      /* Dark background */
  --foreground: oklch(0.985 0 0);      /* Light text */
}
```

**How it works:**
1. In light mode: dark text on light background
2. In dark mode: light text on dark background
3. Tailwind converts `text-foreground` → `color: var(--foreground)` (dynamic!)

### Simple Styling Example

```typescript
// Button with Tailwind
const Button = ({ children, variant = 'primary' }: Props) => {
  const baseClasses = 'px-4 py-2 rounded-md font-medium transition-colors';
  
  const variants = {
    primary: 'bg-primary text-primary-foreground hover:bg-primary/90',
    secondary: 'bg-secondary text-secondary-foreground hover:bg-secondary/90',
    outline: 'border border-border bg-background text-foreground hover:bg-muted'
  };

  return (
    <button className={`${baseClasses} ${variants[variant]}`}>
      {children}
    </button>
  )
}

// Usage:
<Button variant="primary">Sign In</Button>
<Button variant="outline">Learn More</Button>
```

---

## shadcn/ui Components

### What is shadcn/ui?

shadcn/ui provides **pre-built, accessible UI components** styled with Tailwind. Instead of building Button, Card, Dialog from scratch, you use pre-built components.

**In your project:**
- `globals.css` imports: `@import "shadcn/tailwind.css"`
- This provides component styles and base Tailwind setup

### Using shadcn/ui (Future Reference)

While not yet implemented in your Header, here's how to use shadcn/ui:

```bash
# Install a component (e.g., Button)
npm install shadcn-ui@latest

# Use it
import { Button } from "@/components/ui/button"

export default function App() {
  return <Button>Click me</Button>
}
```

### Component Library Structure

When you add shadcn/ui components:
```
components/
├── Header.tsx          ← Your custom component
├── ui/
│   ├── button.tsx      ← shadcn/ui Button
│   ├── card.tsx        ← shadcn/ui Card
│   └── ...
```

---

## Class Name Utilities

### Problem: Managing Dynamic Classes

As components grow, conditional classes become messy:

```typescript
// ❌ Messy string concatenation
const Badge = ({ size, disabled }) => {
  return (
    <span 
      className={`px-2 py-1 rounded ${size === 'lg' ? 'px-4 py-2' : 'px-2 py-1'} ${disabled ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer'}`}
    >
      Badge
    </span>
  )
}
```

### Solution 1: clsx

Your project includes **clsx** - merge conditional classes:

```typescript
import clsx from 'clsx';

const Badge = ({ size, disabled }) => {
  return (
    <span 
      className={clsx(
        'px-2 py-1 rounded',
        {
          'px-4 py-2': size === 'lg',
          'opacity-50 cursor-not-allowed': disabled,
          'cursor-pointer': !disabled
        }
      )}
    >
      Badge
    </span>
  )
}
```

**Why clsx is better:**
- Readable structure
- Falsy values are removed automatically
- Supports objects and arrays

### Solution 2: class-variance-authority (cva)

Your project includes **cva** - define component variants cleanly:

```typescript
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  'px-4 py-2 rounded-md font-medium transition-colors',
  {
    variants: {
      variant: {
        primary: 'bg-primary text-primary-foreground hover:bg-primary/90',
        secondary: 'bg-secondary text-secondary-foreground hover:bg-secondary/90',
        outline: 'border border-border bg-background'
      },
      size: {
        sm: 'px-2 py-1 text-sm',
        md: 'px-4 py-2',
        lg: 'px-6 py-3 text-lg'
      }
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md'
    }
  }
);

type ButtonProps = VariantProps<typeof buttonVariants>;

const Button = ({ variant, size, ...props }: ButtonProps) => {
  return <button className={buttonVariants({ variant, size })} {...props} />;
};

// Usage:
<Button variant="primary" size="lg">Big Primary Button</Button>
<Button variant="outline" size="sm">Small Outline Button</Button>
```

**Benefits:**
- Variants are explicit and documented
- Type-safe (TypeScript knows all valid combinations)
- Easy to extend
- Clean component code

### Solution 3: tailwind-merge

Your project includes **tailwind-merge** - intelligently merge Tailwind classes:

```typescript
import { twMerge } from 'tailwind-merge';

const Card = ({ className, children }) => {
  return (
    <div className={twMerge('bg-card p-4 rounded-lg', className)}>
      {children}
    </div>
  )
}

// User can override default padding:
<Card className="p-8">Content with more padding</Card>
// Result: bg-card rounded-lg p-8 (p-8 overrides p-4)
```

---

## Creating Named Styles

### Approach 1: CSS Variables (Recommended for Global Styles)

Edit `app/globals.css` to add custom properties:

```css
/* Add after existing :root and .dark definitions */
:root {
  --sidebar: oklch(0.985 0 0);
  --sidebar-foreground: oklch(0.145 0 0);
  /* ... existing variables ... */
  
  /* Custom app variables */
  --header-height: 60px;
  --sidebar-width: 250px;
}

.dark {
  --sidebar: oklch(0.205 0 0);
  --sidebar-foreground: oklch(0.985 0 0);
  /* ... existing variables ... */
}
```

Then use in Tailwind (via `globals.css` @theme):

```typescript
const Layout = () => {
  return (
    <div className="flex">
      <aside className="w-[var(--sidebar-width)] bg-sidebar text-sidebar-foreground">
        Sidebar
      </aside>
      <main className="flex-1">Content</main>
    </div>
  )
}
```

### Approach 2: Component Classes (For Component-Specific Styles)

Create a `styles.ts` file next to your component:

```typescript
// components/Header/Header.tsx
import { headerClasses } from './styles';

const Header = () => {
  return (
    <header className={headerClasses.container}>
      <h1 className={headerClasses.title}>Coin View</h1>
      <nav className={headerClasses.nav}>
        {/* Navigation */}
      </nav>
    </header>
  )
}
```

```typescript
// components/Header/styles.ts
export const headerClasses = {
  container: 'bg-primary text-primary-foreground px-6 py-4 shadow-md',
  title: 'text-2xl font-bold',
  nav: 'flex gap-4 items-center'
} as const;
```

### Approach 3: Compound Components Pattern

Build complex UIs with semantic components:

```typescript
// components/Card.tsx
export const Card = ({ children, className }: Props) => (
  <div className={`bg-card rounded-lg p-4 ${className}`}>
    {children}
  </div>
)

export const CardHeader = ({ children }: Props) => (
  <div className="border-b pb-2 mb-2">
    {children}
  </div>
)

export const CardContent = ({ children }: Props) => (
  <div className="py-2">
    {children}
  </div>
)

// Usage:
<Card>
  <CardHeader>Title</CardHeader>
  <CardContent>Content here</CardContent>
</Card>
```

---

## Dark Mode

Your project is configured for dark mode. In `app/layout.tsx`:

```typescript
<html lang="en" className='dark'>
  <body className={`${geistSans.variable} ...`}>
    <Header />
    {children}
  </body>
</html>
```

The `className='dark'` enables dark mode styles from `globals.css`.

### Testing Dark Mode

Classes automatically apply dark mode styles:

```typescript
// Light mode (default)
<div className="bg-background">Light background</div>

// Dark mode (because .dark class on <html>)
// Same element shows dark background due to CSS variables
```

**CSS Variable Magic:**
```css
:root {
  --background: oklch(1 0 0);      /* Light - almost white */
}

.dark {
  --background: oklch(0.145 0 0);  /* Dark - almost black */
}
```

---

## Best Practices

### ✅ Do's

1. **Use Tailwind utilities** for styling
   ```typescript
   // Good
   <div className="flex gap-4 p-4 bg-card rounded-lg">Content</div>
   ```

2. **Use CSS variables** for consistency
   ```typescript
   // Good - changes globally when variable updates
   <header className="bg-primary text-primary-foreground">
   ```

3. **Use cva for component variants**
   ```typescript
   // Good - explicit, maintainable
   const buttonVariants = cva('...', { variants: { ... } });
   ```

4. **Organize styles by scope**
   - Global: `globals.css`
   - Component-level: next to the component file
   - Utilities: helper functions like `headerClasses`

### ❌ Don'ts

1. **Don't write custom CSS for simple styles**
   ```typescript
   // Bad
   <div className="my-style">Content</div>
   // my-style: { padding: 1rem; margin: 1rem; }
   
   // Good
   <div className="p-4 m-4">Content</div>
   ```

2. **Don't hardcode colors**
   ```typescript
   // Bad
   <button className="bg-[#3456ff]">Button</button>
   
   // Good
   <button className="bg-primary">Button</button>
   ```

3. **Don't repeat style combinations**
   ```typescript
   // Bad - repeated in multiple components
   <div className="flex items-center gap-2 p-4 bg-muted rounded">
   <div className="flex items-center gap-2 p-4 bg-muted rounded">
   
   // Good - extract to constant or component
   const cardVariants = cva('flex items-center gap-2 p-4 bg-muted rounded');
   ```

4. **Don't inline long class strings**
   ```typescript
   // Bad
   <header className="bg-primary text-primary-foreground px-6 py-4 shadow-md flex items-center justify-between ...">
   
   // Good
   const headerClasses = {
     container: 'bg-primary text-primary-foreground px-6 py-4 shadow-md flex items-center justify-between'
   };
   <header className={headerClasses.container}>
   ```

---

## Common Patterns

### Flexbox Layout
```typescript
// Row (left to right)
<div className="flex gap-4">
  <div>Item 1</div>
  <div>Item 2</div>
</div>

// Column (top to bottom)
<div className="flex flex-col gap-4">
  <div>Item 1</div>
  <div>Item 2</div>
</div>

// Centered
<div className="flex items-center justify-center">
  <div>Centered</div>
</div>
```

### Grid Layout
```typescript
// 3 columns
<div className="grid grid-cols-3 gap-4">
  <div>Col 1</div>
  <div>Col 2</div>
  <div>Col 3</div>
</div>

// Responsive: 1 col on mobile, 2 on tablet, 3 on desktop
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {/* items */}
</div>
```

### Responsive Design
```typescript
// Small screen: 100vw, Medium+: 600px max
<div className="w-full md:max-w-2xl">Content</div>

// Hide on mobile, show on desktop
<nav className="hidden md:block">Navigation</nav>
```

