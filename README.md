# Commerce

A Next.js storefront for a BigCommerce catalog: product grids, search, cart, wishlist, customer accounts, and checkout handoff. The UI is the late-2020 [Next.js Commerce](https://github.com/vercel/commerce) starter (ACME branding, sample Yeezy hero copy, English and Spanish). Pages are statically generated and revalidated; catalog, cart, and customer calls go through `@bigcommerce/storefront-data-hooks` and thin API routes under `pages/api/bigcommerce`.

## What Paulo added

`master` matches the Vercel starter at [`aa36894605babb7e381acdc150dae07f183b7928`](https://github.com/vercel/commerce/commit/aa36894605babb7e381acdc150dae07f183b7928) (8 December 2020, “Merge pull request #118 from vercel/fix-2”), file for file, including the previous README. Paulo Cheloni published that snapshot on 18 December 2020 as a single commit, `Initial commit` (`abea208`). GitHub does not record this repository as a fork. No later commit on `master` changes the storefront, branding, SEO config, or copy. The MIT copyright in `license.md` is Vercel, Inc., 2020.

The only other branch, `tech-stack-file`, adds `techstack.md` and `techstack.yml`. Those files were committed by stacksharebot in July 2024 and are not on `master`.

## Deployment

The GitHub repository homepage is [commerce.paulocheloni.vercel.app](https://commerce.paulocheloni.vercel.app). That host currently returns Vercel’s “deployment could not be found” page, so there is no working public demo.

## Tech stack

Versions are the ranges in `package.json`.

| Area | Libraries |
| --- | --- |
| App | Next.js `^10.0.3-canary.3` (pages router), React `^16.14.0`, TypeScript `^4.0.3` |
| Styling | Tailwind CSS `^1.9`, `@tailwindcss/ui` `^0.6.2`, CSS modules, PostCSS |
| Commerce | `@bigcommerce/storefront-data-hooks` `^1.0.2` |
| SEO and theme | `next-seo` `^4.11.0`, `next-themes` `^0.0.4` |
| UI extras | `keen-slider` `^5.2.4`, `@reach/portal`, `body-scroll-lock` |
| Tooling | Yarn 1 (`yarn.lock` v1), Prettier, `@next/bundle-analyzer` |

`next.config.js` allows images from `cdn11.bigcommerce.com`, sets locales `en-US` (default) and `es`, and rewrites `/checkout` and `/logout` (with and without a locale prefix) to BigCommerce API routes.

## Features

- **Home.** Featured products, best-selling marquees, a static hero, and a grid of newest products with categories and brands. `getStaticProps` revalidates every 4 hours.
- **Catalog.** Product pages at `/product/[slug]` with an image slider, price, description, option swatches, per-product Open Graph tags, and an add-to-cart action that opens the cart sidebar. Paths are generated per locale; unknown slugs use `fallback: 'blocking'`. Revalidate is 200 seconds.
- **Search.** `/search` queries the catalog and filters by category and brand (“designers”). Sort options are relevance, latest arrivals, trending, and price low-to-high or high-to-low. The navbar links “All”, “Clothes”, and “Accessories” to search URLs.
- **Cart and checkout.** A cart page and a slide-over cart. `/checkout` rewrites to the BigCommerce checkout handler.
- **Accounts.** Login, sign-up, and forgot-password modals; logout rewrite; a profile page that shows the signed-in customer’s name and email.
- **Wishlist.** List and per-product wishlist button, backed by the wishlist API route.
- **Orders and blog.** `/orders` renders a static empty state and does not load order history. `/blog` is static placeholder copy, not a CMS.
- **CMS pages.** `pages/[...pages].tsx` renders BigCommerce pages and revalidates hourly.
- **Chrome.** Locale switcher (English and Spanish), light/dark theme toggle, cookie-consent bar, and site-wide SEO from `config/seo.json` (title “ACME Storefront”, canonical URL `https://nextjs.org/commerce`).

API routes in `pages/api/bigcommerce` re-export handlers from the data-hooks package for cart, catalog products, checkout, customers (current customer, login, logout, signup), and wishlist.

## Project structure

```
assets/                 Tailwind entry CSS
components/
  auth/                 Login, sign-up, forgot-password views
  cart/                 Cart line item and sidebar
  common/               Layout, navbar, footer, search, locale switcher
  icons/
  product/              Product card, slider, detail view, swatches
  ui/                   Buttons, modal, grid, hero, and other primitives
  wishlist/
config/seo.json         next-seo defaults
lib/                    Search helpers, slug, cookies, click-outside, logger
pages/                  Routes, plus pages/api/bigcommerce/*
public/                 Icons, flags, and static images
.env.template           BigCommerce variable names
next.config.js          Images, i18n, rewrites
```

`docs/ROADMAP.md` contains only the heading “Roadmap”. `components/ui/README.md` is a one-line note that UI primitives should stay atomic.

## Run locally

The lockfile is Yarn 1, from late 2020. Node 14 matches this Next.js 10 snapshot; current Node releases often fail to install or build it.

```bash
yarn
cp .env.template .env.local
```

Fill in `.env.local` (names below), then:

```bash
yarn dev
```

The dev server is http://localhost:3000. Home, product, search, and CMS pages call BigCommerce during `getStaticProps`, so those routes fail without valid credentials.

```bash
yarn build   # production build
yarn start   # serve the production build
yarn analyze # production build with the bundle analyzer
```

`yarn analyze` sets `BUNDLE_ANALYZE=both`. `yarn find:unused` runs `next-unused` over `components`, `lib`, and `pages`.

## Environment variables

Required at dev and build time. Copy names from `.env.template` into `.env.local`. Values are store credentials; they are not committed.

- `BIGCOMMERCE_STOREFRONT_API_URL`
- `BIGCOMMERCE_STOREFRONT_API_TOKEN`
- `BIGCOMMERCE_STORE_API_URL`
- `BIGCOMMERCE_STORE_API_TOKEN`
- `BIGCOMMERCE_STORE_API_CLIENT_ID`

Optional, only for the analyzer script:

- `BUNDLE_ANALYZE`

## License

MIT. Copyright (c) 2020 Vercel, Inc. See `license.md`.
