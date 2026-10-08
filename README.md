# IRADAH

Production storefront for Iradah Clothing, live at [https://iradahclothing.com](https://iradahclothing.com).

This repository is [Fuzail Sohail](https://github.com/FUZAILSOHAIL)'s fork of [SyedHassanUlHaq/iradah](https://github.com/SyedHassanUlHaq/iradah).

## Tech stack

| Layer | What this repo uses |
| --- | --- |
| UI | React 18, TypeScript |
| Build | Vite 5 with `@vitejs/plugin-react-swc` |
| Styling | Tailwind CSS 3 and shadcn/ui (Radix primitives in `src/components/ui`) |
| Routing | React Router 6, with route components loaded lazily |
| Catalogue and checkout | Shopify Storefront API (GraphQL, `2025-07`) |
| Cart | Zustand, persisted in `localStorage` |
| Reviews | Judge.me public widget API |
| Document head | `react-helmet-async` |
| Prerender | Playwright (Chromium) after the production build |

`package.json` still carries the Vite + shadcn template name `vite_react_shadcn_ts`. Several template libraries are installed and unused by the storefront (for example TanStack Query is mounted in `src/App.tsx` and never queried).

## What the storefront does

Shoppers browse a live Shopify catalogue and check out on Shopify. The React app is the storefront; it does not run its own product database or payment flow.

**Catalogue.** The homepage is a hero, a scrolling marquee (shipping, fabrics, cash on delivery, returns), a grid of Shopify collections, the eight newest products, and a banner into the full catalogue. `/products` lists the catalogue and can sort by price or name. `/collection/:category` loads a collection by handle (with a few short-name aliases such as `mens` → `men`). `/product/:handle` is the product page: gallery, description, options, and reviews. `/products/:slug` redirects to `/product/:slug`.

**Cart.** A bag drawer adds, updates, and removes line items. The bag is stored locally, then recreated as a Shopify cart so the subtotal and total come from Shopify (including automatic discounts). Checkout opens the Shopify checkout URL.

**Sitemap, prerender, and SEO.** `npm run build` regenerates `public/sitemap.xml` from the live product and collection handles, builds the Vite app, then uses Playwright to snapshot each route into `dist/<route>/index.html`. Those snapshots keep the app script so the page still boots in the browser, and they give crawlers title, meta, JSON-LD, and visible HTML. Pages set their own title, description, canonical URL, Open Graph and Twitter tags, and JSON-LD (organization and website on the home page, `CollectionPage` on collections, `Product` with offers and an aggregate rating when reviews exist). `src/main.tsx` mounts with `createRoot` because each route fetches its data after load, so the prerendered HTML is a crawler snapshot rather than a hydrated tree.

**Reviews and help.** Product pages load Judge.me reviews for that Shopify product id. Static pages cover shipping, returns, the size guide, contact, privacy, and terms. The homepage newsletter field only shows a success toast; it does not send the address anywhere.

## My contributions

The catalogue, cart, Shopify checkout, Judge.me reviews, SEO tags, sitemap, and prerender pipeline are [Syed Hassan Ul Haq](https://github.com/SyedHassanUlHaq)'s. The items below are the commits authored by Fuzail Sohail (`FUZAILSOHAIL`) on 11 and 12 August 2026.

**Hero text stays on the panel (11 Aug).** On large screens the homepage hero is a 42% / 58% split. The heading, copy, and buttons sit in the left column, with type and padding that step down between breakpoints, so the words no longer run onto the photograph.

**Footer, size guide, and collection pages (11 Aug).** The footer links the real Instagram and Facebook accounts, and links Shipping, Returns, Size Guide, and Contact. A strip states free shipping over PKR 5,000, cash on delivery, and 7-day returns. A size guide page shows charts for Pehchaan oversized tees, Empress oversized tees, flat-hem trousers, and joggers. Collection pages use the Shopify collection image as a hero, a short teaser, a piece count, and a “The story” section when the description is long enough. Product photos on cards are centered in the frame. The Lovable component tagger was removed from Vite and from `package.json`, and the prerender script starts `npx` in a way that works on Windows.

**Help pages, size-chart images, and collection hero crop (11 Aug).** Contact, Shipping, Returns, Privacy, and Terms are each their own page, replacing a single shared policy stub. The bottoms charts point at the current Shopify image files. On a product page, a size-chart image that fails to load is hidden. Collection heroes keep the middle of the photo and crop the top and bottom.

**Colour and size are required (11 Aug).** “Add to bag” on a product card opens a picker. The product page starts with no size or colour selected. Options that are out of stock are struck through. The button stays disabled until every option is chosen and that variant can be sold.

**Scroll, collection type, and stock limits (12 Aug).** A route change scrolls to the top. Collection hero titles and teasers scale with the viewport, and the hero crop is lighter on small screens (15% off the top and bottom) than on desktop (30%). Quantity from the product page, the card, and the bag drawer cannot pass Shopify’s `quantityAvailable` for that variant. At the cap the bag says how many are left, and asking for more shows a limited-stock message.

**Azaadi sale (12 Aug).** A green bar above the navigation scrolls “AZAADI SALE”, “14% OFF SITEWIDE”, “DISCOUNT APPLIED AUTOMATICALLY”, and “14 AUGUST”. The homepage hero carries a circular 14% stamp, and a sale banner sits under the hero. In-stock product cards show a 14% OFF badge. The copy says checkout applies the discount; this UI does not calculate a price itself.

## Local setup

Node.js and npm. From the repo root:

```bash
npm install
npm run dev
```

`npm run dev` starts Vite on port **8080** (`vite.config.ts`).

| Script | What it runs |
| --- | --- |
| `npm run dev` | `vite` |
| `npm run build` | `prebuild` writes `public/sitemap.xml` from Shopify, then `vite build`, then `postbuild` prerenders routes with Playwright |
| `npm run build:dev` | `vite build --mode development` (no sitemap step, no prerender) |
| `npm run preview` | `vite preview` |
| `npm run sitemap` | `node scripts/generate-sitemap.mjs` |
| `npm run lint` | `eslint .` |

`npm run build` and `npm run sitemap` call the Shopify Storefront API, and the prerender step launches Chromium, so those commands need network access and the Playwright browser.

## Screenshots

No screenshots are committed. The names below are placeholders. Add the PNG files under `docs/screenshots/` when you have captures; until then, nothing at those paths is an image. See [docs/screenshots/README.md](docs/screenshots/README.md).

| Placeholder | Intended file | What to capture |
| --- | --- | --- |
| Homepage | `docs/screenshots/home.png` | Hero with the Azaadi stamp, sale banner, and collection grid |
| Collection | `docs/screenshots/collection.png` | A collection hero (cropped middle band) and the product grid |
| Product | `docs/screenshots/product.png` | Product page before a size and colour are selected, including a struck-through sold-out option if one is on screen |
| Bag | `docs/screenshots/cart.png` | Bag drawer at the stock cap (“Only N left”) |
| Size guide | `docs/screenshots/size-guide.png` | Size guide with the tee and bottoms charts |
| Help | `docs/screenshots/help.png` | Shipping or contact page |
