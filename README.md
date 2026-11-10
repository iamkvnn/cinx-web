# cinx-web

The learner and instructor web app for Cinx, built with React 19, Vite, React
Router 7, TanStack Query, Zustand and Tailwind CSS v4. It replaces the Next.js
`cinx-frontend`. The app talks to the `cinx` backend through its gateway. Sign-in
goes through Keycloak (OIDC, Authorization Code + PKCE) using `oidc-client-ts`.

## Run locally

Prerequisites:

- Node 24 and pnpm 11.
- The `cinx` backend running locally: the gateway on `:9090`, and Keycloak on
  `:8180` with the `cinx` realm.
- The realm's `cinx-web` client must allow `http://localhost:5174`.

Copy `.env.example` to `.env.local` and adjust it if your backend runs elsewhere.
Then:

```bash
pnpm install
pnpm dev        # http://localhost:5174
```

| Variable | Default | Meaning |
|---|---|---|
| `VITE_API_URL` | `http://localhost:9090` | gateway base URL |
| `VITE_OIDC_AUTHORITY` | `http://localhost:8180/realms/cinx` | Keycloak realm; must equal the issuer the services validate |
| `VITE_OIDC_CLIENT_ID` | `cinx-web` | public OIDC client |

## Scripts

| Command | Does |
|---|---|
| `pnpm dev` | starts the dev server on port 5174 |
| `pnpm build` | type-checks (`tsc -b`) and builds for production into `dist/` |
| `pnpm lint` | runs ESLint |
| `pnpm preview` | serves the production build on port 5174 |

## Docker

```bash
docker build -t cinx-web \
  --build-arg VITE_API_URL=https://api.example.com \
  --build-arg VITE_OIDC_AUTHORITY=https://auth.example.com/realms/cinx .
docker run -p 8080:80 cinx-web
```

The `VITE_*` values are baked in at build time, so they are passed as build
arguments. nginx serves the SPA with an `index.html` fallback and a `/healthz`
endpoint.

## Structure

```
src/
├── app/        router, providers, layouts, route guards
├── features/   one folder per domain (auth, catalog, cart, checkout, learning, …)
│   └── <name>/ api/ · components/ · pages/
└── shared/     api (HTTP client, errors), config (env), ui, components, lib, stores, types
```

A feature may import from `shared/` and from another feature's `index.ts`.
`shared/` never imports a feature.
