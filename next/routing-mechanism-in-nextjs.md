# Routing Mechanism in Next

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

Navigation is one of the core building blocks of every web application. Whether you're building a blog, e-commerce platform, dashboard, or social media application, users need a way to move between pages seamlessly.

In traditional React applications, developers often have to install and configure third-party routing libraries like React Router. They also need to manually define routes, handle nested layouts, manage dynamic URLs, and implement navigation logic.

Next.js simplifies all of this by providing a **file-based routing system**. Simply creating folders and files inside the `app` directory automatically generates application routes, making routing faster, more intuitive, and easier to maintain.

Understanding how routing works in Next.js allows you to:

- Build scalable applications with clean URL structures.
- Create dynamic pages from database content.
- Share layouts across multiple pages.
- Navigate between pages without full-page reloads.
- Organize large applications more effectively.

## The Solution

Next.js uses **file-system routing**, meaning the folder and file structure inside the `app` directory defines your application's routes automatically.

```
app/
├── page.tsx
├── about/
│   └── page.tsx
├── blog/
│   └── page.tsx
```

Produces:

```
/          -> Home
/about     -> About
/blog      -> Blog
```

### Routing

Every route in the App Router is created by adding a `page.tsx` file inside a folder.

Example:

```
app/
├── page.tsx
├── about/
│   └── page.tsx
├── contact/
│   └── page.tsx
```

```tsx
// app/about/page.tsx

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

Visiting `/about` automatically renders the page.

No route configuration is required.

### Nested Routes

Folders can be nested to create nested URLs.

```
app/
└── dashboard/
    ├── page.tsx
    └── analytics/
        └── page.tsx
```

Generated routes:

```
/dashboard
/dashboard/analytics
```

```tsx
// app/dashboard/analytics/page.tsx

export default function AnalyticsPage() {
  return <h1>Analytics Dashboard</h1>;
}
```

Nested routes help organize large applications naturally.

### Dynamic Routes

Sometimes the URL isn't known ahead of time.

Examples include:

- Blog posts
- Products
- User profiles

Dynamic routes are created using square brackets.

```
app/
└── blog/
    └── [slug]/
        └── page.tsx
```

URLs:

```
/blog/nextjs-routing
/blog/getting-started
/blog/react-hooks
```

```tsx
type Props = {
  params: Promise<{
    slug: string;
  }>;
};

export default async function BlogPost({ params }: Props) {
  const { slug } = await params;

  return <h1>{slug}</h1>;
}
```

`slug` contains the value from the URL.

### Nested Dynamic Routes

Dynamic segments can also be nested.

Example:

```
app/
└── shop/
    └── [category]/
        └── [product]/
            └── page.tsx
```

Routes:

```
/shop/shoes/nike-air-max
/shop/laptops/macbook-pro
```

```tsx
type Props = {
  params: Promise<{
    category: string;
    product: string;
  }>;
};

export default async function ProductPage({ params }: Props) {
  const { category, product } = await params;

  return (
    <>
      <h2>{category}</h2>
      <p>{product}</p>
    </>
  );
}
```

### catach-all segements

Sometimes you don't know how many URL segments you'll receive.

Use `[...]`.

```
app/
└── docs/
    └── [...slug]/
        └── page.tsx
```

Examples:

```
/docs/react
/docs/react/hooks
/docs/react/hooks/use-effect
```

```tsx
type Props = {
  params: Promise<{
    slug: string[];
  }>;
};

export default async function DocsPage({ params }: Props) {
  const { slug } = await params;

  return <p>{slug.join(" / ")}</p>;
}
```

#### Optional Catch-all

If the route should also match `/docs`, use:

```
[[...slug]]
```

This matches:

```
/docs
/docs/react
/docs/react/hooks
```

### Route Groups

Sometimes folders are only used for organization and shouldn't appear in the URL.

Wrap the folder name in parentheses.

```
app/
├── (marketing)/
│   ├── about/
│   └── pricing/
└── (dashboard)/
    └── settings/
```

Generated URLs:

```
/about
/pricing
/settings
```

Notice `(marketing)` and `(dashboard)` are omitted from the URL.

Route groups are useful for:

- Organizing large projects
- Using different layouts
- Separating public and authenticated sections

### Link Component

Instead of using HTML `<a>` tags for internal navigation, use Next.js' `Link` component.

```tsx
import Link from "next/link";

export default function Navbar() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link href="/blog">Blog</Link>
    </nav>
  );
}
```

Benefits include:

- Client-side navigation
- Faster page transitions
- Automatic prefetching of linked pages (when links enter the viewport in production)

### Active Links

You can determine the current route using `usePathname()`.

```tsx
"use client";

import Link from "next/link";
import { usePathname } from "next/navigation";

export default function Navbar() {
  const pathname = usePathname();

  return (
    <nav>
      <Link
        href="/about"
        className={pathname === "/about" ? "text-blue-600 font-bold" : ""}
      >
        About
      </Link>
    </nav>
  );
}
```

This is commonly used to highlight the active navigation item.

### Params & Search Params

#### Route Params

Dynamic route values come from `params`.

```
/blog/nextjs-routing
```

```tsx
type Props = {
  params: Promise<{
    slug: string;
  }>;
};

export default async function Page({ params }: Props) {
  const { slug } = await params;

  return <h1>{slug}</h1>;
}
```

#### Search Params

Query strings are accessed using `searchParams`.

```
/products?category=phones&page=2
```

```tsx
type Props = {
  searchParams: Promise<{
    category?: string;
    page?: string;
  }>;
};

export default async function ProductsPage({ searchParams }: Props) {
  const { category, page } = await searchParams;

  return (
    <>
      <p>Category: {category}</p>
      <p>Page: {page}</p>
    </>
  );
}
```

For Client Components, use the `useSearchParams()` hook.

### Navigating Programmatically

Sometimes navigation happens after an action, such as:

- Form submission
- Login
- Logout
- Creating a resource

Use the `useRouter()` hook.

```tsx
"use client";

import { useRouter } from "next/navigation";

export default function LoginButton() {
  const router = useRouter();

  function handleLogin() {
    // authenticate user...

    router.push("/dashboard");
  }

  return <button onClick={handleLogin}>Login</button>;
}
```

Useful router methods include:

```tsx
router.push("/dashboard"); // Navigate to a new page
router.replace("/login"); // Replace current history entry
router.back(); // Go back
router.forward(); // Go forward
router.refresh(); // Refresh server components
router.prefetch("/dashboard"); // Prefetch a route
```

### Before vs After

#### Before (React Router)

```tsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/about" element={<About />} />
    <Route path="/blog/:slug" element={<Blog />} />
  </Routes>
</BrowserRouter>;
```

Requires manual route configuration.

#### After (Next.js)

```
app/
├── page.tsx
├── about/
│   └── page.tsx
└── blog/
    └── [slug]/
        └── page.tsx
```

No routing configuration is required—your folder structure defines the routes.

## Tradeoffs

No routing solution is perfect. Here are some considerations:

- **When this shines:** Building modern applications with nested layouts, dynamic routes, server rendering, and a clear project structure.
- **When to avoid it:** If you're working in an existing React project that doesn't use Next.js, introducing Next.js solely for routing may not be practical.
- **What you give up:** Because routing is tied to the file system, you have less flexibility than manually configuring every route, although this convention greatly improves consistency and maintainability for most applications.

## Key Takeaways

- Next.js uses a **file-based routing system**, where folders and `page.tsx` files automatically become routes.
- Dynamic routes (`[slug]`), nested routes, and catch-all segments make it easy to build scalable URL structures.
- Route Groups help organize code and apply different layouts without affecting the URL.
- Use the `Link` component for fast client-side navigation and `useRouter()` for programmatic navigation.
- Route parameters (`params`) and query parameters (`searchParams`) allow pages to respond dynamically to the current URL.

## References

- [building-your-application/routing](https://nextjs.org/docs/app/building-your-application/routing)
- [api-reference/functions/use-router](https://nextjs.org/docs/app/api-reference/functions/use-router)
- [api-reference/functions/use-pathname](https://nextjs.org/docs/app/api-reference/functions/use-pathname)
- [api-reference/functions/use-search-params](https://nextjs.org/docs/app/api-reference/functions/use-search-params)
