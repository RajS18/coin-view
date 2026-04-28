# Project Architecture & Wiring Guide

Understanding how files and components connect to create the coin-view application.

## Table of Contents
1. [Current Project Structure](#current-project-structure)
2. [Component Wiring](#component-wiring)
3. [Data Flow](#data-flow)
4. [How Files Affect the Project](#how-files-affect-the-project)
5. [Dependency Graph](#dependency-graph)
6. [Adding New Features](#adding-new-features)

---

## Current Project Structure

```
coin-view/
├── app/
│   ├── layout.tsx                    ← ROOT ENTRY POINT
│   │   └── Wraps: Header + Page Content
│   │   └── Imports: Header component, globals.css
│   │   └── Sets: Metadata, fonts, dark mode class
│   │
│   ├── page.tsx                      ← HOME PAGE (/)
│   │   └── Currently shows: "Testing Page" with indigo text
│   │   └── No props or state yet
│   │
│   ├── globals.css                   ← GLOBAL STYLING
│   │   └── Imports: Tailwind, shadcn, tw-animate
│   │   └── Defines: CSS variables, dark mode styles
│   │   └── Applies to: Entire application
│   │
│   └── [other pages will go here]
│
├── components/
│   ├── Header.tsx                    ← HEADER COMPONENT
│   │   └── Currently: Empty header (just opening tag)
│   │   └── Will contain: Navigation, logo, etc.
│   │   └── Used in: app/layout.tsx (every page)
│   │
│   └── [other components will go here]
│       ├── ui/                       ← shadcn/ui components (future)
│       │   ├── button.tsx
│       │   ├── card.tsx
│       │   └── ...
│       │
│       └── features/                 ← Feature-specific components
│           ├── CoinList/
│           │   ├── CoinList.tsx
│           │   └── CoinCard.tsx
│           └── Portfolio/
│               ├── Portfolio.tsx
│               └── PortfolioItem.tsx
│
├── docs/                             ← DOCUMENTATION
│   ├── REACT.md                      ← React fundamentals
│   ├── NEXTJS.md                     ← Next.js guide
│   ├── STYLING.md                    ← Tailwind + shadcn guide
│   ├── ARCHITECTURE.md               ← This file
│   └── DEV.md                        ← Development commands
│
├── public/                           ← STATIC ASSETS
│   ├── favicon.ico
│   └── [images, fonts, etc.]
│
├── package.json                      ← PROJECT DEPENDENCIES
├── tsconfig.json                     ← TYPESCRIPT CONFIG
├── next.config.ts                    ← NEXT.JS CONFIG
└── .next/                            ← BUILD OUTPUT (auto-generated)
```

---

## Component Wiring

### Current Wiring (Today)

```
app/layout.tsx (Root)
│
├── <html lang="en" className='dark'>
│   └── Sets dark mode for entire app
│
├── <body className={fonts}>
│   │
│   ├── <Header /> ← Imported from components/Header.tsx
│   │   │
│   │   └── <header>...</header>
│   │       └── Currently empty (work in progress)
│   │
│   └── {children} ← Replaced by page content
│       │
│       └── app/page.tsx (Home Page)
│           └── <p className="text-2xl text-indigo-500">Testing Page</p>
```

### How Layout Wrapping Works

1. **Next.js finds route**: User visits `/`
2. **Loads layout.tsx**: The root layout file is always loaded
3. **Renders Header**: `<Header />` component is rendered
4. **Renders page**: `app/page.tsx` content replaces `{children}`
5. **Outputs HTML**: User sees Header + Page content

### Code Flow

```typescript
// Step 1: User visits /
// Next.js executes:

// Step 2: Load root layout
import Header from '@/components/Header'  // ← Imports Header component
export default function RootLayout({ children }) {
  return (
    <html lang="en" className='dark'>     // ← Sets dark mode
      <body>
        <Header />                         // ← Renders Header component
        {children}                         // ← Renders page content
      </body>
    </html>
  )
}

// Step 3: Load home page
export default function page() {
  return <p className="text-2xl text-indigo-500">Testing Page</p>
}

// Step 4: Final HTML output
<html lang="en" className='dark'>
  <body>
    <header></header>  {/* From Header.tsx */}
    <p className="text-2xl text-indigo-500">Testing Page</p>  {/* From page.tsx */}
  </body>
</html>
```

---

## Data Flow

### Current Flow (Simple - No Data)

```
User Input (URL)
      ↓
Next.js Router
      ↓
Load Layout + Page Component
      ↓
Render HTML to Browser
      ↓
Display to User
```

### Future Flow (With Data Fetching)

```
User Input (URL)
      ↓
Load Server Component (e.g., app/coins/page.tsx)
      ↓
Fetch Data (from API/database)
      ↓
Render with Data
      ↓
Send HTML to Browser
      ↓
Client Component: useState, useEffect for interactivity
      ↓
User can interact with page
      ↓
Client fetches data via /api routes
      ↓
Updates state, re-renders
```

### Example: Coins Page (Future)

```
app/coins/page.tsx (Server Component)
  ├── Fetches coins from /api/coins
  ├── Or directly from CoinGecko API
  └── Returns coin list with data already loaded
       │
       └── <CoinList coins={coinData} /> (Client Component)
           ├── Displays coins
           ├── Has interactive search
           └── Filters coins on client-side
```

---

## How Files Affect the Project

### `app/layout.tsx` - The Master Layout

**Impact**: CRITICAL - Affects entire application

```
Changes to layout.tsx = Changes visible on EVERY page
```

| What you do | Impact |
|------------|--------|
| Change `<Header />` component | Header changes on all pages |
| Add `<Sidebar />` | Sidebar appears on all pages |
| Change `className='dark'` → `className=''` | Dark mode turns off everywhere |
| Add CSS class to body | Applies to entire app |

**Example:**
```typescript
// Before: Only Header on every page
<Header />

// After: Header + Sidebar on every page
<div className="flex">
  <Sidebar />
  <div className="flex-1">
    <Header />
    {children}
  </div>
</div>
```

### `components/Header.tsx` - Shared Component

**Impact**: Used on every page (currently empty)

```
Changes to Header.tsx = Changes visible on EVERY page
```

| What you do | Impact |
|------------|--------|
| Add logo image | Logo appears on every page |
| Add navigation links | Nav links appear on every page |
| Add dark mode toggle | Toggle works on every page |
| Fix padding/spacing | Affects layout on every page |

### `app/globals.css` - Styling Everything

**Impact**: HIGH - Sets styles for entire application

| What you do | Impact |
|------------|--------|
| Change `--primary` color | Primary color changes everywhere |
| Change `.dark` variables | Dark mode styling updates globally |
| Add new @layer rules | New Tailwind utilities available everywhere |
| Add global font | Font applies to entire app |

### `app/page.tsx` - Home Page Only

**Impact**: LOW - Only affects `/` route

| What you do | Impact |
|------------|--------|
| Change content | Only home page changes |
| Add state/hooks | Only home page has state |
| Add form | Only home page has form |

### `public/` - Static Assets

**Impact**: MEDIUM - Used throughout app

```
public/
├── favicon.ico        ← Tab icon
├── logo.png          ← Used in Header
└── coin-icons/       ← Used in CoinCard components
    ├── btc.png
    ├── eth.png
    └── ...
```

Changes here affect any components that reference the assets.

---

## Dependency Graph

### Visual Dependency Map

```
🔵 = Component/File
🔗 = Depends on / Imports

                    package.json
                         🔗
┌─────────────────────────────────────────────────┐
│                                                  │
│    app/globals.css (Tailwind, shadcn)           │
│    ────────────────────────────────────────    │
│    - Imports Tailwind CSS                       │
│    - Imports shadcn styles                      │
│    - Defines CSS variables                      │
│    └─ Used by: app/layout.tsx                   │
│                                                  │
└────────────────────────────────────────────────┘
                         🔗
                    app/layout.tsx
                    ────────────────
                    - Imports globals.css 🔗
                    - Imports Header 🔗
                    - Wraps all pages
                         🔗
            ┌──────────────┴──────────────┐
            │                             │
       components/                  app/page.tsx
       Header.tsx                   ────────────
       ──────────              - Shows testing content
       - Currently empty       - No dependencies yet
       - Renders on every page
            │
       (In future)
            ├─ Sidebar
            ├─ Navigation
            └─ Logo
```

### Import Chain Example

```
When you visit / (home page):

1. Browser requests: GET /
2. Next.js loads: app/layout.tsx
   ├── import "./globals.css"        ← Load global styles
   ├── import Header from '@/components/Header'  ← Load Header
   │   └── app/globals.css defines colors used by Header
   └── import Metadata from "next"   ← Load metadata
3. Next.js loads: app/page.tsx
4. Renders together:
   <html className='dark'>
     <style>/* globals.css content */</style>
     <body>
       <Header />  {/* from components/Header.tsx */}
       <p>Testing Page</p>  {/* from app/page.tsx */}
     </body>
   </html>
```

---

## Adding New Features

### Pattern 1: Adding a New Page

```typescript
// 1. Create the file
// app/coins/page.tsx

export default function CoinsPage() {
  return (
    <main>
      <h1>Cryptocurrency Prices</h1>
      {/* Content */}
    </main>
  )
}

// 2. Automatically available at: /coins
// 3. Header from layout.tsx appears automatically
// 4. Styling from globals.css applies automatically
```

**Effects:**
- ✅ Creates new route `/coins`
- ✅ Header + Layout wrap it automatically
- ✅ No changes needed to other files

### Pattern 2: Adding a Component

```typescript
// 1. Create component
// components/CoinCard.tsx

interface CoinCardProps {
  name: string;
  price: number;
  change24h: number;
}

export default function CoinCard({ name, price, change24h }: CoinCardProps) {
  return (
    <div className="border rounded-lg p-4 bg-card">
      <h3>{name}</h3>
      <p className="text-xl font-bold">${price}</p>
      <p className={change24h >= 0 ? 'text-green-500' : 'text-red-500'}>
        {change24h}%
      </p>
    </div>
  )
}

// 2. Use in a page
// app/coins/page.tsx

import CoinCard from '@/components/CoinCard'

export default function CoinsPage() {
  const coins = [
    { name: 'Bitcoin', price: 45000, change24h: 2.5 },
    { name: 'Ethereum', price: 2500, change24h: -1.2 }
  ]

  return (
    <div className="grid grid-cols-3 gap-4">
      {coins.map(coin => (
        <CoinCard key={coin.name} {...coin} />
      ))}
    </div>
  )
}
```

**Effects:**
- ✅ Component available for reuse
- ✅ Styled with Tailwind/CSS variables
- ✅ Type-safe with TypeScript
- ✅ Can be used on multiple pages

### Pattern 3: Adding Layout to Subdirectory

```typescript
// app/dashboard/layout.tsx
// This layout only wraps /dashboard/* routes

export default function DashboardLayout({ children }) {
  return (
    <div className="flex h-screen">
      <aside className="w-64 bg-primary text-primary-foreground p-4">
        <nav>
          <a href="/dashboard">Dashboard</a>
          <a href="/dashboard/portfolio">Portfolio</a>
          <a href="/dashboard/settings">Settings</a>
        </nav>
      </aside>
      <main className="flex-1 overflow-auto">
        {children}
      </main>
    </div>
  )
}

// app/dashboard/page.tsx
export default function DashboardPage() {
  return <h1>Dashboard</h1>
}

// Layout nesting:
// RootLayout (Header + body)
// └── DashboardLayout (Sidebar + main)
//     └── DashboardPage
```

**Effects:**
- ✅ Sidebar appears on `/dashboard`, `/dashboard/portfolio`, etc.
- ✅ Root Header still appears above
- ✅ Layout nesting works automatically

### Pattern 4: Adding API Route

```typescript
// app/api/coins/route.ts
// Responds to GET /api/coins

export async function GET(request: Request) {
  // Fetch external data
  const response = await fetch(
    'https://api.coingecko.com/api/v3/coins/list'
  )
  const coins = await response.json()

  return Response.json({ coins, timestamp: new Date() })
}

// Components can call this:
// const res = await fetch('/api/coins')
// const data = await res.json()
```

**Effects:**
- ✅ Creates API endpoint at `/api/coins`
- ✅ Can be called from client-side or server-side
- ✅ Fetches data without exposing external API URLs to client

---

## Impact Matrix

Understanding what changes when you modify files:

| File | When Changed | Impact Scope | Affected Routes |
|------|-------------|--------------|-----------------|
| `app/layout.tsx` | Always | Global | All pages |
| `components/Header.tsx` | Used in layout | Global | All pages |
| `app/globals.css` | Styling | Global | All pages |
| `app/page.tsx` | Content | Single | `/` only |
| `app/coins/page.tsx` | Content | Single | `/coins` only |
| `components/CoinCard.tsx` | Display | Used where imported | Varies |
| `app/api/coins/route.ts` | Endpoint | API | `/api/coins` only |
| `public/coin-icons/btc.png` | Asset | Used where referenced | Varies |

---

## Build & Deployment

### How Files Become a Website

```
1. You write code
   ├── React components (.tsx)
   ├── CSS (globals.css, Tailwind)
   └── API routes

2. Next.js builds:
   npm run build

3. Output: .next/ folder
   ├── Optimized JavaScript bundles
   ├── Pre-rendered HTML pages
   ├── Server functions
   └── Static assets

4. Deploy .next/ to hosting
   npm run start

5. User visits website
   ├── Gets HTML/CSS/JS from server
   ├── JavaScript hydrates (makes interactive)
   └── Component works
```

---

## Next Steps

1. **Improve Header**: Add logo, navigation, styling
2. **Create CoinCard component**: Reusable coin display
3. **Create /coins page**: List all cryptocurrencies
4. **Add API route**: `/api/coins` to fetch data
5. **Create /coins/[id] page**: Individual coin details
6. **Add Portfolio page**: Track user's holdings
7. **Add dark mode toggle**: In Header

Each of these follows the patterns explained in this guide!

