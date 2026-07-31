# Introduction to Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

React is one of the most popular libraries for building user interfaces. It makes it easy to create reusable components and build interactive web applications.

However, React focuses only on the **view layer** of an application. It doesn't provide everything required to build a complete production-ready application.

When building a real-world React application, you'll often need additional tools for:

- Routing
- Data fetching
- Search Engine Optimization (SEO)
- Image optimization
- Authentication
- Server-side rendering
- API endpoints
- Performance optimization

To add these features, developers typically install and configure multiple third-party libraries such as React Router, TanStack Query, authentication libraries, image optimization tools, and more.

As your application grows, managing all these tools can become complex and time-consuming.

This is where **Next.js** helps.

Next.js is a React framework that provides many of these features out of the box, allowing you to focus on building your application instead of configuring your tooling.

## The Solution

### What is Next.js?

Next.js is an open-source React framework created by **Vercel**.

It extends React with features that make building modern, production-ready web applications faster and easier.

Some of the core features include:

- File-based routing
- Server Components and Client Components
- Multiple rendering strategies (SSR, SSG, ISR, CSR)
- Built-in data fetching
- Route Handlers for building APIs
- Image optimization
- Font optimization
- Script optimization
- Metadata API for SEO
- TypeScript support
- Fast Refresh
- Production-ready build system

Instead of installing and configuring many libraries yourself, Next.js provides sensible defaults and conventions that help you build scalable applications.

### Creating Your First Next.js Project

Create a new project using:

```bash
npx create-next-app@latest
```

or

```bash
npm create next-app@latest
```

You'll be asked a few questions during installation:

```text
✔ What is your project named?
✔ Would you like to use TypeScript?
✔ Would you like to use ESLint?
✔ Would you like to use Tailwind CSS?
✔ Would you like your code inside a src/ directory?
✔ Would you like to use the App Router?
✔ Would you like to use Turbopack?
✔ Would you like to customize the import alias?
```

Navigate into your project:

```bash
cd my-next-app
```

Start the development server:

```bash
npm run dev
```

Visit:

```
http://localhost:3000
```

Your first Next.js application should now be running.

### Understanding the Project Structure

A typical Next.js project looks like this:

```text
my-next-app/

├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── globals.css
│
├── public/
├── components/
├── lib/
├── next.config.ts
├── package.json
└── tsconfig.json
```

#### `app/`

The `app` directory contains your application's routes.

Each folder represents a route, and every route must contain a `page.tsx` file.

Example:

```text
app/
│
├── page.tsx
├── about/
│   └── page.tsx
└── contact/
    └── page.tsx
```

Creates:

```
/
/about
/contact
```

#### `layout.tsx`

A layout wraps pages and is shared across multiple routes.

Instead of repeating common UI like navigation bars or footers on every page, you define them once inside a layout.

Example:

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

#### `public/`

Stores static assets such as:

- Images
- Videos
- Fonts
- Icons

Example:

```text
public/logo.png
```

Use it like this:

```tsx
<img src="/logo.png" alt="Logo" />
```

#### `components/`

A common place to store reusable UI components.

Example:

```text
components/
    Navbar.tsx
    Footer.tsx
    Button.tsx
```

#### `lib/`

Many developers use this folder for shared utilities such as:

- API clients
- Database functions
- Helper functions
- Validation schemas

#### `next.config.ts`

Contains project-specific Next.js configuration.

### creating your first Next.js page

Create the following page inside `app/page.tsx`:

```tsx
export default function Home() {
  return <h1>Hello, World!</h1>;
}
```

Visit:

```
http://localhost:3000
```

You'll see:

```
Hello, World!
```

Congratulations! You've created your first Next.js page.

### Client Components vs Server Components

By default, every component in the `app` directory is a **Server Component**.

Server Components render on the server before being sent to the browser. They can fetch data directly and reduce the amount of JavaScript sent to the client.

If a component needs browser features such as:

- `useState`
- `useEffect`
- Event handlers (`onClick`)
- Browser APIs (`localStorage`, `window`, etc.)

you must mark it as a **Client Component** using the `"use client"` directive.

Example:

```tsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

#### What about `"use server"`?

Unlike `"use client"`, you usually **do not need** to add `"use server"` to regular Server Components because they are server-rendered by default.

The `"use server"` directive is primarily used to define **Server Actions**, allowing functions to execute securely on the server.

Example:

```tsx
"use server";

export async function createPost(formData: FormData) {
  // Save data to the database
}
```

### Why Developers Like Next.js

Next.js simplifies modern web development by providing:

- Better SEO through server rendering
- Built-in routing
- Optimized images and fonts
- Fast page loading
- API development with Route Handlers
- Automatic code splitting
- Streaming and Server Components
- Great developer experience

These features make it an excellent choice for:

- Blogs
- Portfolios
- Company websites
- SaaS products
- Dashboards
- E-commerce applications
- Marketing websites

## Tradeoffs

- **When this shines:** Best for Production-ready React applications, SEO-focused websites, Large-scale applications, E-commerce, SaaS products, and Content-heavy websites.
- **When to avoid it:** Small React learning projects, Simple single-page applications that don't require SEO or server rendering, and Teams that only need client-side rendering.
- **What you give up:** More concepts to learn than React alone, Different rendering strategies (SSR, SSG, ISR, CSR, RSC), A framework with conventions and opinions and Slightly steeper learning curve

## Key Takeaways

- React is a library for building user interfaces, while Next.js is a full React framework for building production-ready applications.
- Next.js provides routing, rendering strategies, data fetching, optimization, and API capabilities out of the box.
- The App Router uses file-based routing, making navigation simple and intuitive.
- Components are Server Components by default; use `"use client"` only when browser interactivity is required.
- Next.js helps developers build faster, more scalable, and SEO-friendly applications with minimal configuration.

## References

- [https://nextjs.org/docs](https://nextjs.org/docs)
- [https://nextjs.org/learn](https://nextjs.org/learn)
- [https://react.dev](https://react.dev)
