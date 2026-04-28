# React + TypeScript Fundamentals Guide

Welcome to your React learning journey! This guide documents React concepts as you build the coin-view project.

## Table of Contents
1. [Component Basics](#component-basics)
2. [React in TypeScript](#react-in-typescript)
3. [Component Architecture](#component-architecture)
4. [State Management](#state-management)
5. [Hooks](#hooks)

---

## Component Basics

### What is a Component?

A React component is a **reusable piece of UI** built with JavaScript/TypeScript. Think of it as a building block that:
- Accepts inputs (**props**)
- Manages internal state (if needed)
- Returns JSX (JavaScript XML - looks like HTML)
- Outputs rendered HTML to the DOM

### Component Types

#### 1. **Function Components** (Modern Standard)
```typescript
// Simple function component
const Header = () => {
  return <header>My App Header</header>
}
export default Header
```

**Why we use function components:**
- Simpler syntax than class components
- Easier to test
- Can use React Hooks (modern state management)
- Better performance

#### 2. **Class Components** (Legacy - avoid learning)
- Older approach, still supported but not recommended for new projects
- Use function components instead

### Example: Your Header Component

Your current Header component:
```typescript
import Link from 'next/link';
import Image from 'next/image';

const Header = () => {
  return (
    <header>
      {/* Header content goes here */}
    </header>
  )
}

export default Header
```

**What's happening:**
1. `const Header = () => { ... }` - Declares a function component named `Header`
2. `return (...)` - Returns JSX to render
3. `export default Header` - Makes the component available for import in other files
4. In `app/layout.tsx`, it's imported with: `import Header from '@/components/Header'`

---

## React in TypeScript

TypeScript adds **type safety** to React - you declare what types props should be.

### Typing Component Props

```typescript
// Define what props your component accepts
interface HeaderProps {
  title: string;
  isDarkMode: boolean;
  onMenuClick?: () => void; // Optional prop (?)
}

const Header = ({ title, isDarkMode, onMenuClick }: HeaderProps) => {
  return (
    <header>
      <h1>{title}</h1>
      {isDarkMode && <p>Dark mode enabled</p>}
    </header>
  )
}

export default Header
```

**Key TypeScript concepts for React:**

| Concept | Purpose | Example |
|---------|---------|---------|
| `interface` | Define the shape of props | `interface Props { name: string }` |
| `?` (optional) | Mark prop as optional | `onClose?: () => void` |
| Union Types | Accept multiple types | `status: 'loading' \| 'success' \| 'error'` |
| `React.ReactNode` | Any valid React content | `children: React.ReactNode` |

### Children Prop Pattern

```typescript
interface CardProps {
  title: string;
  children: React.ReactNode; // Any valid React content
}

const Card = ({ title, children }: CardProps) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-content">
        {children}
      </div>
    </div>
  )
}

// Usage:
<Card title="My Card">
  <p>This content is passed as children</p>
  <button>Click me</button>
</Card>
```

---

## Component Architecture

### Component Hierarchy in Your Project

```
RootLayout (app/layout.tsx)
├── Header (components/Header.tsx) ← Rendered here
└── Page Content (app/page.tsx)
    └── Other components...
```

### File Organization Pattern

```
project/
├── app/
│   ├── layout.tsx      ← Root layout, imports global styles & Header
│   ├── page.tsx        ← Main page component
│   └── globals.css     ← Global styles for entire app
├── components/
│   ├── Header.tsx      ← Reusable header component
│   ├── Card.tsx        ← Reusable card component
│   └── ...
└── docs/
    └── REACT.md        ← This file
```

**Why this structure?**
- **`app/`** - Next.js App Router files (pages and layouts)
- **`components/`** - Reusable React components
- **Separation of concerns** - Each file has one responsibility

---

## State Management

State is data that changes over time in your component.

### useState Hook

```typescript
import { useState } from 'react';

const Counter = () => {
  // Declare state variable and function to update it
  const [count, setCount] = useState<number>(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  )
}
```

**Breaking it down:**
- `useState(0)` - Initialize state with value `0`
- `[count, setCount]` - Destructure state value and update function
- `count` - Current value
- `setCount` - Function to update the state
- `useState<number>` - TypeScript: declare that count is a number

### When to Use State

✅ **Use state for:**
- Form inputs (text fields, checkboxes)
- UI toggles (modal open/close, menu expanded)
- Counters, timers
- Temporary data

❌ **Don't use state for:**
- Props from parent (just receive via props)
- Static data
- Data that doesn't change

---

## Hooks

Hooks are special functions that let you "hook into" React features.

### Common Hooks

#### 1. **useState** - Component State
Already covered above. Used for component-level state.

#### 2. **useEffect** - Side Effects
```typescript
import { useEffect, useState } from 'react';

const CryptoData = () => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Runs after component renders
    const fetchData = async () => {
      try {
        const response = await fetch('/api/crypto');
        const json = await response.json();
        setData(json);
      } catch (error) {
        console.error(error);
      } finally {
        setLoading(false);
      }
    }

    fetchData();
  }, []); // Empty dependency array = runs once on mount

  if (loading) return <p>Loading...</p>;
  return <div>{/* Render data */}</div>;
}
```

**useEffect patterns:**
- `useEffect(() => {}, [])` - Run once on mount (initialize)
- `useEffect(() => {}, [dependency])` - Run when dependency changes
- `useEffect(() => { return () => {} }, [])` - Cleanup function (run on unmount)

#### 3. **useContext** - Share Data Between Components
```typescript
// Create context
const ThemeContext = React.createContext('light');

// Provider (top of component tree)
const App = () => {
  return (
    <ThemeContext.Provider value="dark">
      <Header />
    </ThemeContext.Provider>
  )
}

// Consumer (deep in component tree)
const Header = () => {
  const theme = useContext(ThemeContext);
  return <header className={theme}>...</header>
}
```

---

## Component Patterns in coin-view

### Controlled Components

For form inputs that React controls:
```typescript
const SearchInput = () => {
  const [query, setQuery] = useState('');

  return (
    <input
      type="text"
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Search coins..."
    />
  )
}
```

### Composition Pattern

Build complex UIs by combining components:
```typescript
<Card>
  <CardHeader>Crypto Prices</CardHeader>
  <CardContent>
    {/* List of coins */}
  </CardContent>
  <CardFooter>Last updated: now</CardFooter>
</Card>
```

---

## Next Steps

- Start with simple components (Header, Card, Button)
- Add props to make them flexible
- Use `useState` for interactive features
- Use `useEffect` to fetch data from APIs
- Refer back to this guide as you build!

