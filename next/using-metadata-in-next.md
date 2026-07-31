# Using Metadata in Next.js

> **Topic:** Next.js · **Level:** Beginner · **Author:** [@geliettech](https://github.com/geliettech)

## The Problem

Every web page contains more than just the visible content. Search engines, browsers, and social media platforms rely on **metadata** to understand what a page is about.

Without proper metadata, your application may have:

- Generic browser tab titles like **Next App**.
- Poor search engine optimization (SEO).
- Unattractive previews when links are shared on social media.
- Missing page descriptions and icons.
- Difficulty identifying pages in browser history and bookmarks.

In traditional React applications, developers usually manage metadata using libraries such as `react-helmet`. In Next.js App Router, metadata management is built into the framework, making it easier to define SEO-related information for every page.

## The Solution

Next.js provides a **Metadata API** that lets you define metadata at the layout or page level.

Metadata can be:

- **Static** – defined using the exported `metadata` object.
- **Dynamic** – generated at request time using `generateMetadata()`.

Metadata automatically renders appropriate HTML tags inside the document's `<head>`.

### Routing Metadata

Metadata follows the same hierarchy as the App Router.

You can define metadata in:

- `app/layout.tsx` (applies to every page)
- Nested layouts (applies only to routes within that layout)
- Individual pages (overrides parent metadata)

For example:

```
app/
├── layout.tsx
├── page.tsx
├── about/
│   └── page.tsx
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    └── settings/
        └── page.tsx
```

- `app/layout.tsx` provides metadata shared across the entire application.
- `dashboard/layout.tsx` overrides metadata only for dashboard pages.
- `dashboard/settings/page.tsx` can further customize its own metadata.

Metadata is **merged** from parent layouts down to child pages.

### Title Metadata

This is simplest metadata you can define is the page title.

```tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Home",
};

export default function HomePage() {
  return <h1>Home Page</h1>;
}
```

This generates:

```html
<title>Home</title>
```

### Adding a Description

```tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "About",
  description: "Learn more about our company.",
};

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

Generated HTML:

```html
<title>About</title>
<meta name="description" content="Learn more about our company." />
```

---

### Default Title Template

Instead of repeating your website name on every page, define a template in the root layout.

```tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: {
    default: "My Store",
    template: "%s | My Store",
  },
};
```

Now a page can simply define:

```tsx
export const metadata = {
  title: "Products",
};
```

The browser title becomes:

```
Products | My Store
```

If a page doesn't specify a title, the default becomes:

```
My Store
```

---

### Dynamic Metadata

Sometimes metadata depends on fetched data.

For example, an e-commerce product page should display the product's name in the browser title.

Instead of using a static object, export `generateMetadata()`.

```tsx
import type { Metadata } from "next";

type Props = {
  params: Promise<{ id: string }>;
};

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const { id } = await params;

  const product = await fetch(`https://api.example.com/products/${id}`).then(
    (res) => res.json(),
  );

  return {
    title: product.name,
    description: product.description,
  };
}

export default function ProductPage() {
  return <h1>Product Page</h1>;
}
```

Every product page now has unique metadata generated from its data.

### Other Common Metadata

Next.js supports much more than just titles.

```tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Blog",
  description: "Latest articles",

  keywords: ["Next.js", "React", "JavaScript"],

  authors: [
    {
      name: "Geliet Tech",
    },
  ],

  icons: {
    icon: "/favicon.ico",
  },

  robots: {
    index: true,
    follow: true,
  },
};
```

You can also configure:

- Open Graph metadata for Facebook and LinkedIn.
- Twitter Cards.
- Canonical URLs.
- Verification tags.
- Theme colors.
- Apple web app settings.
- Alternate languages.
- Manifest files.

These help improve SEO and how your pages appear when shared online.

## Tradeoffs

- **When this shines:** Building applications that need good SEO, social sharing, or meaningful browser titles.
- **When to avoid it:** Almost never. Metadata is lightweight and should be used for nearly every page.
- **What you give up:** Dynamic metadata may introduce additional data fetching, so avoid unnecessary requests if the information is already available.

## Key Takeaways

- Next.js includes a built-in Metadata API, so you don't need libraries like `react-helmet`.
- Metadata can be defined globally in layouts or locally in individual pages.
- Child routes inherit and can override metadata from parent layouts.
- Use `metadata` for static values and `generateMetadata()` for dynamic, data-driven pages.
- Well-defined metadata improves SEO, browser usability, and link previews.

## References

- [https://nextjs.org/docs/app/api-reference/functions/generate-metadata](https://nextjs.org/docs/app/api-reference/functions/generate-metadata)
- [https://nextjs.org/docs/app/building-your-application/optimizing/metadata](https://nextjs.org/docs/app/building-your-application/optimizing/metadata)
