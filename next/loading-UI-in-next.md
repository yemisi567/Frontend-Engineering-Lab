# Loading UI in Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

# Loading UI in Next.js

## The Problem

Modern web applications often fetch data from APIs, databases, or external services before a page can be displayed. Depending on network speed or server response time, users may have to wait several seconds before seeing the page.

Without a loading state, users are presented with a blank screen or an unresponsive interface, making the application feel slow or broken.

In traditional React applications, developers typically manage loading states manually using `useState`, `useEffect`, and conditional rendering.

```tsx
const [loading, setLoading] = useState(true);

if (loading) {
  return <Spinner />;
}
```

As applications grow, manually managing loading states for every page becomes repetitive and difficult to maintain.

The Next.js App Router solves this problem with the **`loading.tsx` convention**, allowing you to define loading interfaces for routes without writing extra loading state logic.

# The Solution

In the App Router, placing a `loading.tsx` file inside a route folder automatically creates a loading UI for that route.

Whenever the page is waiting for server-rendered data or asynchronous components to finish rendering, Next.js instantly displays the loading component.

For example:

```
app/
│
├── dashboard/
│   ├── page.tsx
│   └── loading.tsx
```

When a user visits `/dashboard`, Next.js behaves like this:

1. Navigation starts.
2. `loading.tsx` is rendered immediately.
3. `page.tsx` loads in the background.
4. The loading UI is automatically replaced with the completed page.

No additional state management is required.

### Creating a Loading UI

Create a `loading.tsx` file in the route folder.

```tsx
// app/dashboard/loading.tsx

export default function Loading() {
  return <p>Loading dashboard...</p>;
}
```

Your page can then fetch data normally.

```tsx
// app/dashboard/page.tsx

async function getUsers() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users");

  return res.json();
}

export default async function DashboardPage() {
  const users = await getUsers();

  return (
    <div>
      <h1>Dashboard</h1>

      {users.map((user: any) => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}
```

While `getUsers()` is fetching data, users will automatically see the loading component.

### Route-Level Loading

Each route can have its own loading UI.

```
app/
│
├── dashboard/
│   ├── loading.tsx
│   └── page.tsx
│
├── profile/
│   ├── loading.tsx
│   └── page.tsx
```

Visiting `/dashboard` displays the dashboard loading screen.

Visiting `/profile` displays the profile loading screen.

Each loading UI is isolated to its own route.

### Designing Better Loading Screens

A loading screen doesn't have to be plain text.

You can display:

- Skeleton placeholders
- Spinners
- Animated cards
- Placeholder avatars
- Loading tables
- Progress indicators

Example:

```tsx
// app/dashboard/loading.tsx

export default function Loading() {
  return (
    <div className="space-y-4 animate-pulse">
      <div className="h-8 w-56 rounded bg-gray-200" />

      <div className="h-20 rounded bg-gray-200" />

      <div className="h-20 rounded bg-gray-200" />

      <div className="h-20 rounded bg-gray-200" />
    </div>
  );
}
```

Skeleton loaders provide a better user experience because they preview the page layout while content is loading.

### How `loading.tsx` Works

The `loading.tsx` file is automatically wrapped in a React Suspense boundary by Next.js.

Conceptually, Next.js does something similar to:

```tsx
<Suspense fallback={<Loading />}>
  <Page />
</Suspense>
```

This means you don't need to manually add a Suspense boundary for route-level loading. Next.js handles it automatically.

### Best Practices

- Keep loading screens lightweight so they render immediately.
- Use skeleton loaders instead of generic spinners when possible.
- Make the loading layout resemble the final page to reduce perceived waiting time.
- Avoid fetching data inside `loading.tsx`; it should only display placeholder content.
- Create route-specific loading screens rather than using the same loader everywhere.

## Tradeoffs

- **When this shines:** Loading server-rendered pages, Displaying immediate feedback during route navigation, Building applications with streamed content, and Reducing boilerplate by avoiding manual loading state management.

- **When to avoid it:** When loading a small part of a page instead of the entire route. In such cases, use your own `Suspense` boundary around the specific component. For client-side interactions such as submitting forms or handling button clicks, where component-level loading states are more appropriate.
- **What you give up:** `loading.tsx` only applies to route segments, not arbitrary components. It doesn't replace all loading states; you'll still manage loading manually for client-side interactions and mutations.

## Key Takeaways

- `loading.tsx` provides automatic route-level loading UI in the Next.js App Router.
- Next.js displays `loading.tsx` immediately while the corresponding page is rendering or fetching data.
- Every route segment can define its own loading experience by adding a `loading.tsx` file.
- `loading.tsx` is automatically wrapped in a React Suspense boundary.
- Skeleton loaders usually provide a better user experience than simple text or spinner loaders.

## References

- [Next.js App Router Documentation](https://nextjs.org/docs/app)
- [Next.js Loading UI and Streaming Documentation](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming)
- [React Suspense Documentation](https://react.dev/reference/react/Suspense)
