# Private Folder in Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

As your Next.js application grows, the `app` directory can become cluttered with files that are only meant to support a route. These may include:

- Utility functions
- Data fetching helpers
- Validation schemas
- Constants
- Custom hooks
- Internal components

Since the App Router creates routes based on the file system, it's natural to wonder whether adding more folders will accidentally create new routes.

For example, you might organize your project like this:

```text
app/
├── dashboard/
│   ├── page.tsx
│   ├── utils/
│   ├── hooks/
│   └── components/
```

Although folders without special files (`page.tsx`, `layout.tsx`, `route.ts`, etc.) don't become routes, Next.js provides an even clearer way to indicate that a folder contains implementation details that should never be treated as part of the routing structure.

This is where **Private Folders** come in.

## The Solution

A **Private Folder** is a folder whose name begins with an underscore (`_`).

```text
app/
└── dashboard/
    ├── page.tsx
    ├── _lib/
    ├── _components/
    └── _hooks/
```

Folders prefixed with `_` are ignored by the Next.js routing system. They exist purely for organizing code.

Private folders help communicate that their contents are **internal implementation details** for a specific route or feature.

### Why use Private Folders?

Private folders provide several benefits:

- Keep route-specific code close to the route that uses it.
- Clearly separate implementation details from route segments.
- Prevent accidental route creation.
- Improve project organization as applications grow.
- Make it easier for other developers to understand which files are intended for reuse and which are local to a feature.

### Example

Suppose you have a dashboard page that needs helper functions and validation logic.

```text
app/
└── dashboard/
    ├── page.tsx
    ├── _lib/
    │   ├── fetch-users.ts
    │   └── format-date.ts
    └── _components/
        └── UserTable.tsx
```

You can import files normally:

```tsx
import { fetchUsers } from "./_lib/fetch-users";
import { UserTable } from "./_components/UserTable";

export default async function DashboardPage() {
  const users = await fetchUsers();

  return <UserTable users={users} />;
}
```

Even though `_lib` and `_components` are inside the `app` directory, they **do not become routes**.

## Tradeoffs

- **When this shines:** Large applications where each route has its own helpers, components, hooks, or business logic.
- **When to avoid it:** Very small projects where adding many folders creates unnecessary complexity.
- **What you give up:** Route-specific code becomes less reusable. If multiple routes need the same utilities, move them to a shared location like `lib/` or `components/` instead of duplicating them in multiple private folders.

## Key Takeaways

- A **Private Folder** is any folder whose name starts with an underscore (`_`).
- Private folders are ignored by the Next.js routing system and never become routes.
- Use folders like `_lib`, `_components`, and `_hooks` to organize route-specific implementation details.
- Keeping implementation code close to the route improves maintainability and project structure.
- Shared code should live outside private folders in common directories such as `lib/` or `components/`.

## References

- [app/building-your-application/routing/colocation](https://nextjs.org/docs/app/building-your-application/routing/colocation)
- [nextjs.org/docs/app](https://nextjs.org/docs/app)
