# VISION CRAFT — Mining Intelligence Platform

AI + Space Technology decision-support prototype for manganese mining:
**predict reserves → detect risk → recommend action**.

Stack: Next.js (App Router) · PostgreSQL + Drizzle ORM · Tailwind · Leaflet · Recharts.
Demo data is seeded automatically on first request (idempotent, advisory-locked).

Demo login: `manager / vision123` (also `geologist`, `production`, `maintenance`, `admin`).

---

## Deploy option A — Vercel + Neon (recommended, ~5 min, free tier)

1. **Create a Postgres database** at <https://neon.tech> (or Supabase / Railway).
   Copy the connection string (it ends with `?sslmode=require`).
2. **Push the code to GitHub**
   ```bash
   git init && git add . && git commit -m "Vision Craft prototype"
   gh repo create vision-craft --private --source=. --push
   ```
3. **Import the repo at <https://vercel.com/new>** (framework auto-detected: Next.js).
4. In *Environment Variables* add:
   | Name | Value |
   |------|-------|
   | `DATABASE_URL` | your Neon connection string |
   | `DATABASE_SSL` | `true` |
5. Click **Deploy**.
6. **Create the tables** once, from your machine:
   ```bash
   DATABASE_URL="<neon url>" npx drizzle-kit push
   ```
   (Or set `DATABASE_URL` in `.env` and run `npx drizzle-kit push`.)
7. Open the Vercel URL → login → data seeds itself on the first page load.

## Deploy option B — Docker (any VPS / Render / Fly.io / Railway)

```bash
docker compose up --build -d      # Postgres + schema push + app on :3000
```

Single container against an external DB:
```bash
docker build -t vision-craft .
docker run -p 3000:3000 -e DATABASE_URL="postgresql://..." -e DATABASE_SSL=true vision-craft
# then once: DATABASE_URL="postgresql://..." npx drizzle-kit push
```

## Deploy option C — Render / Railway (git-based)

- Build command: `npm ci && npm run build`
- Start command: `npm run start`
- Env vars: `DATABASE_URL`, `DATABASE_SSL=true`
- Run `npx drizzle-kit push` once (Railway/Render shell, or locally with the DB URL).

## Local development

```bash
cp .env.example .env     # set DATABASE_URL
npm install
npx drizzle-kit push
npm run dev
```

Health check: `GET /api/health` → `{"ok":true}`
ML inference: `POST /api/predict` (Random Forest reserve model)

## Project map

```
src/app/login              cinematic login (impact → dashboard transition)
src/app/(app)/dashboard    KPI cards · mine map · production chart · AI insights
src/app/(app)/map          Leaflet manganese potential map (hero)
src/app/(app)/drillholes   drill logs + Random Forest model + live playground
src/app/(app)/forecast     12-month production forecast
src/app/(app)/risk         composite risk score
src/app/(app)/recommendations  rule-based AI recommendation engine
src/app/(app)/equipment    fleet health / anomaly detection
src/lib/ml.ts              RandomForestRegressor, linear regression, risk scoring
src/lib/analytics.ts       reserve / forecast / risk / recommendation services
src/lib/seed.ts            synthetic prototype data (clearly labelled)
src/db/schema.ts           Drizzle schema
```

> Prototype output on synthetic demo data — not certified reserves.
