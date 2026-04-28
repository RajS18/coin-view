# Next.js Fundamentals Guide

Learn Next.js (v16) as you build coin-view. This guide covers server-side rendering, routing, and the App Router.

## Table of Contents
1. [What is Next.js?](#what-is-nextjs)
2. [Project Structure (App Router)](#project-structure-app-router)
3. [File-Based Routing](#file-based-routing)
4. [Layouts](#layouts)
5. [Pages](#pages)
6. [Server vs Client Components](#server-vs-client-components)
7. [API Routes](#api-routes)
8. [Image Optimization](#image-optimization)
9. [Metadata & SEO](#metadata--seo)

---

## What is Next.js?

Next.js is a **React framework** that adds:
1. **File-based routing** - Create pages by adding files
2. **Server-side rendering** - Render pages on the server
3. **Static generation** - Pre-render pages at build time
4. **API routes** - Build APIs without a separate backend
5. **Optimizations** - Images, code splitting, etc.

### Your project uses:
- **Next.js 16.1.6** (latest version)
- **App Router** (modern routing, not Pages Router)
- **React 19.2.3** (latest React with improved performance)
- **TypeScript** (type-safe development)

---

## Project Structure (App Router)

### Directory Layout

```
coin-view/
├── app/
│   ├── layout.tsx          ← Root layout (wraps all pages)
│   ├── page.tsx            ← Home page (/ route)
│   ├── globals.css         ← Global styles for entire app
│   ├── (group)/            ← Optional route groups (organize routes)
│   │   ├── dashboard/
│   │   │   ├── layout.tsx  ← Dashboard-specific layout
│   │   │   └── page.tsx    ← /dashboard route
│   │   └── settings/
│   │       └── page.tsx    ← /settings route
│   └── api/                ← API routes
│       ├── coins/
│       │   └── route.ts    ← GET /api/coins
│       └── search/
│           └── route.ts    ← GET /api/search
├── components/
│   ├── Header.tsx
│   ├── Card.tsx
│   └── ui/                 ← shadcn/ui components
│       ├── button.tsx
│       └── ...
├── docs/                   ← Documentation (this folder)
├── public/                 ← Static assets (images, fonts)
│   ├── favicon.ico
│   └── ...
├── package.json
├── tsconfig.json
├── next.config.ts
└── globals.css
```

### Terminology

| Term | Meaning | Example |
|------|---------|---------|
| **Route** | URL path to a page | `/dashboard` |
| **Segment** | Part of the route | `dashboard` in `/dashboard/settings` |
| **Nested Route** | Route inside folders | `app/dashboard/settings/page.tsx` |
| **Layout** | Wraps pages with shared UI | `app/layout.tsx` wraps all pages |

---

## File-Based Routing

### How Routes are Created

In Next.js, the **file path** = **URL path**:

```
File Structure          →    URL Path
────────────────────────────────────
app/page.tsx           →    /
app/about/page.tsx     →    /about
app/blog/[id]/page.tsx →    /blog/123, /blog/456 (dynamic)
```

### Example Routes in coin-view

| File | Route | Purpose |
|------|-------|---------|
| `app/page.tsx` | `/` | Home page |
| `app/coins/page.tsx` | `/coins` | List all coins |
| `app/coins/[id]/page.tsx` | `/coins/btc` | Individual coin page |
| `app/portfolio/page.tsx` | `/portfolio` | User's portfolio |

### Dynamic Routes

```typescript
// app/coins/[id]/page.tsx
interface CoinPageProps {
  params: Promise<{ id: string }>;
}

export default async function CoinPage({ params }: CoinPageProps) {
  const { id } = await params; // btc, eth, sol, etc.
  
  return (
    <div>
      <h1>Coin: {id}</h1>
      {/* Fetch and display coin data */}
    </div>
  )
}
```

**When user visits `/coins/bitcoin`:**
- Next.js matches `app/coins/[id]/page.tsx`
- `id = "bitcoin"` is passed to the component
- Component renders coin details

---

## Layouts

### What is a Layout?

A layout is a **reusable UI wrapper** that:
- Wraps child pages
- Persists across page changes
- Shares state/context
- Ideal for navigation, sidebars, headers

### Your Root Layout

```typescript
// app/layout.tsx
import type { Metadata } from "next";
import { Geist, Geist_Mono } from "next/font/google";
import "./globals.css";
import Header from '@/components/Header'

const geistSans = Geist({
  variable: "--font-geist-sans",
  subsets: ["latin"],
});

const geistMono = Geist_Mono({
  variable: "--font-geist-mono",
  subsets: ["latin"],
});

export const metadata: Metadata = {
  title: "Coin View",
  description: "A Crypto Currency Price Tracking and screening App with low latency terminal and dashboard.",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en" className='dark'>
      <body
        className={`${geistSans.variable} ${geistMono.variable} antialiased`}
      >
        <Header />
        {children}
      </body>
    </html>
  );
}
```

**What happens:**
1. `<html>` and `<body>` are the root HTML elements
2. `<Header />` is rendered on **every page**
3. `{children}` is replaced with the current page content
4. `metadata` sets the page title and description (for SEO)

### Nested Layouts

```
app/
├── layout.tsx                    ← Root layout (Header, body, etc.)
└── dashboard/
    ├── layout.tsx                ← Dashboard layout
    └── page.tsx

// Structure:
// <RootLayout>
//   <Header />
//   <DashboardLayout>
//     <DashboardPage />
//   </DashboardLayout>
// </RootLayout>
```

```typescript
// app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="flex">
      <aside className="sidebar">Dashboard Sidebar</aside>
      <main className="flex-1">
        {children}
      </main>
    </div>
  );
}
```

---

## Pages

### What is a Page?

A page is a **user-visible component** that:
- Is exported from `page.tsx`
- Renders at a specific route
- Has a unique URL
- Cannot be reused with layout persistence

### Simple Page

```typescript
// app/page.tsx
export default function HomePage() {
  return (
    <main>
      <h1>Welcome to Coin View</h1>
      <p>Track and screen cryptocurrencies in real-time.</p>
    </main>
  )
}
```

Visit `/` to see this page.

### Your Current Home Page

```typescript
// app/page.tsx
import React from 'react'

const page = () => {
  return (
    <p className="text-2xl text-indigo-500">Testing Page</p>
  )
}

export default page
```

**Note:** Component name `page` (lowercase) is the convention for Next.js page components.

---

## Server vs Client Components

### Key Difference

| Aspect | Server Component | Client Component |
|--------|-----------------|-----------------|
| **Runs on** | Server only | Browser (client) |
| **Can use** | Database, secrets, async | useState, useEffect, event handlers |
| **Data fetching** | Direct (no extra request) | Via API (fetch in useEffect) |
| **Bundle size** | ✅ Smaller (code not sent to browser) | ❌ Larger |
| **Interactivity** | ❌ Not interactive | ✅ Interactive |

### Server Components (Default)

```typescript
// app/page.tsx - This is a Server Component by default
import db from '@/lib/database'; // Can access secrets, databases

export default async function HomePage() {
  // Can use async/await for database calls
  const coins = await db.query('SELECT * FROM coins');
  
  return (
    <div>
      <h1>Coins</h1>
      <ul>
        {coins.map(coin => (
          <li key={coin.id}>{coin.name}</li>
        ))}
      </ul>
    </div>
  )
}
```

**Benefits:**
- Direct database access
- No API needed
- Secrets secure on server
- Smaller JavaScript sent to browser

### Client Components

Add `'use client'` directive:

```typescript
// components/SearchBar.tsx
'use client'; // This component runs in the browser

import { useState } from 'react';

export default function SearchBar() {
  const [query, setQuery] = useState('');

  const handleSearch = async () => {
    // Can use useState, useEffect, event handlers
    const res = await fetch('/api/search', {
      method: 'POST',
      body: JSON.stringify({ query })
    });
    const results = await res.json();
    // ...
  }

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search coins..."
      />
      <button onClick={handleSearch}>Search</button>
    </div>
  )
}
```

### When to Use Client Components

✅ **Use 'use client' for:**
- Interactive forms
- Real-time updates (charts, prices)
- Event handlers (clicks, drags)
- `useState`, `useEffect`, hooks
- Browser APIs (localStorage, localStorage, etc.)

❌ **Server Components preferred for:**
- Fetching data from APIs or databases
- Accessing environment variables/secrets
- Rendering static content
- Faster initial page load

---

## API Routes

### Creating API Endpoints

```typescript
// app/api/coins/route.ts - Responds to GET /api/coins

export async function GET(request: Request) {
  // Fetch from external API
  const response = await fetch('https://api.coingecko.com/api/v3/coins/list');
  const coins = await response.json();

  return Response.json({ coins });
}
```

### With Dynamic Parameters

```typescript
// app/api/coins/[id]/route.ts - Responds to GET /api/coins/bitcoin

export async function GET(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;
  
  const response = await fetch(
    `https://api.coingecko.com/api/v3/coins/${id}`
  );
  const coinData = await response.json();

  return Response.json(coinData);
}
```

### Using API Routes in Components

```typescript
// components/CoinList.tsx
'use client';

import { useEffect, useState } from 'react';

export default function CoinList() {
  const [coins, setCoins] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchCoins = async () => {
      const res = await fetch('/api/coins');
      const data = await res.json();
      setCoins(data.coins);
      setLoading(false);
    }

    fetchCoins();
  }, []);

  if (loading) return <p>Loading...</p>;
  return <ul>{coins.map(c => <li key={c.id}>{c.name}</li>)}</ul>;
}
```

---

## Image Optimization

### Using Next.js Image Component

```typescript
import Image from 'next/image';

export default function CoinIcon() {
  return (
    <Image
      src="/coin-icons/bitcoin.png"
      alt="Bitcoin"
      width={64}
      height={64}
      priority // Load immediately (for above-fold images)
    />
  )
}
```

**Benefits over `<img>`:**
- Automatic responsive sizing
- WebP conversion (smaller files)
- Lazy loading (load only when visible)
- Prevents layout shift

### External Images

```typescript
import Image from 'next/image';

export default function ExternalImage() {
  return (
    <Image
      src="https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?days=30"
      alt="Bitcoin Chart"
      width={400}
      height={300}
      unoptimized // For external/dynamic images
    />
  )
}
```

---

## Metadata & SEO

### Static Metadata

```typescript
// app/page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Coin View - Crypto Screener',
  description: 'Track and analyze cryptocurrency prices in real-time',
  keywords: 'crypto, bitcoin, ethereum, prices',
  openGraph: {
    title: 'Coin View',
    description: 'Track cryptocurrencies',
    url: 'https://coinview.app',
    images: [
      {
        url: 'https://coinview.app/og-image.png',
        width: 1200,
        height: 630,
      }
    ]
  }
};

export default function HomePage() {
  return <h1>Home</h1>;
}
```

### Dynamic Metadata

```typescript
// app/coins/[id]/page.tsx
import type { Metadata } from 'next';

interface CoinPageProps {
  params: Promise<{ id: string }>;
}

export async function generateMetadata(
  { params }: CoinPageProps
): Promise<Metadata> {
  const { id } = await params;
  const coin = await fetch(`/api/coins/${id}`).then(r => r.json());

  return {
    title: `${coin.name} Price - Coin View`,
    description: `Current ${coin.name} price and market data`,
  };
}

export default async function CoinPage({ params }: CoinPageProps) {
  const { id } = await params;
  // ... component
}
```

---

## Common Next.js Patterns for coin-view

### Data Fetching Pattern

```typescript
// Server Component (app/coins/page.tsx)
import CoinCard from '@/components/CoinCard';

export default async function CoinsPage() {
  // Fetch on server
  const res = await fetch('https://api.coingecko.com/api/v3/coins/list');
  const coins = await res.json();

  return (
    <div className="grid grid-cols-3 gap-4">
      {coins.slice(0, 9).map(coin => (
        <CoinCard key={coin.id} coin={coin} />
      ))}
    </div>
  )
}
```

### Layout with Sidebar Pattern

```typescript
// app/layout.tsx
import Sidebar from '@/components/Sidebar';
import Header from '@/components/Header';

export default function RootLayout({ children }) {
  return (
    <html lang="en" className="dark">
      <body>
        <Header />
        <div className="flex">
          <Sidebar />
          <main className="flex-1">
            {children}
          </main>
        </div>
      </body>
    </html>
  )
}
```

### Real-time Updates (Client Component)

```typescript
// components/PriceUpdater.tsx
'use client';

import { useEffect, useState } from 'react';

export default function PriceUpdater({ coinId }: Props) {
  const [price, setPrice] = useState(null);

  useEffect(() => {
    const interval = setInterval(async () => {
      const res = await fetch(`/api/coins/${coinId}/price`);
      const data = await res.json();
      setPrice(data.price);
    }, 5000); // Update every 5 seconds

    return () => clearInterval(interval);
  }, [coinId]);

  return <span className="text-2xl font-bold">${price}</span>;
}
```

---

## Next Steps

1. Create more pages: `/coins`, `/portfolio`, `/settings`
2. Add API routes: `/api/coins`, `/api/search`
3. Create components: `CoinCard`, `PriceChart`, `Portfolio`
4. Fetch real data from CoinGecko or similar API
5. Add dynamic routes for individual coins
6. Implement dark mode toggle (currently always dark)

