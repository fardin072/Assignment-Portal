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
├── submit/page.tsx
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

### Step 15: Update Tailwind Config

Replace `tailwind.config.ts`:
```typescript
import type { Config } from 'tailwindcss'

const config: Config = {
  darkMode: ['class'],
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  // Copy your existing theme configuration
}

export default config
```

### Step 16: Environment Variables

Create `.env.local`:
```
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-secret-key-here-generate-with-openssl
```

### Step 17: Copy Global Styles

Copy your `client/global.css` to `src/app/globals.css`

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
