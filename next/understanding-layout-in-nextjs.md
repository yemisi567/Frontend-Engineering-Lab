# Understanding Layouts in Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

Most web applications share common UI elements across multiple pages, such as:

- Navigation bars
- Sidebars
- Headers
- Footers
- Authentication wrappers
- Dashboard shells

Without layouts, you would have to duplicate these components on every page. This leads to repetitive code, inconsistent UI, and unnecessary re-rendering when users navigate between pages.

Traditional React applications often solve this by wrapping routes in shared components or manually composing layouts. While this works, it becomes harder to maintain as the application grows.

Next.js provides **Layouts** to solve this problem. Layouts allow you to define shared UI once and reuse it across multiple pages while preserving state during navigation.

## The Solution

A **Layout** is a React component that wraps one or more pages.

Unlike normal components, layouts are **persistent**. When navigating between pages that share the same layout, Next.js keeps the layout mounted instead of recreating it. This improves performance and preserves client-side state such as:

- Sidebar expansion
- Scroll position
- Search input values
- Theme settings
- Navigation menus

Layouts are created using a special `layout.tsx` (or `layout.jsx`) file inside the **App Router**.

### Root Layout

Every App Router application must have a **Root Layout**.

It is located at:

```text
app/
 ├── layout.tsx
 ├── page.tsx
```

The Root Layout is required because it defines the HTML document structure for your application.

Example:

```tsx
// app/layout.tsx

import "./globals.css";

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

The Root Layout is the perfect place for:

- Global CSS
- Theme providers
- Authentication providers
- Context providers
- Fonts
- Metadata
- Navigation that should appear on every page

For example:

```text
app/
 ├── layout.tsx
 ├── page.tsx
 ├── about/
 │    └── page.tsx
 └── contact/
      └── page.tsx
```

The rendered UI becomes:

```text
<html>
  <body>
    Root Layout
      Home / About / Contact
  </body>
</html>
```

Every page automatically shares the Root Layout.

### Nested Layouts

Large applications often have sections that need their own layout.

For example:

- Dashboard
- Admin panel
- Documentation
- User settings

Instead of wrapping every page manually, you can create another `layout.tsx` inside a route segment.

Example folder structure:

```text
app/
├── layout.tsx
├── page.tsx
├── dashboard/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── analytics/
│   │    └── page.tsx
│   └── settings/
│        └── page.tsx
```

Dashboard layout:

```tsx
// app/dashboard/layout.tsx

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="dashboard">
      <aside>Sidebar</aside>

      <main>{children}</main>
    </div>
  );
}
```

Now every page inside the `dashboard` folder automatically receives the sidebar.

Rendering hierarchy:

```text
Root Layout
    └── Dashboard Layout
            ├── Dashboard Page
            ├── Analytics Page
            └── Settings Page
```

This keeps dashboard-specific UI isolated from the rest of the application.

### Multiple Root Layouts

Sometimes different parts of your application require completely different root layouts.

Examples include:

- Marketing website
- Authentication pages
- Admin dashboard
- Customer portal

Next.js supports this using **Route Groups**.

Example:

```text
app/
├── (marketing)/
│   ├── layout.tsx
│   ├── page.tsx
│   └── pricing/
│       └── page.tsx
│
├── (dashboard)/
│   ├── layout.tsx
│   ├── dashboard/
│   │    └── page.tsx
│   └── settings/
│        └── page.tsx
│
└── globals.css
```

The folders inside parentheses **do not appear in the URL**.

URLs remain:

```text
/
/pricing
/dashboard
/settings
```

But each section uses its own layout.

Marketing pages may have:

- Landing page navigation
- Public footer
- Hero sections

Dashboard pages may have:

- Sidebar
- User profile
- Notifications
- Admin navigation

This separation keeps each part of the application clean and maintainable.

### Layout Composition

Layouts are composed automatically by Next.js.

For a route like:

```text
/dashboard/settings
```

Next.js renders:

```text
Root Layout
    ↓
Dashboard Layout
    ↓
Settings Page
```

Each layout wraps the one below it, creating a tree of shared UI.

Without layouts, every page repeats the same structure.

```tsx
export default function Dashboard() {
  return (
    <>
      <Navbar />
      <Sidebar />

      <main>Dashboard Content</main>
    </>
  );
}
```

```tsx
export default function Settings() {
  return (
    <>
      <Navbar />
      <Sidebar />

      <main>Settings Content</main>
    </>
  );
}
```

This duplicates UI across pages. The layout handles the shared UI, while each page focuses only on its own content.

## Tradeoffs

- **When this shines:** Large applications with shared UI, dashboards, admin panels, blogs, and documentation websites.
- **When to avoid it:** Very small applications where only one or two pages exist and shared UI is minimal.
- **What you give up:** Because layouts persist across navigation, you cannot rely on them remounting when changing pages. If a component must reset on every navigation, it belongs in the page rather than the layout.

## Key Takeaways

- Every App Router project must have a **Root Layout** (`app/layout.tsx`).
- Layouts provide shared UI that persists across page navigation.
- Nested layouts allow different sections of an application to have their own shared interface.
- Route Groups let you create multiple root layouts without affecting the URL structure.
- Keeping shared UI in layouts reduces duplication, improves maintainability, and provides a better navigation experience.

## References

- [/building-your-application/routing/pages-and-layouts](https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts)
- [/api-reference/file-conventions/layout](https://nextjs.org/docs/app/api-reference/file-conventions/layout)
- [/building-your-application/routing/route-groups](https://nextjs.org/docs/app/building-your-application/routing/route-groups)
