# Parallel Intercepting Routes

> **Topic:** Next.js · **Level:** Intermediate · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

Modern applications often need to display different pieces of UI at the same time without forcing the user to navigate away from the current page.

Consider a photo gallery. A user can browse a grid of photos and click one to view its details. A traditional navigation approach might take the user from:

```text
/gallery
```

to:

```text
/gallery/photo-1
```

The problem is that the entire page may be replaced by the photo details page. The user loses the gallery context and has to navigate back to continue browsing.

A better experience is to open the selected photo in a **modal while keeping the gallery visible behind it**.

This is where **Parallel Routes** and **Intercepting Routes** become especially useful in Next.js App Router applications.

- **Parallel Routes** allow multiple route segments to render simultaneously within the same layout.
- **Intercepting Routes** allow a route to be displayed inside the current layout instead of replacing the entire page.

When combined, they can create experiences such as modals, drawers, split views, dashboards, and complex nested interfaces while still preserving normal URL-based navigation.

---

## The Solution

### Parallel Routes

**Parallel Routes** are an advanced routing mechanism in the Next.js App Router that allows multiple pages or route segments to render simultaneously within the same layout.

Instead of having a layout render only one child page, you can define multiple **slots**, with each slot receiving its own route content.

For example, imagine an admin dashboard containing:

```text
┌─────────────────────────────────────────┐
│ Header                                  │
├─────────────┬───────────────────────────┤
│             │                           │
│ Sidebar     │ Main Content              │
│             │                           │
│             │                           │
└─────────────┴───────────────────────────┘
```

The sidebar and main content can be represented by separate route slots.

#### Parallel Routes Use Cases

Parallel Routes are particularly useful for:

- **Dashboards with multiple sections**
- **Split-view interfaces**
- **Complex admin interfaces**
- **Master-detail layouts**
- **Applications with independent navigation areas**
- **Pages that need multiple independently rendered sections**

#### Benefits of Parallel Routes

##### 1. Splitting a Layout into Manageable Slots

Parallel Routes allow a large layout to be divided into smaller, independently managed sections.

This can also make larger applications easier for multiple teams to work on because different parts of the interface can have their own route structure.

##### 2. Independent Route Handling

Each slot can have its own loading, error, and route behavior.

This means one part of the interface can change without necessarily replacing the other parts.

##### 3. Independent Sub-Navigation

Different slots can maintain their own navigation state.

For example, a dashboard might have:

```text
/dashboard
├── @analytics
├── @activity
└── @settings
```

Each slot can represent a different area of the dashboard.

---

#### How to Set Up Parallel Routes

Parallel Routes in Next.js are defined using a feature known as **slots**.

Slots are created using the `@folder` naming convention.

For example:

```text
app/
├── dashboard/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── @analytics/
│   │   └── page.tsx
│   └── @activity/
│       └── page.tsx
```

The `@analytics` and `@activity` directories are **slots**.

They are passed automatically as props to the corresponding layout.

For example:

```tsx
// app/dashboard/layout.tsx

export default function DashboardLayout({
  children,
  analytics,
  activity,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  activity: React.ReactNode;
}) {
  return (
    <div>
      <header>Dashboard</header>

      <main>
        <section>{children}</section>

        <aside>
          {analytics}
          {activity}
        </aside>
      </main>
    </div>
  );
}
```

The important thing to understand is that `@analytics` and `@activity` are **not URL segments**.

For example:

```text
/dashboard/@analytics
```

does **not** become part of the browser URL.

The `@` convention is used to define a slot for the layout.

---

#### Unmatched Routes in Parallel Routes

One important concept when working with Parallel Routes is **unmatched routes**.

A slot may not have a matching route for every URL.

Next.js provides a special `default.tsx` file that can be used to define what should be rendered when a slot does not have a matching route.

For example:

```text
app/
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    └── @analytics/
        ├── page.tsx
        └── default.tsx
```

The `default.tsx` file acts as a fallback for the slot.

```tsx
// app/dashboard/@analytics/default.tsx

export default function DefaultAnalytics() {
  return null;
}
```

This is particularly useful when a parallel slot should display nothing unless a specific route is active.

---

#### Conditional Routes in Parallel Routes

Parallel Routes can also be useful when different users should see different parts of an interface.

For example, an application might have:

- An authenticated user view
- An unauthenticated user view

A layout can decide which content should be rendered based on authentication state.

Conceptually:

```tsx
export default async function Layout({
  children,
  authenticated,
  unauthenticated,
}: {
  children: React.ReactNode;
  authenticated: React.ReactNode;
  unauthenticated: React.ReactNode;
}) {
  const user = await getUser();

  return (
    <>
      {children}

      {user ? authenticated : unauthenticated}
    </>
  );
}
```

The exact authentication implementation will depend on the authentication solution being used, but the key idea is that **different route slots can represent different UI states**.

---

### Intercepting Routes

**Intercepting Routes** are an advanced Next.js routing mechanism that allows a route to be rendered within the context of the current layout.

They are especially useful when you want to show new content without completely replacing the current page.

A common example is a modal.

Imagine a user is browsing:

```text
/gallery
```

and clicks:

```text
Photo 1
```

The actual route for the photo might be:

```text
/gallery/photo-1
```

Normally, navigating to that route would replace the gallery page.

With an Intercepting Route, Next.js can instead display:

```text
┌─────────────────────────────────────────┐
│ Gallery                                 │
│                                         │
│   Photo 2     Photo 3                   │
│                                         │
│        ┌───────────────────┐            │
│        │                   │            │
│        │     Photo 1       │            │
│        │                   │            │
│        │       [ X ]       │            │
│        └───────────────────┘            │
│                                         │
└─────────────────────────────────────────┘
```

The URL can still change to:

```text
/gallery/photo-1
```

but the gallery remains visible underneath the modal.

This gives us both:

- **Good user experience**
- **Shareable and bookmarkable URLs**

---

#### Intercepting Route Conventions

Next.js provides special folder conventions for defining Intercepting Routes.

- `(.)` Matches a segment at the **same level**.

- `(..)` Matches a segment **one level above**.

- `(..)(..)` Matches a segment **two levels above**.

- `(...)` Matches a segment from the **root of the `app` directory**.

These conventions describe the relationship between the interceptor and the route being intercepted.

Importantly, route groups such as `(marketing)` do not count as URL segments when determining these relationships.

---

### Parallel Intercepting Routes Example

A photo gallery is one of the clearest examples of where Parallel Routes and Intercepting Routes work well together.

Suppose our application has this structure:

```text
app/
└── gallery/
    ├── layout.tsx
    ├── page.tsx
    ├── @modal/
    │   ├── default.tsx
    │   └── (.)photo/
    │       └── [id]/
    │           └── page.tsx
    └── photo/
        └── [id]/
            └── page.tsx
```

Here is what each part does:

```text
gallery/
├── page.tsx
```

Displays the main photo gallery.

```text
gallery/photo/[id]/page.tsx
```

Displays the full photo page.

```text
gallery/@modal/
```

Creates a parallel route slot called `modal`.

```text
gallery/@modal/(.)photo/[id]/page.tsx
```

Intercepts navigation to:

```text
/gallery/photo/[id]
```

and renders the photo inside the `modal` slot when navigation happens from the gallery.

---

#### The Gallery Layout

The `layout.tsx` renders both the main page and the modal slot.

```tsx
// app/gallery/layout.tsx

export default function GalleryLayout({
  children,
  modal,
}: {
  children: React.ReactNode;
  modal: React.ReactNode;
}) {
  return (
    <>
      {children}
      {modal}
    </>
  );
}
```

The important part here is:

```tsx
{
  children;
}
{
  modal;
}
```

`children` represents the normal gallery content, while `modal` represents the parallel route slot.

---

#### The Default Modal

When there is no intercepted photo route, the modal slot should render nothing.

```tsx
// app/gallery/@modal/default.tsx

export default function Default() {
  return null;
}
```

This means visiting:

```text
/gallery
```

will show the gallery without a modal.

---

#### The Normal Photo Page

We still need a normal route for the photo.

```tsx
// app/gallery/photo/[id]/page.tsx

type PhotoPageProps = {
  params: Promise<{
    id: string;
  }>;
};

export default async function PhotoPage({ params }: PhotoPageProps) {
  const { id } = await params;

  return (
    <main>
      <h1>Photo {id}</h1>
      <p>Full photo page.</p>
    </main>
  );
}
```

This route is important because the photo should still have a real URL.

If someone directly visits:

```text
/gallery/photo/123
```

they should get the full photo page rather than depending on the gallery modal.

---

#### The Intercepted Photo Route

Now we create the intercepted version:

```tsx
// app/gallery/@modal/(.)photo/[id]/page.tsx

type ModalPhotoProps = {
  params: Promise<{
    id: string;
  }>;
};

export default async function ModalPhoto({ params }: ModalPhotoProps) {
  const { id } = await params;

  return (
    <div className="fixed inset-0 flex items-center justify-center bg-black/50">
      <div className="relative rounded-lg bg-white p-6">
        <h2>Photo {id}</h2>

        <p>Photo displayed inside a modal.</p>
      </div>
    </div>
  );
}
```

Now the same photo route has two possible experiences.

##### Direct navigation

If the user directly visits:

```text
/gallery/photo/123
```

Next.js renders:

```text
gallery/photo/[id]/page.tsx
```

and shows the full photo page.

##### Client-side navigation from the gallery

If the user clicks a photo while already on:

```text
/gallery
```

the intercepted route can render the photo inside:

```text
gallery/@modal/(.)photo/[id]/page.tsx
```

The gallery remains visible underneath.

---

#### Navigating to the Photo

The gallery can use Next.js `Link` for navigation:

```tsx
import Link from "next/link";

export default function Gallery() {
  const photos = ["1", "2", "3"];

  return (
    <div className="grid grid-cols-3 gap-4">
      {photos.map((id) => (
        <Link key={id} href={`/gallery/photo/${id}`}>
          <div className="rounded border p-8">Photo {id}</div>
        </Link>
      ))}
    </div>
  );
}
```

The important detail is that the link points to the **real photo URL**:

```tsx
href={`/gallery/photo/${id}`}
```

The intercepted route changes **how the navigation is presented**, not what the URL represents.

This is one of the biggest advantages of the pattern.

---

#### Closing the Modal

Because the modal is associated with a real URL, closing it can navigate back to the previous page.

A simple approach is to use `router.back()`:

```tsx
"use client";

import { useRouter } from "next/navigation";

export function CloseButton() {
  const router = useRouter();

  return <button onClick={() => router.back()}>Close</button>;
}
```

This gives the user a natural browser-history experience.

For example:

```text
/gallery
    ↓
/gallery/photo/123
    ↓
Close
    ↓
/gallery
```

The browser Back button can also provide the expected behavior.

---

### Why Use Both Patterns?

Parallel Routes and Intercepting Routes solve different problems.

#### Parallel Routes

Answer:

> **"How can I render multiple route segments at the same time?"**

For example:

```text
children + modal
children + sidebar
children + dashboard panel
```

#### Intercepting Routes

Answer:

> **"How can I render a different route inside the current UI instead of replacing the current page?"**

For example:

```text
/gallery
    ↓
/gallery/photo/123

Render photo as a modal
```

#### Together

They allow us to build:

```text
                Parallel Route
                     │
                     ▼
Gallery ────────► Modal Slot
  │                  │
  │                  │
  └── Photo URL ◄────┘
       │
       ▼
Intercepting Route
```

This combination is particularly powerful for modal-based navigation.

---

## Tradeoffs

- **When this shines:** Modal-based navigation,Photo and media galleries, Dashboards, Admin applications, Split-view applications, Master-detail interfaces, Complex applications with multiple independent UI areas, and Applications where URLs should remain shareable while navigation feels modal.
- **When to avoid it:** Avoid using these patterns simply because they are available, For a simple application with straightforward page-to-page navigation, normal App Router navigation is usually easier to understand and maintain. For example, you probably don't need Parallel Routes for:

```text
Home → About → Contact
```

A normal route structure is enough.

- **What you give up:** The main tradeoff is **complexity**. You need to understand: Slots, `default.tsx`, Intercepting Route conventions, Layout behavior, Client-side navigation, Browser history, and Direct navigation versus intercepted navigation.
  The folder structure can also become more difficult to understand as the application grows.
  A good rule is:
  > **Use Parallel and Intercepting Routes when they solve a real UI/navigation problem, not just because they are advanced Next.js features.**

---

# Key Takeaways

- **Parallel Routes** allow multiple route segments to render simultaneously inside the same layout.
- Parallel Routes use **named slots**, created with the `@folder` convention.
- **Intercepting Routes** allow a route to be rendered within the current layout, which makes them especially useful for modals and drawers.
- The `(.)`, `(..)`, `(..)(..)`, and `(...)` conventions determine which route an Intercepting Route matches.
- **Parallel + Intercepting Routes** are a powerful combination for experiences such as photo galleries where a detail page should open as a modal while preserving the underlying page.
- Always provide a **normal route** for content that may be directly accessed or refreshed; the intercepted route should enhance the navigation experience rather than replace the canonical route.

---

## References

- [Next.js — Parallel Routes](https://nextjs.org/docs/app/building-your-application/routing/parallel-routes)
- [Next.js — Intercepting Routes](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes)
- [Next.js — Routing Fundamentals](https://nextjs.org/docs/app/building-your-application/routing)
- [Next.js — Linking and Navigating](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating)
