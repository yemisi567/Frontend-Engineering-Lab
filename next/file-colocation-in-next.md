# File Colocation in Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

As an application grows, organizing files becomes increasingly difficult. In a traditional React project, components, hooks, styles, utilities, and tests are often grouped by file type.

For example:

```text
src/
├── components/
│   ├── Button.tsx
│   ├── ProductCard.tsx
├── hooks/
│   ├── useProducts.ts
├── styles/
│   ├── product.css
├── utils/
│   ├── formatPrice.ts
└── pages/
    ├── products.tsx
```

While this structure works, finding everything related to a single feature can become frustrating. Every time you work on the product page, you may need to jump between several folders.

As projects become larger, this scattered structure makes development slower and maintenance more difficult.

## The Solution

Next.js App Router introduces **file colocation**, a pattern that allows you to keep files related to a route together.

Instead of organizing your project by file type, you organize it by **feature** or **route**.

The App Router only treats specific files as routes or special files, such as:

- `page.tsx`
- `layout.tsx`
- `loading.tsx`
- `error.tsx`
- `not-found.tsx`
- `route.ts`

Every other file inside the folder is ignored by the routing system, allowing you to colocate components, hooks, utilities, styles, tests, and other supporting files next to the page that uses them.

### How File Colocation Works

Suppose you're building a dashboard.

Instead of placing every component inside a global `components` folder. Your folder might look like this:

```text
app/
└── dashboard/
    ├── page.tsx
    ├── layout.tsx
    ├── loading.tsx
    ├── UserCard.tsx
    ├── Sidebar.tsx
    ├── useDashboard.ts
    ├── dashboard.css
    ├── formatDate.ts
    └── dashboard.test.tsx
```

Only these files become part of Next.js routing:

- `page.tsx`
- `layout.tsx`
- `loading.tsx`

The remaining files are simply supporting files that can be imported where needed.

```tsx
// app/dashboard/page.tsx

import UserCard from "./UserCard";
import Sidebar from "./Sidebar";

export default function DashboardPage() {
  return (
    <>
      <Sidebar />
      <UserCard />
    </>
  );
}
```

Since `UserCard.tsx` is only used by the dashboard page, keeping it inside the same folder makes the project easier to understand.

### Colocate Shared vs Route-Specific Files

A common question is:

> **Should every component be colocated?**

Not necessarily.

#### Colocate files when they belong to one route

```text
app/
└── products/
    ├── page.tsx
    ├── ProductCard.tsx
    └── ProductFilter.tsx
```

If `ProductCard` is only used on the products page, keeping it inside the route folder is a great choice.

#### Move shared files outside the route

If multiple routes need the same component, place it in a shared folder.

```text
components/
├── Button.tsx
├── Navbar.tsx
└── Modal.tsx
```

These components can then be imported anywhere in the application.

A good rule of thumb is:

- **Used in one route?** Colocate it.
- **Used in multiple routes?** Move it to a shared location.

### Benefits of File Colocation

- Easier Navigation: Everything related to a feature is located in one folder, reducing the time spent searching for files.
- Better Maintainability: Developers can quickly understand a feature without exploring unrelated parts of the project.
- Better Scalability: As your application grows, each feature remains self-contained, making it easier to modify or remove.
- Cleaner Imports: Because related files are close together, imports become shorter.

```tsx
import UserCard from "./UserCard";
import useDashboard from "./useDashboard";
```

instead of

```tsx
import UserCard from "@/components/dashboard/UserCard";
import useDashboard from "@/hooks/dashboard/useDashboard";
```

### Best Practices

- Keep route-specific files inside the route folder.
- Extract components to a shared folder only when multiple routes need them.
- Avoid placing every component in a global `components` directory by default.
- Keep helper functions, custom hooks, styles, and tests close to the feature that uses them.
- Don't over-colocate. If a file becomes widely reused, move it to a shared location.

## Tradeoffs

File colocation has advantages and limitations.

- **When this shines:** Large applications with many routes where keeping feature-related files together improves maintainability and developer productivity.
- **When to avoid it:** For utilities or UI components shared across many routes. Duplicating shared code in multiple route folders can make maintenance harder.
- **What you give up:** You may end up with more files inside a single route folder, so it's important to keep the folder organized and extract reusable code when appropriate.

## Key Takeaways

- File colocation means keeping route-specific components, hooks, styles, and utilities alongside the route that uses them.
- In the App Router, only special files like `page.tsx`, `layout.tsx`, `loading.tsx`, and `error.tsx` affect routing; other files are ignored by the router.
- Colocation makes features easier to navigate, understand, and maintain.
- Shared components should live in a common directory, while route-specific code should stay with its route.
- Organizing by feature instead of file type helps Next.js applications scale more effectively.

## References

- Next.js App Router documentation: [https://nextjs.org/docs/app](https://nextjs.org/docs/app)
- Project Organization: [https://nextjs.org/docs/app/getting-started/project-structure](https://nextjs.org/docs/app/getting-started/project-structure)
- Routing Fundamentals: [https://nextjs.org/docs/app/building-your-application/routing](https://nextjs.org/docs/app/building-your-application/routing)
