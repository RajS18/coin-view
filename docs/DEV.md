# Development Guide - Commands, Setup & Troubleshooting

Reference guide for development commands, environment setup, and common issues encountered.

## Table of Contents
1. [Project Setup](#project-setup)
2. [Development Commands](#development-commands)
3. [Building & Deployment](#building--deployment)
4. [Troubleshooting](#troubleshooting)
5. [Git Workflow](#git-workflow)
6. [Terminal Reference](#terminal-reference)

---

## Project Setup

### Initial Setup (Already Done)

```bash
# Initialize Next.js project
npm create-next-app@latest coin-view -- --typescript --tailwind

# Install dependencies (already in package.json)
npm install
```

### Your Project Info

```
Project: coin-view
Version: 0.1.0
React: 19.2.3
Next.js: 16.1.6
Node: [Your version - run `node --version`]
TypeScript: 5.x
```

### Check Your Node Version

```bash
node --version
# Expected: v18.x or higher
# Command path: Run in terminal anywhere

npm --version
# Expected: 9.x or higher
```

---

## Development Commands

### Start Development Server

```bash
npm run dev
```

**What happens:**
- Starts Next.js dev server on `http://localhost:3000`
- Auto-reloads when you save files
- Shows build errors in terminal and browser
- You can now visit:
  - `http://localhost:3000` → Home page
  - `http://localhost:3000/about` → 404 (page doesn't exist yet)

**Sample output:**
```
> coin-view@0.1.0 dev
> next dev

  ▲ Next.js 16.1.6
  - Local:        http://localhost:3000
  - Environments: .env.local

✓ Ready in 891ms
```

**Stop the server:**
```
Press Ctrl+C in terminal
```

### Run Linting

```bash
npm run lint
```

**What it does:**
- Checks code for style issues
- Finds unused variables
- Checks TypeScript types
- Reports errors/warnings

**Sample output:**
```
✓ No ESLint errors
```

Or if there are issues:
```
app/page.tsx
  5:5  warning  'React' is defined but never used  @typescript-eslint/no-unused-vars

1 warning in 1 file.
```

**Fix automatically:**
```bash
npm run lint -- --fix
```

### Build for Production

```bash
npm run build
```

**What it does:**
- Compiles React to JavaScript
- Optimizes bundle size
- Checks for errors
- Creates `.next/` folder with production code

**Sample output:**
```
> coin-view@0.1.0 build
> next build

✓ Compiled successfully
✓ Linting and type checking

Route (pages)                              Size     First Load JS
/                                          3.0 kB        85.2 kB

✓ Build successful
```

**Watch for:**
- Type errors (code won't build)
- Warnings (fix before deploying)

### Start Production Server

```bash
npm run start
```

**Requires:** Running `npm run build` first

**What it does:**
- Starts optimized server
- Similar to production environment
- Runs on `http://localhost:3000`

---

## Building & Deployment

### Local Testing Before Deploy

```bash
# 1. Build for production
npm run build

# 2. Start production server
npm run start

# 3. Visit http://localhost:3000 in browser
# 4. Test all features work
# 5. Press Ctrl+C to stop
```

### Project Structure After Build

```
.next/
├── static/               ← Compiled JavaScript/CSS
│   ├── chunks/          ← Code split chunks
│   └── media/           ← Images
├── server/              ← Server-side code
└── app-build-manifest.json
```

---

## Troubleshooting

### Issue: "Cannot find module '@/components/Header'"

**Cause:** Path alias not working or component doesn't exist

**Solution:**
```bash
# 1. Check component exists
ls components/Header.tsx
# If not found, create it

# 2. Check tsconfig.json has path mapping
cat tsconfig.json | grep -A2 '"paths"'
# Should show: "@/*": ["./*"]

# 3. Restart dev server
# Stop with Ctrl+C and run: npm run dev
```

### Issue: Port 3000 Already in Use

**Error:** `Error: EADDRINUSE: address already in use :::3000`

**Solution:**
```bash
# Option 1: Kill process using port 3000
# On Windows (PowerShell):
netstat -ano | findstr :3000
taskkill /PID [PID] /F

# Option 2: Use different port
npm run dev -- -p 3001
# Now server runs on http://localhost:3001
```

### Issue: TypeScript Errors

**Error:** `Type 'string' is not assignable to type 'number'`

**Solution:**
```bash
# 1. Check the error location (shown in terminal)
# 2. Fix the type mismatch in your code
# 3. Save file - dev server auto-rebuilds
```

**Common TypeScript mistakes:**
```typescript
// ❌ Wrong: string vs number
const count: number = "5"

// ✅ Correct:
const count: number = 5

// ❌ Wrong: missing props
<Header title={123} />  // title expects string

// ✅ Correct:
<Header title="Coin View" />
```

### Issue: Changes Not Appearing

**Solution:**
```bash
# 1. Make sure you saved the file (Ctrl+S)
# 2. Hard refresh browser (Ctrl+Shift+R or Cmd+Shift+R)
# 3. Check dev server shows "ready" (look at terminal)
# 4. If stuck: 
#    - Stop dev server (Ctrl+C)
#    - Delete .next folder: rm -rf .next
#    - Restart: npm run dev
```

### Issue: Styling Not Applied

**Cause:** Tailwind classes not recognized

**Solution:**
```typescript
// ❌ Wrong: arbitrary class
<div className="p-custom">Content</div>

// ✅ Correct: use Tailwind class
<div className="p-4">Content</div>

// ✅ Or: use CSS variable
<div style={{ padding: 'var(--custom-padding)' }}>Content</div>
```

**Check globals.css:**
```bash
cat app/globals.css | head -5
# Should show:
# @import "tailwindcss";
# @import "tw-animate-css";
```

### Issue: "Cannot destructure property 'useState' of 'react'"

**Cause:** Using hooks in Server Component without `'use client'`

**Solution:**
```typescript
// ❌ Wrong: useState in server component
export default function Page() {
  const [count, setCount] = useState(0);  // ERROR!
  // ...
}

// ✅ Correct: Add 'use client'
'use client';

import { useState } from 'react';

export default function Page() {
  const [count, setCount] = useState(0);  // Works!
  // ...
}
```

---

## Git Workflow

### Current Branch

```bash
git branch
# Shows: * feature/navigation-branch
```

### Check Status

```bash
git status
```

**Sample output:**
```
On branch feature/navigation-branch
Your branch is ahead of 'origin/feature/navigation-branch' by 2 commits.

nothing to commit, working tree clean
```

### View Recent Commits

```bash
git log --oneline -5
```

**Sample output:**
```
f3dba1f remove editor config from tracking
293ea56 Remove default readme.md doc
e004082 Initial commit - coin-view
dfb36cc Initial commit from Create Next App
```

### Create New Commit

```bash
# 1. Make changes to files
# 2. Stage changes
git add .

# 3. Create commit
git commit -m "feat: Add Header component styling"

# 4. Push to remote
git push origin feature/navigation-branch
```

### Merge to Main

```bash
# 1. Ensure branch is up to date
git fetch origin
git rebase origin/master

# 2. Switch to master
git checkout master

# 3. Merge feature branch
git merge feature/navigation-branch

# 4. Push to remote
git push origin master
```

---

## Terminal Reference

### Navigation Commands

```bash
# Show current directory
pwd

# List files
ls                      # Simple list
ls -la                  # Detailed with hidden files
ls components/          # Files in subdirectory

# Change directory
cd app/                 # Go to app folder
cd ..                   # Go to parent folder
cd ../../               # Go up two levels
cd ~                    # Go to home directory
cd D:/Project\ Next/crypto-screener/  # Go to project
```

### File Operations

```bash
# Create file
echo "content" > filename.txt
touch filename.tsx      # Create empty file

# View file content
cat filename.txt        # Show entire file
head -20 filename.txt   # Show first 20 lines
tail -10 filename.txt   # Show last 10 lines

# Copy file
cp source.txt dest.txt

# Move/rename file
mv oldname.tsx newname.tsx

# Delete file
rm filename.txt

# Create directory
mkdir foldername

# Delete directory (must be empty)
rmdir foldername

# Delete directory with contents
rm -rf foldername
```

### Search Commands

```bash
# Find files by name
find . -name "*.tsx"
find components/ -type f

# Search in file contents
grep -r "useState" app/
grep "import Header" app/layout.tsx

# Count matches
grep -c "className" components/Header.tsx
```

### Package Manager Commands

```bash
# Install all dependencies (from package.json)
npm install

# Install new package
npm install package-name

# Install dev dependency
npm install --save-dev package-name

# Update package
npm update package-name

# Remove package
npm uninstall package-name

# List installed packages
npm list

# Check for vulnerabilities
npm audit

# Fix vulnerabilities
npm audit fix
```

### Git Commands

```bash
# Initialize git repo
git init

# Clone repository
git clone https://github.com/user/repo.git

# Check status
git status

# Stage changes
git add .               # Stage all
git add file.tsx        # Stage specific file

# Create commit
git commit -m "message"

# View history
git log
git log --oneline

# Switch branch
git checkout branch-name
git switch branch-name  # Newer syntax

# Create new branch
git branch new-branch
git checkout -b new-branch  # Create and switch

# Push changes
git push origin branch-name

# Pull changes
git pull origin branch-name

# Merge branches
git merge other-branch
```

---

## Environment Variables

### Create .env.local

```bash
# Create file in project root
touch .env.local

# Add variables (example):
# NEXT_PUBLIC_API_URL=https://api.coingecko.com
# SECRET_KEY=your-secret-key
```

**Usage:**
```typescript
// In code
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
// Only NEXT_PUBLIC_* variables are accessible in browser
```

---

## Performance Tips

### Monitor Build Size

```bash
npm run build

# Output shows:
# Route (pages)                              Size     First Load JS
# /                                          3.0 kB        85.2 kB
# /_not-found                                4.3 kB        84.5 kB
```

**Goal:** Keep "First Load JS" under 100 kB

### Check Dependencies

```bash
# List package sizes
npm ls --depth=0

# Find large packages
npm run build
# Look at output for large routes

# Remove unused dependencies
npm prune
```

---

## Useful Dev Shortcuts

### VS Code (If Using)

```
Ctrl+S              → Save file
Ctrl+Shift+P        → Command palette
Ctrl+/              → Comment/uncomment
Ctrl+H              → Find and replace
Alt+Up/Down         → Move line up/down
Shift+Alt+Up/Down   → Duplicate line
```

### Browser DevTools

```
F12 or Ctrl+Shift+I  → Open DevTools
Ctrl+Shift+C         → Inspect element
Ctrl+Shift+R         → Hard refresh (clear cache)
Network tab          → See API calls
Console              → See errors/logs
```

---

## Deployment Checklist

Before deploying to production:

```bash
# ✅ Run lint
npm run lint

# ✅ Build successfully
npm run build

# ✅ Start prod server locally
npm run start

# ✅ Test main features in browser
# - Navigate pages
# - Check responsive design
# - Verify dark mode
# - Test forms/interactions

# ✅ Check no console errors
# Open DevTools → Console tab

# ✅ Commit final changes
git add .
git commit -m "deploy: Prepare for production"

# ✅ Push to main branch
git push origin master

# ✅ Deploy to hosting (Vercel, Railway, etc.)
```

---

## Common File Edits During Development

### Add New Page

```bash
# Create file
mkdir -p app/coins
touch app/coins/page.tsx

# Edit with content
# See NEXTJS.md for page structure
```

### Add New Component

```bash
# Create file
touch components/CoinCard.tsx

# Edit with content
# See REACT.md for component structure
```

### Update Global Styles

```bash
# Open globals.css
# Add CSS variables or @layer rules
# Save - dev server auto-reloads
```

### Update Tailwind Config (Future)

```bash
# If you create tailwind.config.js:
touch tailwind.config.js

# Then restart dev server
# npm run dev
```

