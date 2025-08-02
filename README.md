# Complete Next.js Conversion Guide - Assignment Portal

## 🎯 Overview
A comprehensive step-by-step guide to convert your working React + Vite Assignment Portal to Next.js 14 with App Router, ensuring all UI components and navigation work correctly.

## 📋 What We're Converting
- ✅ Working React + Vite app with responsive navigation
- ✅ Bottom navbar on mobile, desktop navbar on large screens  
- ✅ Complete UI component library with shadcn/ui
- ✅ Authentication system and data management

---

## 🚀 Step-by-Step Conversion

### Step 1: Create Fresh Next.js Project
```bash
# Create new Next.js project
npx create-next-app@latest assignment-portal-nextjs \
  --typescript \
  --tailwind \
  --eslint \
  --app \
  --src-dir \
  --import-alias "@/*"

cd assignment-portal-nextjs
```

### Step 2: Install All Required Dependencies
```bash
# Core dependencies (exact versions that work)
npm install \
  next-auth@4.24.5 \
  @hookform/resolvers@3.9.0 \
  react-hook-form@7.53.0 \
  date-fns@3.6.0 \
  recharts@2.12.7 \
  zod@3.23.8

# Radix UI components (complete set)
npm install \
  @radix-ui/react-accordion@1.2.0 \
  @radix-ui/react-alert-dialog@1.1.1 \
  @radix-ui/react-avatar@1.1.0 \
  @radix-ui/react-button@1.1.0 \
  @radix-ui/react-checkbox@1.1.1 \
  @radix-ui/react-dialog@1.1.2 \
  @radix-ui/react-dropdown-menu@2.1.1 \
  @radix-ui/react-label@2.1.0 \
  @radix-ui/react-popover@1.1.1 \
  @radix-ui/react-select@2.1.1 \
  @radix-ui/react-separator@1.1.0 \
  @radix-ui/react-slot@1.1.0 \
  @radix-ui/react-tabs@1.1.0 \
  @radix-ui/react-toast@1.2.1 \
  @radix-ui/react-tooltip@1.1.4

# Utility libraries
npm install \
  class-variance-authority@0.7.1 \
  clsx@2.1.1 \
  tailwind-merge@2.5.2 \
  lucide-react@0.462.0 \
  cmdk@1.0.0 \
  sonner@1.5.0 \
  vaul@0.9.3 \
  input-otp@1.2.4

# Animation
npm install \
  tailwindcss-animate@1.0.7

# Development dependencies
npm install -D @types/node
```

### Step 3: Setup Complete Tailwind Configuration

Replace `tailwind.config.ts`:
```typescript
import type { Config } from "tailwindcss";

const config: Config = {
  darkMode: ["class"],
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  prefix: "",
  theme: {
    container: {
      center: true,
      padding: "1rem",
      screens: {
        "2xl": "1400px",
      },
    },
    screens: {
      'xs': '375px',
      'sm': '640px',
      'md': '768px',
      'lg': '1024px',
      'xl': '1280px',
      '2xl': '1536px',
    },
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
        '128': '32rem',
      },
      keyframes: {
        "accordion-down": {
          from: { height: "0" },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: "0" },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
} satisfies Config;

export default config;
```

### Step 4: Setup Complete Global CSS

Replace `src/app/globals.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;
    --radius: 0.5rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;
    --popover: 222.2 84% 4.9%;
    --popover-foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;
    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;
    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;
    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;
    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;
    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 212.7 26.8% 83.9%;
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

### Step 5: Copy and Fix All UI Components

Copy your UI components and fix imports:
```bash
# Copy all UI components
mkdir -p src/components/ui
mkdir -p src/lib
mkdir -p src/hooks

# Copy from your current project (update path as needed)
cp -r client/components/ui/* src/components/ui/
cp -r client/lib/* src/lib/
cp -r client/hooks/* src/hooks/
```

**CRITICAL: Fix all import paths in UI components**

Go through each file in `src/components/ui/` and update imports:
- Change `"../lib/utils"` → `"@/lib/utils"`
- Change `"../../lib/utils"` → `"@/lib/utils"`
- Change any relative imports to use `@/` prefix

Example for `src/components/ui/button.tsx`:
```typescript
import { cn } from "@/lib/utils"  // ← Fix this import
```

### Step 6: Setup NextAuth.js

Create `src/app/api/auth/[...nextauth]/route.ts`:
```typescript
import NextAuth, { NextAuthOptions } from 'next-auth'
import CredentialsProvider from 'next-auth/providers/credentials'

const users = [
  { id: '1', name: 'Dr. Smith', email: 'instructor@example.com', password: 'password', role: 'instructor' },
  { id: '2', name: 'John Student', email: 'student@example.com', password: 'password', role: 'student' },
]

export const authOptions: NextAuthOptions = {
  providers: [
    CredentialsProvider({
      name: 'credentials',
      credentials: {
        email: { label: 'Email', type: 'email' },
        password: { label: 'Password', type: 'password' },
        role: { label: 'Role', type: 'text' }
      },
      async authorize(credentials) {
        if (!credentials?.email || !credentials?.password || !credentials?.role) {
          return null
        }

        const user = users.find(
          u => u.email === credentials.email && 
               u.password === credentials.password && 
               u.role === credentials.role
        )

        if (user) {
          return {
            id: user.id,
            name: user.name,
            email: user.email,
            role: user.role,
          }
        }
        return null
      }
    })
  ],
  session: { strategy: 'jwt' },
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.role = user.role
      }
      return token
    },
    async session({ session, token }) {
      if (token) {
        session.user.id = token.sub!
        session.user.role = token.role as string
      }
      return session
    },
  },
  pages: {
    signIn: '/login',
  },
}

const handler = NextAuth(authOptions)
export { handler as GET, handler as POST }
```

### Step 7: Create Type Definitions

Create `src/types/next-auth.d.ts`:
```typescript
import NextAuth from 'next-auth'

declare module 'next-auth' {
  interface User {
    role: string
  }
  interface Session {
    user: {
      id: string
      name: string
      email: string
      role: string
    }
  }
}

declare module 'next-auth/jwt' {
  interface JWT {
    role: string
  }
}
```

Create `src/types/index.ts`:
```typescript
export type UserRole = 'instructor' | 'student';

export interface User {
  id: string;
  name: string;
  email: string;
  role: UserRole;
}

export interface Assignment {
  id: string;
  title: string;
  description: string;
  deadline: string;
  instructorId: string;
}

export interface Submission {
  id: string;
  assignmentId: string;
  studentId: string;
  submissionUrl: string;
  note?: string;
  feedback?: string;
  status: 'pending' | 'accepted' | 'rejected';
  submittedAt: string;
  student?: { name: string; email: string; };
  assignment?: { title: string; };
}
```

### Step 8: Create Data Context

Create `src/contexts/DataContext.tsx` (copy your existing context):
```typescript
'use client'

// Copy the ENTIRE content from your current client/contexts/DataContext.tsx
// No changes needed - it will work as-is in Next.js

import React, { createContext, useContext, useState, useEffect } from 'react'
import { Assignment, Submission } from '@/types'

// ... rest of your DataContext code exactly as-is
```

### Step 9: Create Complete Navigation Component

Create `src/components/Navigation.tsx`:
```typescript
'use client'

import React from 'react'
import { useSession, signOut } from 'next-auth/react'
import Link from 'next/link'
import { usePathname } from 'next/navigation'
import { Button } from '@/components/ui/button'
import { Avatar, AvatarFallback } from '@/components/ui/avatar'
import { 
  GraduationCap, 
  LayoutDashboard, 
  FileText, 
  PlusCircle, 
  ClipboardList, 
  Send,
  LogOut,
} from 'lucide-react'
import { cn } from '@/lib/utils'

export default function Navigation() {
  const { data: session, status } = useSession()
  const pathname = usePathname()

  // Don't render if no session or still loading
  if (status === 'loading') return null
  if (!session?.user) return null

  const user = session.user

  const instructorLinks = [
    { href: '/dashboard', label: 'Dashboard', icon: LayoutDashboard },
    { href: '/create-assignment', label: 'Create', icon: PlusCircle },
    { href: '/submissions', label: 'Review', icon: ClipboardList },
  ]

  const studentLinks = [
    { href: '/dashboard', label: 'Dashboard', icon: LayoutDashboard },
    { href: '/assignments', label: 'Assignments', icon: FileText },
    { href: '/submit', label: 'Submit', icon: Send },
    { href: '/my-submissions', label: 'My Work', icon: ClipboardList },
  ]

  const links = user.role === 'instructor' ? instructorLinks : studentLinks

  const handleLogout = async () => {
    await signOut({ callbackUrl: '/login' })
  }

  return (
    <>
      {/* Desktop Navigation */}
      <nav className="bg-white border-b border-gray-200 shadow-sm lg:block hidden">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex justify-between h-16">
            <div className="flex items-center">
              <Link href="/dashboard" className="flex items-center space-x-3">
                <div className="bg-blue-600 p-2 rounded-lg">
                  <GraduationCap className="h-6 w-6 text-white" />
                </div>
                <span className="text-xl font-bold text-gray-900 hidden xl:block">Assignment Portal</span>
                <span className="text-lg font-bold text-gray-900 xl:hidden">Portal</span>
              </Link>
            </div>

            <div className="flex items-center space-x-1">
              {links.map((link) => {
                const Icon = link.icon
                return (
                  <Link
                    key={link.href}
                    href={link.href}
                    className={cn(
                      "flex items-center space-x-2 px-3 py-2 rounded-md text-sm font-medium transition-colors",
                      pathname === link.href
                        ? "bg-blue-100 text-blue-700"
                        : "text-gray-600 hover:text-gray-900 hover:bg-gray-50"
                    )}
                  >
                    <Icon className="h-4 w-4" />
                    <span className="hidden xl:inline">{link.label}</span>
                  </Link>
                )
              })}
            </div>

            <div className="flex items-center space-x-4">
              <div className="flex items-center space-x-3">
                <Avatar className="h-8 w-8">
                  <AvatarFallback className="bg-blue-100 text-blue-700 text-sm">
                    {user.name.split(' ').map(n => n[0]).join('')}
                  </AvatarFallback>
                </Avatar>
                <div className="text-sm hidden 2xl:block">
                  <div className="font-medium text-gray-900">{user.name}</div>
                  <div className="text-gray-500 capitalize">{user.role}</div>
                </div>
              </div>
              
              <Button
                variant="outline"
                size="sm"
                onClick={handleLogout}
                className="flex items-center space-x-2"
              >
                <LogOut className="h-4 w-4" />
                <span className="hidden xl:inline">Logout</span>
              </Button>
            </div>
          </div>
        </div>
      </nav>

      {/* Mobile Header */}
      <header className="bg-white border-b border-gray-200 shadow-sm lg:hidden sticky top-0 z-40">
        <div className="px-4 sm:px-6">
          <div className="flex justify-between items-center h-14">
            <Link href="/dashboard" className="flex items-center space-x-2">
              <div className="bg-blue-600 p-1.5 rounded-lg">
                <GraduationCap className="h-5 w-5 text-white" />
              </div>
              <span className="text-lg font-bold text-gray-900">Portal</span>
            </Link>
            
            <div className="flex items-center space-x-2">
              <Avatar className="h-7 w-7">
                <AvatarFallback className="bg-blue-100 text-blue-700 text-xs">
                  {user.name.split(' ').map(n => n[0]).join('')}
                </AvatarFallback>
              </Avatar>
              <Button
                variant="ghost"
                size="sm"
                onClick={handleLogout}
                className="p-1.5"
              >
                <LogOut className="h-4 w-4" />
              </Button>
            </div>
          </div>
        </div>
      </header>

      {/* Bottom Navigation */}
      <nav className="lg:hidden fixed bottom-0 left-0 right-0 bg-white border-t border-gray-200 shadow-lg z-50">
        <div className="grid grid-cols-4 h-16">
          {links.slice(0, 4).map((link) => {
            const Icon = link.icon
            const isActive = pathname === link.href
            
            return (
              <Link
                key={link.href}
                href={link.href}
                className={cn(
                  "flex flex-col items-center justify-center space-y-1 transition-colors relative",
                  isActive ? "text-blue-600" : "text-gray-600 hover:text-gray-900"
                )}
              >
                {isActive && (
                  <div className="absolute top-0 left-1/2 transform -translate-x-1/2 w-8 h-0.5 bg-blue-600 rounded-full"></div>
                )}
                <Icon className={cn("h-5 w-5", isActive && "text-blue-600")} />
                <span className={cn(
                  "text-xs font-medium leading-none",
                  isActive ? "text-blue-600" : "text-gray-600"
                )}>
                  {link.label}
                </span>
              </Link>
            )
          })}
        </div>
      </nav>

      {/* Bottom spacing for mobile nav */}
      <div className="lg:hidden h-16"></div>
    </>
  )
}
```

### Step 10: Create Session Provider

Create `src/providers/SessionProvider.tsx`:
```typescript
'use client'

import { SessionProvider as NextAuthSessionProvider } from 'next-auth/react'
import { ReactNode } from 'react'

interface Props {
  children: ReactNode
}

export function SessionProvider({ children }: Props) {
  return <NextAuthSessionProvider>{children}</NextAuthSessionProvider>
}
```

### Step 11: Setup Root Layout with Providers

Replace `src/app/layout.tsx`:
```typescript
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'
import { SessionProvider } from '@/providers/SessionProvider'
import { DataProvider } from '@/contexts/DataContext'
import Navigation from '@/components/Navigation'
import { Toaster } from '@/components/ui/toaster'

const inter = Inter({ subsets: ['latin'] })

export const metadata: Metadata = {
  title: 'Assignment Portal',
  description: 'Streamlined submission management for educators and students',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <SessionProvider>
          <DataProvider>
            <Navigation />
            {children}
            <Toaster />
          </DataProvider>
        </SessionProvider>
      </body>
    </html>
  )
}
```

### Step 12: Create Page Structure

Create all page files:

**`src/app/page.tsx`** (root redirect):
```typescript
'use client'

import { useSession } from 'next-auth/react'
import { redirect } from 'next/navigation'
import { useEffect } from 'react'

export default function Home() {
  const { data: session, status } = useSession()

  useEffect(() => {
    if (status === 'loading') return
    
    if (session) {
      redirect('/dashboard')
    } else {
      redirect('/login')
    }
  }, [session, status])

  return (
    <div className="min-h-screen flex items-center justify-center">
      <div className="text-lg">Loading...</div>
    </div>
  )
}
```

**`src/app/login/page.tsx`**:
```typescript
'use client'

import { useState, useEffect } from 'react'
import { signIn, useSession } from 'next-auth/react'
import { useRouter } from 'next/navigation'
// Copy your existing Login component and adapt it
// Replace useAuth() with useSession()
// Replace login() with signIn()
// Replace navigate() with router.push()

export default function LoginPage() {
  const { data: session } = useSession()
  const router = useRouter()

  useEffect(() => {
    if (session) {
      router.push('/dashboard')
    }
  }, [session, router])

  // Your existing login component logic here...
  // Just replace the auth hooks and functions
}
```

**`src/app/dashboard/page.tsx`**:
```typescript
'use client'

import { useSession } from 'next-auth/react'
import { redirect } from 'next/navigation'
// Copy your existing Dashboard component

export default function DashboardPage() {
  const { data: session, status } = useSession()

  if (status === 'loading') {
    return <div className="min-h-screen flex items-center justify-center">Loading...</div>
  }
  
  if (!session) {
    redirect('/login')
  }

  // Copy your existing Dashboard component logic
  // Replace user from useAuth() with session.user
}
```

Repeat similar pattern for all other pages:
- `src/app/assignments/page.tsx`
- `src/app/submit/page.tsx`
- `src/app/my-submissions/page.tsx`
- `src/app/create-assignment/page.tsx`
- `src/app/submissions/page.tsx`

### Step 13: Environment Variables

Create `.env.local`:
```env
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=c1f5f7f8d9e8a7b6c5d4e3f2a1b0c9d8e7f6a5b4c3d2e1f0
```

### Step 14: Create PostCSS Config

Create `postcss.config.js`:
```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

### Step 15: Fix Next.js Config

Create `next.config.js`:
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    optimizePackageImports: ['lucide-react'],
  },
}

module.exports = nextConfig
```

### Step 16: Test the Conversion

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Open http://localhost:3000
```

## ✅ Verification Checklist

Test each of these features:

- [ ] **Login page loads with proper styling**
- [ ] **Login works for both instructor and student**
- [ ] **Desktop navigation shows on large screens** 
- [ ] **Mobile header shows on small screens**
- [ ] **Bottom navigation shows on mobile**
- [ ] **Logout button works and redirects to login**
- [ ] **All pages load without errors**
- [ ] **Dashboard charts render correctly**
- [ ] **All links in navigation work**
- [ ] **Responsive design works on all screen sizes**
- [ ] **Create assignment form works**
- [ ] **Submit assignment works**
- [ ] **Data persists in localStorage**

## 🚨 Common Issues & Solutions

### Navigation Not Showing
```typescript
// Make sure Navigation is in layout.tsx
// Check useSession() returns valid data
// Verify session.user exists before rendering
if (!session?.user) return null
```

### CSS Variables Missing
```css
/* Ensure all CSS variables are in globals.css */
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  /* ... all other variables */
}
```

### Import Errors
```typescript
// All UI component imports must use @/ prefix
import { cn } from "@/lib/utils"
import { Button } from "@/components/ui/button"
```

### Bottom Navbar Not Showing
```typescript
// Check lg:hidden class is applied
<nav className="lg:hidden fixed bottom-0 left-0 right-0">
```

### Authentication Not Working
```typescript
// Verify NextAuth configuration
// Check environment variables
// Ensure callbacks are correct
```

## 🎯 Key Differences

| React + Vite | Next.js |
|--------------|---------|
| `useAuth()` | `useSession()` |
| `login()` | `signIn()` |
| `logout()` | `signOut()` |
| `<Link to="">` | `<Link href="">` |
| `useLocation()` | `usePathname()` |
| `navigate()` | `router.push()` |

## 🚀 Success!

Your Next.js conversion should now have:
- ✅ Working authentication with NextAuth.js
- ✅ Responsive navigation (desktop + mobile)
- ✅ All UI components working
- ✅ Proper TypeScript types
- ✅ Complete functionality from original app

The navbar should now show correctly on all screen sizes with proper logout functionality!
