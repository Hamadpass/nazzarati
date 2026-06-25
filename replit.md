# Nazarat.jo — نظّاراتي

Jordan's first nationwide optics marketplace — a promotional landing page connecting optician shops, wholesalers, and customers across all 12 governorates.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/landing run dev` — run the landing page (port 18150)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run typecheck:libs` — typecheck composite libs only
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string, `SESSION_SECRET` — cookie session secret

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Landing: React + Vite + Tailwind CSS, Tajawal Arabic font, teal theme
- API: Express 5 + Replit Auth (OIDC/PKCE)
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/landing/` — React+Vite promotional landing page (preview at `/`)
- `artifacts/api-server/` — Express 5 API server (preview at `/api`)
- `lib/db/src/schema/` — Drizzle schema: `auth.ts` (sessions+users), `registrations.ts` (shop+wholesaler registrations)
- `lib/api-spec/openapi.yaml` — source-of-truth API contract
- `lib/api-zod/src/generated/` — generated Zod schemas from OpenAPI
- `lib/replit-auth-web/` — browser auth hook (`useAuth()`)
- `artifacts/landing/src/index.css` — teal theme CSS variables
- `artifacts/landing/src/lib/i18n.tsx` — Arabic/English translations

## Architecture decisions

- **Contract-first API**: OpenAPI spec is the source of truth. Run codegen after any spec change.
- **B2B wholesale section** is visible to all but locks pricing behind shop owner registration.
- **Registration forms** are interest-capture only (no payment). Two types: shop owners + wholesalers.
- **Replit Auth** (OIDC/PKCE) is wired on the API server but the "Login" header link is a placeholder until a shop-owner dashboard is built.
- **No custom login forms** — Replit OIDC handles authentication.

## Product

- Public landing page with Arabic/English toggle (RTL/LTR)
- Product showcase from real WooCommerce CSV data (contact lenses)
- Wholesale B2B marketplace preview section (locked, shop owners only)
- Registration interest forms: shop owner onboarding + wholesaler onboarding
- Both forms POST to `/api/registrations/shop` and `/api/registrations/wholesaler` and persist to PostgreSQL

## User preferences

- Teal color theme (`--primary: 174 70% 35%`)
- Tajawal Arabic font via Google Fonts
- Arabic first (RTL default), with English toggle

## Gotchas

- Always run `pnpm --filter @workspace/api-spec run codegen` after editing `openapi.yaml`, then run `pnpm run typecheck:libs` to build the composite libs before leaf artifact checks.
- `replit-auth-web` requires `vite` as a devDependency for `import.meta.env` types — it is a composite lib.
- DB schema push uses `drizzle-kit push` — never run in production without reviewing the diff first.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
- See the `replit-auth` skill for auth flow details
