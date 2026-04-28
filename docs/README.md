# Coin View Documentation

Comprehensive learning guides for the crypto screener project. Start here to learn React, Next.js, and build the application.

## 📚 Documentation Structure

### For Learning

| Guide | Purpose | Best For |
|-------|---------|----------|
| **[REACT.md](./REACT.md)** | React + TypeScript fundamentals | Understanding components, hooks, state |
| **[NEXTJS.md](./NEXTJS.md)** | Next.js framework concepts | Pages, routing, layouts, API routes |
| **[STYLING.md](./STYLING.md)** | Tailwind CSS, shadcn/ui, styling patterns | CSS classes, components, dark mode |

### For Building & Reference

| Guide | Purpose | Best For |
|-------|---------|----------|
| **[ARCHITECTURE.md](./ARCHITECTURE.md)** | How files wire together | Understanding project structure, component dependencies |
| **[DEV.md](./DEV.md)** | Development commands & troubleshooting | Running project, fixing errors, git workflow |

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ ([Download](https://nodejs.org/))
- A code editor (VS Code recommended)
- Basic JavaScript knowledge

### First Run

```bash
# Navigate to project
cd "D:\Project Next\crypto-screener"

# Start development server
npm run dev

# Open browser
# Visit http://localhost:3000
```

You should see: **"Testing Page"** (from `app/page.tsx`)

---

## 📖 Learning Path

### Week 1: Understand the Basics

1. **Read:** [NEXTJS.md - Project Structure](./NEXTJS.md#project-structure-app-router)
   - Understand what files do
   - See how layout wrapping works

2. **Read:** [ARCHITECTURE.md - Current Project Structure](./ARCHITECTURE.md#current-project-structure)
   - Visualize how components connect
   - See file relationships

3. **Try:** Run `npm run dev` and navigate pages

### Week 2: React Fundamentals

1. **Read:** [REACT.md - Component Basics](./REACT.md#component-basics)
   - Learn what components are
   - See your Header component structure

2. **Read:** [REACT.md - React in TypeScript](./REACT.md#react-in-typescript)
   - Understand prop typing
   - Learn interfaces

3. **Try:** Create your own simple component
   - Follow pattern from [REACT.md - Component Patterns](./REACT.md#component-patterns-in-coin-view)

### Week 3: Styling & Design

1. **Read:** [STYLING.md - Tailwind CSS Basics](./STYLING.md#tailwind-css-basics)
   - Learn utility-first approach
   - Understand the color system

2. **Read:** [STYLING.md - Class Name Utilities](./STYLING.md#class-name-utilities)
   - Learn clsx and cva
   - See how to manage complex classes

3. **Try:** Style the Header component
   - Add navigation
   - Use Tailwind classes

### Week 4: State & Interactivity

1. **Read:** [REACT.md - State Management](./REACT.md#state-management)
   - Learn useState hook
   - Understand when to use state

2. **Read:** [REACT.md - Hooks](./REACT.md#hooks)
   - Learn useEffect for side effects
   - See useContext for sharing data

3. **Try:** Build an interactive feature
   - Search input with state
   - Toggle dark mode

### Week 5+: Build Features

Use the guides as reference while building:
- Create pages with `app/[route]/page.tsx`
- Build components with `components/[name].tsx`
- Style with Tailwind classes and CSS variables
- Fetch data with API routes (`app/api/[route]/route.ts`)

---

## 🔗 How Files Connect

### Quick Reference

**Current wiring (see [ARCHITECTURE.md](./ARCHITECTURE.md#component-wiring) for details):**

```
User visits / 
  ↓
Next.js loads app/layout.tsx
  ├─ Imports app/globals.css (Tailwind + colors)
  ├─ Imports components/Header.tsx (renders on all pages)
  └─ Loads app/page.tsx for route-specific content
  ↓
Browser renders HTML
```

**When you add a new page:**
```
Create: app/coins/page.tsx
  ↓
Automatically available at: /coins
  ↓
Header from layout.tsx appears automatically
  ↓
Styling from globals.css applies automatically
```

---

## 💡 Concepts by Commit Chain

As you build, commit messages document what you learned:

### Example Commit Messages

```
✨ feat: Create Header component
- Added Header component with imports for Link and Image
- Component currently empty, ready for content
- See REACT.md: Component Basics

🎨 style: Add Header navigation styling
- Added Tailwind classes for header layout
- Used bg-primary, text-primary-foreground for theming
- See STYLING.md: Tailwind CSS Basics

⚙️ refactor: Improve Header layout with cva
- Extracted header styles to class variants
- Used class-variance-authority for cleaner styling
- See STYLING.md: Class Name Utilities

✨ feat: Add dark mode toggle to Header
- Implemented useState for theme switching
- Toggle updates CSS variables in globals.css
- See REACT.md: State Management & STYLING.md: Dark Mode
```

**Benefits:**
- ✅ Each commit teaches one concept
- ✅ Future you can understand the "why"
- ✅ Others can follow your learning journey

---

## 🛠️ Common Tasks

### I Want To...

**Understand how a component works**
→ Read [REACT.md - Component Basics](./REACT.md#component-basics)

**Style a component**
→ Read [STYLING.md - Tailwind CSS Basics](./STYLING.md#tailwind-css-basics)

**Create a new page**
→ Read [NEXTJS.md - File-Based Routing](./NEXTJS.md#file-based-routing)

**Add state to a component**
→ Read [REACT.md - State Management](./REACT.md#state-management)

**Fetch data from an API**
→ Read [NEXTJS.md - API Routes](./NEXTJS.md#api-routes)

**Fix a TypeScript error**
→ Read [DEV.md - Troubleshooting](./DEV.md#troubleshooting)

**Start the dev server**
→ Read [DEV.md - Development Commands](./DEV.md#development-commands)

**Understand project structure**
→ Read [ARCHITECTURE.md](./ARCHITECTURE.md)

---

## 📋 Quick Command Reference

```bash
# Start development server (auto-reload on save)
npm run dev

# Check code for errors and style issues
npm run lint

# Build for production
npm run build

# Start production server (after build)
npm run start

# View file structure
ls -la

# View recent commits
git log --oneline -5

# Create new commit
git add .
git commit -m "message"
```

See [DEV.md - Terminal Reference](./DEV.md#terminal-reference) for more commands.

---

## 🔍 File & Component Map

### App Files (`app/`)
- `layout.tsx` - Root layout (wraps all pages, imports Header)
- `page.tsx` - Home page (`/`)
- `globals.css` - Global styles, CSS variables, dark mode

### Components (`components/`)
- `Header.tsx` - Navigation header (currently empty)
- `ui/` - shadcn/ui components (future)
- Feature-specific components (future)

### API Routes (`app/api/`)
- Will create routes like `/api/coins`, `/api/search` (future)

See [ARCHITECTURE.md - Current Project Structure](./ARCHITECTURE.md#current-project-structure) for full map.

---

## 🎯 Learning Outcomes

By the end, you'll understand:

### React
- ✅ How components work (functions returning JSX)
- ✅ Props: passing data down
- ✅ State: data that changes
- ✅ Hooks: useState, useEffect, useContext
- ✅ Conditional rendering
- ✅ Lists and keys
- ✅ Event handling
- ✅ Forms and input handling

### Next.js
- ✅ File-based routing (pages and routes from files)
- ✅ Layouts (reusable wrappers)
- ✅ Server vs Client components
- ✅ API routes (backend without separate server)
- ✅ Static and dynamic pages
- ✅ Image optimization
- ✅ Metadata and SEO

### Styling
- ✅ Utility-first CSS (Tailwind)
- ✅ CSS variables for theming
- ✅ Responsive design
- ✅ Dark mode implementation
- ✅ Component styling patterns
- ✅ Using clsx and cva for classes

### Development
- ✅ Terminal commands
- ✅ Git workflow
- ✅ Debugging and troubleshooting
- ✅ Building and deploying

---

## 📝 Documentation Updates

As you learn and build, add to these docs:

**When you:**
- Learn a new concept → Add to relevant guide
- Fix a bug → Add to [DEV.md - Troubleshooting](./DEV.md#troubleshooting)
- Create a pattern → Document in [ARCHITECTURE.md](./ARCHITECTURE.md#adding-new-features)
- Use a new CLI command → Add to [DEV.md - Terminal Reference](./DEV.md#terminal-reference)

Example:
```
// Learned: How to use useReducer for complex state
// Add to: REACT.md section on "State Management" 
// Include: When to use over useState, example code, use cases
```

---

## 🔗 External Resources

While learning, you may want to reference:

- **React Docs:** https://react.dev
- **Next.js Docs:** https://nextjs.org/docs
- **Tailwind Docs:** https://tailwindcss.com/docs
- **TypeScript Docs:** https://www.typescriptlang.org/docs
- **shadcn/ui:** https://ui.shadcn.com
- **CoinGecko API:** https://docs.coingecko.com (for data)

---

## ❓ FAQ

**Q: Where do I write my code?**
A: Components in `components/`, pages in `app/`, styles with Tailwind classes

**Q: How do I see my changes?**
A: Run `npm run dev`, then refresh browser after saving

**Q: What if something breaks?**
A: Check [DEV.md - Troubleshooting](./DEV.md#troubleshooting) section

**Q: How do I deploy?**
A: See [DEV.md - Building & Deployment](./DEV.md#building--deployment)

**Q: Can I use both Tailwind and custom CSS?**
A: Yes! Tailwind in classes, custom CSS in separate files or `<style>` tags

**Q: How do I add a new page?**
A: Create `app/[route-name]/page.tsx` - see [NEXTJS.md](./NEXTJS.md#file-based-routing)

---

## 📞 Getting Help

If stuck:

1. **Check [DEV.md - Troubleshooting](./DEV.md#troubleshooting)** - Most common issues covered
2. **Search the relevant guide** - Use Ctrl+F to search
3. **Check external docs** - Links in "External Resources" above
4. **Check terminal output** - Error messages often explain the problem
5. **Check browser DevTools** (F12) - Console may show JavaScript errors

---

## 🎓 Next Step

Choose based on your comfort:

- **Never used React?** → Start with [REACT.md](./REACT.md)
- **Want to understand the project?** → Start with [ARCHITECTURE.md](./ARCHITECTURE.md)
- **Ready to code?** → Start with [DEV.md](./DEV.md#development-commands) to run the project
- **Need to style something?** → Go to [STYLING.md](./STYLING.md)

Happy coding! 🚀

