# Frontends: Reference & Procedure

This document describes the exact procedure I will follow (and you can follow) to build the eight unique cryptocurrency frontends in this workspace. It includes the environment variable to add to each project, Tailwind v4 notes, fetch examples, and 8 design patterns (one per site).

----

## Overview

- Root folder: `/Users/sahil/Desktop/frontends`
- Projects (already present): `blocknex`, `chainlytics`, `coinetic`, `coinova`, `nexara`, `numora`, `toknova`, `volnex`
- API endpoint to use (add to each project's `.env.local`):

```
NEXT_PUBLIC_API_URL="http://localhost:8080/api/v1/external/listings"
```

Notes:
- I use `NEXT_PUBLIC_API_URL` so both client and server code can access it easily. If you prefer only server-side reads, also add `API_URL` (without NEXT_PUBLIC) and use it from server components.

----

## High-level procedure (applies to every project)

1. Add `.env.local` at project root with the line shown above.
2. Add a small fetch helper and type definitions in `/lib` (e.g., `/lib/api.ts`) to fetch listings using `process.env.NEXT_PUBLIC_API_URL`.
3. Implement the UI according to the project's design (see the 8 unique patterns below). Keep the app router `app/page.tsx` as the main landing page that lists the 21 coins.


----

## Sample fetch helper (create `/lib/api.ts` or `/lib/api.js`)

Example (TypeScript-friendly):

```ts
export type CoinQuote = {
  price: number
  percent_change_1h: number
  percent_change_24h: number
  percent_change_7d: number
  market_cap?: number
  volume_24h?: number
}

export type Coin = {
  id: number
  name: string
  symbol: string
  slug: string
  cmc_rank?: number
  quote: { USD: CoinQuote }
}

export async function fetchListings() {
  const url = process.env.NEXT_PUBLIC_API_URL!
  const res = await fetch(url)
  if (!res.ok) throw new Error('Failed to fetch listings')
  const body = await res.json()
  return body.data as Coin[]
}
```

Usage in an App Router server component (`app/page.tsx`):

```tsx
import { fetchListings } from '../lib/api'

export default async function Page() {
  const coins = await fetchListings()
  return (
    <main>
      {coins.map(c => (
        <div key={c.id}>{c.name} — ${c.quote.USD.price}</div>
      ))}
    </main>
  )
}
```

If you prefer client-side fetching, use `useEffect` and `NEXT_PUBLIC_API_URL`.

----

## `.env.local` creation (one-liner you can run)

From `/Users/sahil/Desktop/frontends` run:

```bash
BASE="/Users/sahil/Desktop/frontends"
NAMES=(blocknex chainlytics coinetic coinova nexara numora toknova volnex)
for name in "${NAMES[@]}"; do
  echo "NEXT_PUBLIC_API_URL=\"http://localhost:8080/api/v1/external/listings\"" > "$BASE/$name/.env.local"
done
```

This will create a `.env.local` file in every project containing the required API URL.

----

## 8 Unique site patterns and implementation notes

Below are eight distinct listing UI approaches — one for each project. Each entry describes theme colors, layout, key components, and extra features. Use Tailwind utility classes and component files in `/components`.

1) **Coinova** — Clean dashboard (modern teal)
   - Theme: teal accents (#0ea5a4) on dark slate background.
   - Layout: header with search + grid of large cards (3 columns desktop).
   - Key components: `Header`, `CoinCard`, `PriceBadge`, `MarketSparkline` (small sparkline SVG).
   - Extras: sort dropdown (rank / market cap / price) and search by name/symbol.

2) **Blocknex** — Professional table (blue corporate)
   - Theme: deep blue (#0b63c5) with light backgrounds.
   - Layout: responsive table with columns: Rank, Name, Price, 24h%, Market Cap, Volume.
   - Key components: `Table`, `TableRow`, `PercentPill` (green/red).
   - Extras: client-side sorting and basic pagination (10 per page).

3) **Chainlytics** — Analytics-style cards (purple gradient)
   - Theme: purple gradient (#7c3aed -> #a78bfa).
   - Layout: cards in a 2-column layout with charts and percent change badges.
   - Key components: `AnalyticsCard`, `MiniChart`.
   - Extras: 7d percent trend indicator, filter by tag (e.g., layer-1).

4) **Toknova** — Minimalist list (light, airy)
   - Theme: soft gray + accent coral (#fb7185).
   - Layout: compact vertical list with avatar/symbol and price, suitable for mobile.
   - Key components: `ListItem`, `SymbolAvatar`.
   - Extras: swipeable list for mobile (touch-friendly CSS).

5) **Coinetic** — Spotlight page (card + details)
   - Theme: black background, gold accents (#f59e0b).
   - Layout: featured top coin large card, then list of rest.
   - Key components: `Spotlight`, `MiniStats`.
   - Extras: clickable coin to open detail modal (server component fetch per coin).

6) **volnex** — Volume-first UI (heatmap)
   - Theme: dark with neon green (#10b981) accents.
   - Layout: grid ordered by `volume_24h` with size/opacity mapping to volume.
   - Key components: `VolumeTile`, `Legend`.
   - Extras: color scale legend and toggle to show market cap instead.

7) **numora** — Compact table with inline charts
   - Theme: slate with cyan accents (#06b6d4).
   - Layout: narrow table with tiny sparkline in each row and quick action button.
   - Key components: `TinySparkline`, `RowActions`.
   - Extras: row highlight for top 5 coins.

8) **nexara** — Carousel / showcase
   - Theme: immersive gradient (indigo -> pink).
   - Layout: horizontally scrollable carousel of featured coins with big numbers.
   - Key components: `Carousel`, `FeatureCard`.
   - Extras: autoplay and manual drag; accessible controls.

----

## Developer notes & recommended file structure (per project)

```
/<project>
  /app
    page.tsx           # landing list page (App Router)
    /coin/[slug]/page.tsx  # optional coin detail
  /components
    Header.tsx
    CoinCard.tsx
    Table.tsx
  /lib
    api.ts
  /styles
    globals.css
  tailwind.config.cjs
  postcss.config.cjs
  package.json
  .env.local
```

Testing locally:

```bash
cd /Users/sahil/Desktop/frontends/coinova
npm run dev
# open http://localhost:3000
```

----

## Verification steps I will run once you ask me to continue

1. Create `.env.local` files in all 8 projects.
2. Create `tailwind.config.cjs` in each project (v4-compatible minimal config above).
3. Add `/lib/api.ts` helper in each project (copy the sample).
4. Add a baseline `app/page.tsx` server component that calls `fetchListings()` and renders a simple list (to verify API integration).
5. Run `npm run dev` in each project and verify the page renders without console errors.

----

If you'd like, I can now:

- create the `.env.local` files in all 8 projects, and/or
- create `tailwind.config.cjs` files in all 8 projects, and/or
- add the sample `/lib/api.ts` and a minimal `app/page.tsx` in one project to demo.

Tell me which of those to do next and I will apply the changes.

----

## Blocknex — Implementation Notes (updates made)

I implemented the Blocknex site as a working crypto listings frontend. Below is a concise record of the files I added/updated and the UI behaviors we discussed (so you — or another developer — can follow or reproduce them for the other projects).

Files added
- `.env.local` — contains `NEXT_PUBLIC_API_URL="http://localhost:8080/api/v1/external/listings"`
- `lib/api.ts` — fetch helper and TypeScript types for the coin listing API.
- `components/CoinTable.tsx` — client component with search, sorting, pagination, responsive rendering (table for md+, stacked cards for mobile), and a details modal opener.
- `components/PercentPill.tsx` — colored badge showing percent change with arrow.
- `components/Sparkline.tsx` — small synthetic SVG sparkline used as a visual hint (demo-only).
- `components/Modal.tsx` — client-side modal overlay used for coin details.

Important UI and behavior points
- The page uses server-side data fetching in `app/page.tsx` via `fetchListings()` and passes `initialData` into the client `CoinTable` component. This keeps initial rendering fast and avoids a flash of empty content.
- Responsive behavior:
  - On `md` and larger screens the coins render in a table with sortable columns.
  - On small screens the table is hidden and a stacked card list is shown with touch-friendly rows.
  - Search and sort controls stack vertically on mobile and line up horizontally on larger screens.
- Modal enhancements:
  - The modal overlay uses `bg-black/40 backdrop-blur-sm` to blur the background when open.
  - The modal container uses `border border-slate-200` (light) and `dark:border-slate-800` (dark) and a subtle background so it visually stands apart.
  - Close button padding increased for better touch target (`px-3 py-2`).
- Spacing/controls:
  - Search input and select use `px-4 py-2` for improved spacing and accessibility.
  - Pagination buttons were updated to `px-4 py-2`.

Notes about Tailwind
- I did not change your Tailwind config — I left Tailwind as-is per your earlier request.

How to run and verify (Blocknex)
1. Make sure your API is running at `http://localhost:8080/api/v1/external/listings` (the value in `.env.local`).
2. Start the dev server for Blocknex:

```bash
cd /Users/sahil/Desktop/frontends/blocknex
npm run dev
# open http://localhost:3000
```

3. Verify:
  - The table populates on desktop if the API returns the sample 21 coins.
  - On mobile the stacked cards appear instead of the table.
  - Clicking a row opens the modal; the background should blur and the modal should show market data.

Next steps (repeatable for other projects)
- Copy the `lib/api.ts` and `.env.local` to other projects.
- Create project-specific components following Blocknex patterns and reuse `PercentPill`/`Sparkline`/`Modal` where appropriate.
- If you want, I can now replicate the same baseline (env, lib helper, minimal `app/page.tsx`) to the remaining 7 projects so each has the same API wiring and a simple list page.

----

## Chainlytics — Implementation Notes (updates made)

I implemented the Chainlytics site as a responsive analytics frontend. Below is a concise record of the files I added/updated and the UI behaviors (including the Navbar) so you can reproduce the pattern for other projects.

Files added
- `.env.local` — contains `NEXT_PUBLIC_API_URL="http://localhost:8080/api/v1/external/listings"`
- `lib/api.ts` — fetch helper and TypeScript types for the coin listing API (copied from Blocknex).
- `components/MiniChart.tsx` — purple-themed SVG mini-chart used inside cards.
- `components/AnalyticsCard.tsx` — card component that shows name, price, percent badges and the `MiniChart`.
- `components/Navbar.tsx` — responsive navbar with compact logo, title and navigation links (shows links on larger screens).
- `app/page.tsx` — server page that fetches listings and renders a responsive grid of `AnalyticsCard` components.

Important UI and behavior points
- Theme: purple gradient accents and soft gradient background for the page.
- Layout: responsive card grid — 1 column on small screens, 2 on small/medium, 3 on large screens.
- Navbar: a lightweight, responsive `Navbar` was added and placed above the page header. It includes:
  - A compact gradient badge logo (left), the site title, and a set of navigation links that hide on small screens.
  - A translucent background with `backdrop-blur` so it sits visually above content.
- Data flow: same approach as Blocknex — server-side fetch in `app/page.tsx` with `fetchListings()` and `initialData` passed implicitly to the rendered cards.

How to run and verify (Chainlytics)
1. Ensure your API is running at `http://localhost:8080/api/v1/external/listings` (the value in `.env.local`).
2. Start the dev server for Chainlytics:

```bash
cd /Users/sahil/Desktop/frontends/chainlytics
npm run dev
# open http://localhost:3000
```

3. Verify:
  - The grid populates with cards for each coin when the API returns data.
  - The Navbar appears above the header and hides non-essential links on small screens.

Notes & lint hints
- The project may show Tailwind class suggestions (e.g., `bg-gradient-to-b` → `bg-linear-to-b`). These are lint suggestions and optional to change.


