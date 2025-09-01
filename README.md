TC-S World Mini (Vercel-Ready)

A tiny, deploy-ready Next.js (App Router) app with Tailwind and server-side Supabase. Ships with a demo market, a simple wallet (daily Solar grant), and a stub “Identify” UI for future multimodal work.

What’s inside

Next.js App Router + Tailwind + PostCSS

Server-side Supabase integration

Routes:

/ — Home

/market — SSR from tcs_items

/wallet — Cookie wallet id, balance, daily grant via POST /api/wallet/daily

/identify — Stub UI for multimodal estimate

API: /api/market/list, /api/market/item/[id], /api/wallet/*, /api/seed, /api/seed/demo

SQL bootstrap: sql/bootstrap.sql to create required tables & helpers

One-time setup
1) Supabase (schema)

Open Supabase → SQL Editor and run the contents of sql/bootstrap.sql.
This creates:

tcs_balances (wallet_id, kwh)

tcs_items (id, title, description, rays_price, owner_wallet)

helper functions: tcs_increment_balance, tcs_transfer, tcs_exec_sql

2) Environment variables

Set these in all environments (Development, Preview, Production):

SUPABASE_URL=<your-supabase-project-url>
SUPABASE_SERVICE_KEY=<your-supabase-service-role-key>
OPENAI_API_KEY=<optional for future Identify AI>

APP_ID=tcsnetwork.world.id
NEXT_PUBLIC_APP_ID=tcsnetwork.world.id
NEXT_PUBLIC_WORLD_LINK_BASE=https://world.org/mini-app

TCS_KWH_PER_SOLAR=4913
TCS_RAYS_SOLAR=10000
TCS_DAILY_SOLAR=1


Tip (Vercel): In an old project, go to Settings → Environment Variables, select them, then Copy to Project to instantly mirror into the new project.

3) Deploy to Vercel

Vercel → New Project → Import → upload this repo/zip.

Confirm env vars are present, then Deploy.

4) (Optional) Seed demo data

After deploying:

# preferred (as documented)
curl -X POST https://<your-domain>/api/seed/demo

# or the original seed route
curl -X GET  https://<your-domain>/api/seed


This inserts sample tcs_items and ensures tables exist.

Local development
# install
npm install

# run dev server
npm run dev    # http://localhost:3000

# build / start
npm run build
npm run start  # production mode on port 3000

# lint (non-blocking)
npm run lint

Routes & APIs
Pages

/ — Home

/market — Server-side render, fetches from tcs_items

/wallet — Shows wallet id & balance; “Daily Grant” calls /api/wallet/daily

/identify — Minimal placeholder for future multimodal features

API

GET /api/market/list — List items

GET /api/market/item/[id] — Get single item

GET /api/wallet/balance — Get balance for current cookie wallet

POST /api/wallet/daily — Add daily grant to wallet

POST /api/wallet/transfer — Transfer between wallets

GET /api/seed — Idempotent seed (GET)

POST /api/seed/demo — Idempotent seed (POST), mirrors /api/seed

Configuration notes

Next.js: Uses App Router and runtime = 'nodejs' in API routes.

TypeScript: next.config.js sets ignoreBuildErrors: true so production builds don’t block on types; adjust if you want stricter CI.

Supabase: Server client created via SUPABASE_URL and SUPABASE_SERVICE_KEY (service role). Rotate keys and restrict RLS in production as needed.

Units: Solar/Rays conversions are driven by the TCS_* env vars above.

Troubleshooting

Build succeeds locally but fails on Vercel
Double-check env vars are present in the Production environment (not just Preview/Development).

Seeding returns { ok: true } but no rows
Confirm you ran sql/bootstrap.sql and that the Supabase keys point to the correct project.

Wallet not updating
Verify cookies work on your domain and check Vercel Functions logs for /api/wallet/daily.
