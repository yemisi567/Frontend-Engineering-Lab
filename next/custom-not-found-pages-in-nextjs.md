# Custom Not Found (404) Pages in Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

Users don't always navigate to valid pages. They might:

- Enter an incorrect URL.
- Click an outdated bookmark.
- Follow a broken link from another website.
- Request a resource that no longer exists.

In a traditional React application, you typically configure a catch-all (`*`) route in React Router to display a custom 404 page.

In Next.js, this is handled differently. The framework automatically detects unknown routes and renders a **404 Not Found** page. While the default page works, most applications need a custom experience that matches their branding and helps users navigate back to useful content.

## The Solution

The Next.js App Router provides built-in support for custom **404 Not Found** pages. You can:

- Create a global `not-found.tsx` page.
- Display a custom 404 page for specific route segments.
- Programmatically show a 404 page using the `notFound()` function when requested data doesn't exist.
- This article applies to the Next.js App Router (app/), not the Pages Router (pages/).

### Global Not Found Page

Create a not-found.tsx file inside the app directory to define a custom global 404 page.

```
app/
├── layout.tsx
├── page.tsx
└── not-found.tsx
```

```tsx
// app/not-found.tsx

import Link from "next/link";

export default function NotFound() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-center gap-4">
      <h1 className="text-5xl font-bold">404</h1>

      <p className="text-gray-600">
        Sorry, the page you're looking for doesn't exist.
      </p>

      <Link href="/" className="rounded bg-black px-4 py-2 text-white">
        Go back home
      </Link>
    </main>
  );
}
```

Whenever a user visits a route that doesn't exist, Next.js automatically renders this page.

### Handling Missing Resources with notFound()

Sometimes the route exists, but the requested resource does not.

For example:

- `/blog/nextjs-routing` → exists ✅
- `/blog/random-post` → does not exist ❌

Instead of showing an error or blank page, you can render the 404 page by calling `notFound()`.

```tsx
// app/blog/[slug]/page.tsx

import { notFound } from "next/navigation";

export default async function BlogPost({ params }) {
  const post = await getPost(params.slug);

  if (!post) {
    notFound();
  }

  return <article>{post.title}</article>;
}
```

Calling notFound() throws a special Next.js error that immediately stops rendering the current page and displays the nearest not-found.tsx, if post does not exist.

### Route-Specific Not Found Pages

In larger applications, different sections may require their own customized 404 experience.

For example:

```
app/
├── dashboard/
│   ├── page.tsx
│   └── not-found.tsx
├── blog/
│   ├── page.tsx
│   └── not-found.tsx
└── not-found.tsx
```

If `notFound()` is called inside the `dashboard` route, Next.js searches for the closest not-found.tsx in the current route segment. If none exists, it falls back to the global app/not-found.tsx:

```
app/dashboard/not-found.tsx
```

instead of the global one.

This allows different parts of your application to have customized error messages and navigation.

Example:

```tsx
// app/dashboard/not-found.tsx

import Link from "next/link";

export default function DashboardNotFound() {
  return (
    <div>
      <h2>Dashboard page not found</h2>

      <Link href="/dashboard">Return to Dashboard</Link>
    </div>
  );
}
```

### Why Use `notFound()` Instead of Returning JSX?

#### Before

```tsx
if (!user) {
  return <p>User not found</p>;
}
```

The URL still returns a successful page (HTTP 200), which isn't ideal for SEO or APIs.

#### After

```tsx
import { notFound } from "next/navigation";

if (!user) {
  notFound();
}
```

Next.js returns the correct **404 HTTP status** while displaying your custom 404 page.

This improves:

- SEO
- User experience
- Search engine indexing
- Proper HTTP semantics

## Tradeoffs

- **When this shines:** Building production applications where users may request invalid routes or missing resources.
- **When to avoid it:** Don't use `notFound()` for permission or authentication errors. Those should return an authorization page or redirect users to sign in.
- **What you give up:** Calling `notFound()` immediately stops rendering the current page, so no additional code after it will execute.

## Key Takeaways

- Next.js automatically provides a 404 page for routes that don't exist.
- Create `app/not-found.tsx` to customize the global 404 page.
- Use `notFound()` from `next/navigation` when requested data cannot be found.
- Route segments can have their own `not-found.tsx` files for customized experiences.
- Using `notFound()` returns the proper HTTP 404 status, improving SEO and user experience.

## References

- [api-reference/file-conventions/not-found](https://nextjs.org/docs/app/api-reference/file-conventions/not-found)
- [api-reference/functions/not-found](https://nextjs.org/docs/app/api-reference/functions/not-found)
