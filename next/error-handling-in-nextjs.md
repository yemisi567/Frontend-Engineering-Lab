# Error Handling in Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

Applications don't always work as expected.

A network request may fail, a database query may throw an exception, a component may crash because of unexpected data, or a third-party service may become unavailable.

Without proper error handling:

- Users see a blank page or an unhelpful browser error.
- A single broken component can crash an entire page.
- Developers have little information for debugging.
- Users cannot recover without manually refreshing the page.

In traditional React applications, developers typically create **Error Boundaries** manually to catch rendering errors. While this works, it requires extra configuration and careful placement throughout the application.

Next.js simplifies this process by providing **file-based error handling**. By creating special files such as `error.tsx` and `global-error.tsx`, you can display friendly error pages, isolate failures to specific route segments, and even allow users to recover without leaving the page.

## The Solution

The App Router provides built-in support for handling errors at different levels of your application.

There are three primary ways to handle errors:

- Recovering from route errors using `error.tsx`
- Handling errors in nested routes
- Handling application-wide failures with `global-error.tsx`

### Recovering from Errors

To handle errors for a specific route segment, create an `error.tsx` file inside that route.

```
app
│── dashboard
│   ├── page.tsx
│   ├── error.tsx
│   └── loading.tsx
```

When an error occurs inside `dashboard/page.tsx` or any of its child components, Next.js automatically renders `error.tsx` instead of crashing the entire application.

#### Example

```tsx
// app/dashboard/error.tsx

"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>

      <p>{error.message}</p>

      <button onClick={() => reset()}>Try Again</button>
    </div>
  );
}
```

#### Why `"use client"`?

Error components must be **Client Components** because they:

- Receive the `error` object
- Use the `reset()` function
- Handle user interactions like button clicks

Without `"use client"`, the component cannot access these features.

---

#### The `error` object

The `error` parameter contains information about the failure.

```tsx
console.log(error.message);
```

Example output:

```
Failed to fetch data
```

During development, the error contains the full stack trace.

In production, Next.js intentionally hides sensitive details to improve security.



#### Recovering with `reset()`

One of the best features of `error.tsx` is the `reset()` function.

Instead of forcing users to reload the browser, `reset()` attempts to re-render the failed route.

```tsx
<button onClick={() => reset()}>Try Again</button>
```

This is useful for temporary problems such as:

- Network failures
- API timeouts
- Temporary server issues

---

### Handling Errors in Nested Routes

Error boundaries are **nested**.

Each `error.tsx` only catches errors inside its own route segment and child segments.

Example folder structure:

```
app
│── dashboard
│   ├── error.tsx
│   ├── page.tsx
│   └── settings
│       ├── page.tsx
│       └── error.tsx
```

Here:

- `dashboard/error.tsx` handles errors inside the dashboard.
- `settings/error.tsx` handles only errors inside the settings page.

If an error occurs in:

```
app/dashboard/settings/page.tsx
```

Next.js first looks for:

```
settings/error.tsx
```

If none exists, it bubbles up to:

```
dashboard/error.tsx
```

This allows different parts of your application to display customized error UIs.

---

#### Example

```
Dashboard
├── Analytics
├── Users
└── Settings
```

If the Settings page crashes, only the Settings section displays its error screen.

The rest of the Dashboard continues working normally.

This improves the user experience because one broken feature does not take down the entire application.

---

### Handling Global Errors

Some errors happen outside individual routes.

For example:

- The root layout crashes.
- The HTML document cannot render.
- A shared provider throws an exception.
- An application-wide component fails.

For these cases, create a `global-error.tsx` file.

```
app
│── global-error.tsx
│── layout.tsx
│── page.tsx
```

Unlike `error.tsx`, this file replaces the **entire application**.

---

#### Example

```tsx
// app/global-error.tsx

"use client";

export default function GlobalError({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <html>
      <body>
        <h1>Application Error</h1>

        <p>{error.message}</p>

        <button onClick={() => reset()}>Try Again</button>
      </body>
    </html>
  );
}
```

Notice that the component returns both:

```tsx
<html>
  <body>...</body>
</html>
```

Since this component replaces the entire application, it must render the root HTML elements.

---

### Throwing an Error

Errors can be thrown manually.

```tsx
export default async function Dashboard() {
  throw new Error("Failed to load dashboard");

  return <div>Dashboard</div>;
}
```

Next.js automatically displays the nearest `error.tsx`.

### Best Practices

- Keep error messages simple and user-friendly.
- Log errors to monitoring services like Sentry or LogRocket for debugging.
- Use `reset()` for recoverable errors such as failed network requests.
- Create nested `error.tsx` files for independent sections of large applications.
- Reserve `global-error.tsx` for failures that affect the entire application.
- Never expose sensitive server details or stack traces to users in production.

## Tradeoffs

Consider the following:

- **When this shines:** Large applications with multiple route segments. Applications that depend on APIs or databases. Dashboards and admin panels where isolated failures improve user experience. Production applications that require graceful error recovery.

- **When to avoid it:** Very small applications where a single error boundary is sufficient. Components that can safely handle errors with local conditional rendering instead of throwing exceptions.

- **What you give up:** Additional files (`error.tsx`, `global-error.tsx`) to maintain.Errors in event handlers (such as button clicks) are **not** caught by route error boundaries and should be handled using `try...catch`. Developers still need proper logging and monitoring to diagnose production issues.

## Key Takeaways

- Next.js provides built-in file-based error handling using `error.tsx` and `global-error.tsx`.
- `error.tsx` catches errors for a route segment and its children without crashing the rest of the application.
- Use the `reset()` function to let users retry rendering after a recoverable error.
- Nested routes can have their own error boundaries, allowing failures to be isolated to specific sections.
- Use `global-error.tsx` to handle application-wide failures that affect the root layout or entire app.

## References

- [https://nextjs.org/docs/app/building-your-application/routing/error-handling](https://nextjs.org/docs/app/building-your-application/routing/error-handling)
- [https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)
