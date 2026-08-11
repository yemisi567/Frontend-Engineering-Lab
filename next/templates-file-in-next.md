# Templates File Next

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

By default, layouts in the Next.js App Router are **persistent**. Once a layout is mounted, it stays mounted as users navigate between routes that share the same layout.

This is usually desirable because it preserves UI state, improves performance, and avoids unnecessary re-renders. However, there are situations where you **want part of your UI to reset whenever navigation occurs**.

For example:

- A search panel should clear its input whenever the user visits a new page.
- A page transition animation should replay on every navigation.
- A form wizard should start fresh when navigating back to it.
- Component state should be discarded instead of being preserved.

Using a layout for these scenarios won't work because the layout persists across route changes.

This is where **template files** become useful.

## The Solution

A **`template.tsx`** (or `template.jsx`) file is a special file in the Next.js App Router that behaves similarly to a layout, but with one important difference:

> **Templates create a new instance for every navigation.**

Unlike layouts, templates **do not preserve component state** between page transitions. Whenever the user navigates to another route that uses the same template, Next.js remounts the template and all of its children.

```
app/
├── dashboard/
│   ├── template.tsx
│   ├── page.tsx
│   ├── analytics/
│   │   └── page.tsx
│   └── settings/
│       └── page.tsx
```

In this example, navigating between:

- `/dashboard`
- `/dashboard/analytics`
- `/dashboard/settings`

will recreate the template every time.

### Creating a Template

A template looks almost identical to a layout.

```tsx
// app/dashboard/template.tsx

export default function Template({ children }: { children: React.ReactNode }) {
  return <section className="dashboard-template">{children}</section>;
}
```

The key difference is **how Next.js renders it internally**.

### Layout vs Template

Although they have similar syntax, they behave differently.

#### Layout

```tsx
// app/dashboard/layout.tsx

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <Sidebar />
      {children}
    </>
  );
}
```

The layout is mounted once and reused during navigation.

---

#### Template

```tsx
// app/dashboard/template.tsx

export default function Template({ children }: { children: React.ReactNode }) {
  return (
    <>
      <Sidebar />
      {children}
    </>
  );
}
```

This component is recreated every time navigation occurs.

### How Templates Work

Imagine this route structure:

```
app/
└── dashboard/
    ├── template.tsx
    ├── analytics/page.tsx
    └── settings/page.tsx
```

Navigation flow:

```
Dashboard Analytics
        │
        ▼
Template mounts

        │
Navigate

        ▼
Dashboard Settings

        │
        ▼
Old Template unmounts

        │
        ▼
New Template mounts
```

Unlike layouts, the template starts from a fresh state after every navigation.

### When Should You Use Templates?

Templates are useful when you need components to **restart** whenever the route changes.

#### Reset Component State

```tsx
"use client";

import { useState } from "react";

export default function Template({ children }: { children: React.ReactNode }) {
  const [search, setSearch] = useState("");

  return (
    <>
      <input
        value={search}
        onChange={(e) => setSearch(e.target.value)}
        placeholder="Search..."
      />

      {children}
    </>
  );
}
```

Every navigation recreates the template, clearing the search input automatically.

#### Restart Animations

Many animation libraries trigger animations only when a component mounts.

Using a template ensures animations replay on every page transition.

```tsx
"use client";

export default function Template({ children }: { children: React.ReactNode }) {
  return <div className="animate-fade-in">{children}</div>;
}
```

#### Reset Forms

Suppose a multi-step form should always begin from step one whenever users revisit it.

A template provides a fresh instance without manually resetting state.

### Layout and Template Together

You can combine both in the same route.

```
app/
└── dashboard/
    ├── layout.tsx
    ├── template.tsx
    ├── page.tsx
    └── settings/
        └── page.tsx
```

A common pattern is:

- **Layout** → persistent UI such as navigation bars, sidebars, or authentication wrappers.
- **Template** → UI that should reset on every navigation, such as forms, search inputs, or animations.

This lets you preserve global UI while recreating only the parts that need a fresh state.

## Tradeoffs

- **When this shines:** When you need state, effects, or animations to reset on every navigation.
- **When to avoid it:** For shared UI like headers, navigation, sidebars, or providers that should persist across pages.
- **What you give up:** Templates remount on every navigation, so local state is lost and initialization code runs again, which may introduce additional rendering work.

## Key Takeaways

- `template.tsx` is a special App Router file that behaves like a layout but remounts on every navigation.
- Unlike layouts, templates do **not** preserve component state between route changes.
- Use templates for components that should reset, such as forms, search inputs, or page transition animations.
- Use `layout.tsx` for persistent UI and `template.tsx` for UI that should start fresh on each navigation.
- Layouts and templates can be used together to balance performance with predictable component behavior.

## References

- [https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts](https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts)
- [https://nextjs.org/docs/app/api-reference/file-conventions/template](https://nextjs.org/docs/app/api-reference/file-conventions/template)
