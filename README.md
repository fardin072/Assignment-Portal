# Convert Assignment Portal to Next.js - Step by Step

## 🎯 Overview
This guide converts your current React + Vite Assignment Portal to Next.js 14+ with App Router, maintaining all existing features while adding NextAuth.js authentication.

## 📋 Prerequisites
- Node.js 18.17+
- Your current project files
- 30-45 minutes for conversion

---

## 🚀 Step-by-Step Conversion

### Step 1: Create New Next.js Project
```bash
# Create new Next.js project in a separate directory
npx create-next-app@latest assignment-portal-nextjs \
  --typescript \
  --tailwind \
  --eslint \
  --app \
  --src-dir \
  --import-alias "@/*"

cd assignment-portal-nextjs
```

### Step 2: Install Dependencies
```bash
# Copy these exact dependencies from your current package.json
npm install \
  @hookform/resolvers \
  @radix-ui/react-accordion @radix-ui/react-alert-dialog @radix-ui/react-aspect-ratio \
  @radix-ui/react-avatar @radix-ui/react-checkbox @radix-ui/react-collapsible \
  @radix-ui/react-context-menu @radix-ui/react-dialog @radix-ui/react-dropdown-menu \
  @radix-ui/react-hover-card @radix-ui/react-label @radix-ui/react-menubar \
  @radix-ui/react-navigation-menu @radix-ui/react-popover @radix-ui/react-progress \
  @radix-ui/react-radio-group @radix-ui/react-scroll-area @radix-ui/react-select \
  @radix-ui/react-separator @radix-ui/react-slider @radix-ui/react-slot \
  @radix-ui/react-switch @radix-ui/react-tabs @radix-ui/react-toast \
  @radix-ui/react-toggle @radix-ui/react-toggle-group @radix-ui/react-tooltip \
  class-variance-authority clsx cmdk \
  date-fns embla-carousel-react framer-motion \
  input-otp lucide-react next-themes \
  react-day-picker react-hook-form react-resizable-panels \
  recharts sonner tailwind-merge tailwindcss-animate \
  vaul zod

# Add NextAuth.js
npm install next-auth

# Development dependencies
npm install -D @types/node
```

### Step 3: Copy Your UI Components
```bash
# Copy your entire UI components folder
cp -r /path/to/current/project/client/components/ui ./src/components/ui
cp -r /path/to/current/project/client/lib ./src/lib
cp -r /path/to/current/project/client/hooks ./src/hooks
```

### Step 4: Setup NextAuth.js

Create `src/app/api/auth/[...nextauth]/route.ts`:
```typescript
import NextAuth from 'next-auth'
import CredentialsProvider from 'next-auth/providers/credentials'

const users = [
  { id: '1', name: 'Dr. Smith', email: 'instructor@example.com', password: 'password', role: 'instructor' },
  { id: '2', name: 'John Student', email: 'student@example.com', password: 'password', role: 'student' },
]

export const authOptions = {
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
      if (user) token.role = user.role
      return token
    },
    async session({ session, token }) {
      if (token) {
        session.user.id = token.sub
        session.user.role = token.role
      }
      return session
    },
  },
  pages: { signIn: '/login' },
}

const handler = NextAuth(authOptions)
export { handler as GET, handler as POST }
```

### Step 5: Create Type Definitions

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

Create `src/types/index.ts` (copy your current shared types):
```typescript
// Copy content from your current shared/auth.ts
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

### Step 6: Setup App Layout

Replace `src/app/layout.tsx`:
```typescript
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'
import { Providers } from '@/components/providers'

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
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```

### Step 7: Create Providers

Create `src/components/providers.tsx`:
```typescript
'use client'

import { SessionProvider } from 'next-auth/react'
import { DataProvider } from '@/contexts/DataContext'
import { Toaster } from '@/components/ui/toaster'

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <SessionProvider>
      <DataProvider>
        {children}
        <Toaster />
      </DataProvider>
    </SessionProvider>
  )
}
```

### Step 8: Copy and Convert Your Context

Create `src/contexts/DataContext.tsx` (copy your current client/contexts/DataContext.tsx):
```typescript
'use client'

// Copy the entire content from your current DataContext.tsx file
// No changes needed - it will work as-is in Next.js
```

### Step 9: Convert Navigation Component

Create `src/components/Navigation.tsx`:
```typescript
'use client'

import { useSession, signOut } from 'next-auth/react'
import Link from 'next/link'
import { usePathname } from 'next/navigation'
// Copy the rest of your Navigation component
// Replace React Router's useLocation with usePathname
// Replace logout() with signOut()
// Replace <Link to="/path"> with <Link href="/path">
```

### Step 10: Create Page Structure

Create these directories and files in `src/app/`:

```
src/app/
├── page.tsx (redirect to dashboard)
├── login/page.tsx
├── dashboard/page.tsx
├── assignments/page.tsx
���── submit/page.tsx
├── my-submissions/page.tsx
├── create-assignment/page.tsx
└── submissions/page.tsx
```

### Step 11: Convert Root Page

Replace `src/app/page.tsx`:
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

  return <div>Loading...</div>
}
```

### Step 12: Convert Login Page

Create `src/app/login/page.tsx`:
```typescript
'use client'

import { useState } from 'react'
import { signIn, useSession } from 'next-auth/react'
import { redirect } from 'next/navigation'
// Copy your Login component content
// Replace useAuth with NextAuth hooks
// Replace login() with signIn()
```

### Step 13: Convert Dashboard Page

Create `src/app/dashboard/page.tsx`:
```typescript
'use client'

import { useSession } from 'next-auth/react'
import { redirect } from 'next/navigation'
// Copy your Dashboard component
// Replace useAuth with useSession

export default function DashboardPage() {
  const { data: session, status } = useSession()

  if (status === 'loading') return <div>Loading...</div>
  if (!session) redirect('/login')

  // Copy your existing Dashboard component logic here
  // Replace user from useAuth() with session.user
}
```

### Step 14: Convert Remaining Pages

For each remaining page (`assignments`, `submit`, etc.):
1. Copy your existing page component
2. Wrap in 'use client' directive
3. Replace `useAuth` with `useSession`
4. Replace `user` with `session.user`
5. Replace React Router navigation with Next.js navigation

### Step 15: Fix Tailwind CSS Configuration

Replace `tailwind.config.ts` with your exact current config:
```typescript
import type { Config } from "tailwindcss";

export default {
  darkMode: ["class"],
  content: ["./src/**/*.{ts,tsx}"],
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
        sidebar: {
          DEFAULT: "hsl(var(--sidebar-background))",
          foreground: "hsl(var(--sidebar-foreground))",
          primary: "hsl(var(--sidebar-primary))",
          "primary-foreground": "hsl(var(--sidebar-primary-foreground))",
          accent: "hsl(var(--sidebar-accent))",
          "accent-foreground": "hsl(var(--sidebar-accent-foreground))",
          border: "hsl(var(--sidebar-border))",
          ring: "hsl(var(--sidebar-ring))",
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
```

### Step 16: Copy Global CSS with All Variables

Replace `src/app/globals.css` with your exact current CSS:
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
    --sidebar-background: 0 0% 98%;
    --sidebar-foreground: 240 5.3% 26.1%;
    --sidebar-primary: 240 5.9% 10%;
    --sidebar-primary-foreground: 0 0% 98%;
    --sidebar-accent: 240 4.8% 95.9%;
    --sidebar-accent-foreground: 240 5.9% 10%;
    --sidebar-border: 220 13% 91%;
    --sidebar-ring: 217.2 91.2% 59.8%;
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
    --sidebar-background: 240 5.9% 10%;
    --sidebar-foreground: 240 4.8% 95.9%;
    --sidebar-primary: 224.3 76.3% 48%;
    --sidebar-primary-foreground: 0 0% 100%;
    --sidebar-accent: 240 3.7% 15.9%;
    --sidebar-accent-foreground: 240 4.8% 95.9%;
    --sidebar-border: 240 3.7% 15.9%;
    --sidebar-ring: 217.2 91.2% 59.8%;
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

### Step 17: Fix Navigation Component for Next.js

Update `src/components/Navigation.tsx` - **CRITICAL FIX**:
```typescript
'use client'

import React from 'react'
import { useSession, signOut } from 'next-auth/react'
import Link from 'next/link'
import { usePathname } from 'next/navigation'
import { Button } from './ui/button'
import { Avatar, AvatarFallback } from './ui/avatar'
import {
  GraduationCap,
  LayoutDashboard,
  FileText,
  PlusCircle,
  ClipboardList,
  Send,
  LogOut,
} from 'lucide-react'
import { cn } from '../lib/utils'

export default function Navigation() {
  const { data: session } = useSession()
  const pathname = usePathname()

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

  const handleLogout = () => {
    signOut({ callbackUrl: '/login' })
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

      <div className="lg:hidden h-16"></div>
    </>
  )
}
```

### Step 18: Environment Variables

Create `.env.local`:
```
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-secret-key-here-generate-with-openssl
```

### Step 18: Test the Conversion

```bash
# Start development server
npm run dev

# Test all functionality:
# ✅ Login as instructor/student
# ✅ Navigation works
# ✅ All pages load
# ✅ Create assignments
# ✅ Submit assignments
# ✅ Review submissions
```

---

## 🔄 Key Differences Checklist

- [ ] **React Router** → **Next.js App Router**
- [ ] **useAuth()** → **useSession()**
- [ ] **login/logout** → **signIn/signOut**
- [ ] **<Link to="">** → **<Link href="">**
- [ ] **useLocation** → **usePathname**
- [ ] **navigate()** → **redirect()**
- [ ] **'use client'** added to interactive components

## ✅ Verification Steps

1. **Authentication**: Login/logout works
2. **Navigation**: All links work correctly
3. **Responsive**: Bottom navbar on mobile
4. **Data**: CRUD operations work
5. **Charts**: Recharts display properly
6. **Forms**: All forms submit correctly

## 🚀 Deployment Ready

Your Next.js app is now ready for deployment to Vercel, Netlify, or any other platform!

```bash
npm run build
npm start
```

**Total Conversion Time: ~30-45 minutes**

This maintains all your existing features while upgrading to Next.js with proper authentication and routing!
