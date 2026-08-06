# RentIA

![Next.js](https://img.shields.io/badge/Next.js-15-black?logo=next.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)
![Vercel](https://img.shields.io/badge/Deploy-Vercel-black?logo=vercel&logoColor=white)
![Estado](https://img.shields.io/badge/Status-Hackathon%20MVP-orange)
![Licencia](https://img.shields.io/badge/License-No%20license-lightgrey)

> Herramienta orientada al mercado de alquileres argentino | Tool focused on the Argentine rental market.

AI-powered platform that makes renting in Argentina more transparent for tenants. Upload your lease, get a plain-language breakdown of the fine print, check if the asking price is fair for your area, and simulate future rent increases using official BCRA indices — all in one place.

> 🏆 Built in **8 hours** at a hackathon — [Live demo](https://rentia-gules.vercel.app)

---

## Table of contents

- [Hackathon context](#hackathon-context)
- [Screenshots](#screenshots)
- [Features](#features)
- [Tech stack](#tech-stack)
- [Folder structure](#folder-structure)
- [Getting started](#getting-started)
- [Environment variables](#environment-variables)
- [API endpoints](#api-endpoints)
- [Authors](#authors)
- [License](#license)

---

## Hackathon context

The challenge: **design, build and deploy an app from scratch in 8 hours**.

The problem we chose to solve: renting in Argentina is opaque for the tenant — contracts full of legal jargon, no clear price benchmarks, and rent adjustment indices that almost nobody knows how to calculate. RentIA combines AI analysis, real BCRA data and community-registered contracts to give tenants concrete information before they sign.

---

## Screenshots

### Contract review with AI

Full contract analysis: applicable legal framework, compliance check and risky clauses.

![Contract review — analysis result](docs/screenshots/revisar-contrato.png)

### Condition evaluation before signing

Describe what the landlord is asking for and the app compares it to what's standard in your area.

![Condition evaluation](docs/screenshots/evaluar-condiciones.png)

### Price comparison by neighbourhood

See where the asking price sits within the actual range of contracts registered on the platform.

![Price by zone — Barrio Martín, Rosario](docs/screenshots/precios-por-zona.png)

### Rent increase simulator

Month-by-month projection using real BCRA indices: when does it go up and by how much.

![Rent simulator — ICL index projection](docs/screenshots/simulador.png)

### Unauthenticated view

AI analysis is available to registered users (free account).

![Login required screen](docs/screenshots/sin-login.png)

---

## Features

- **AI contract review** — paste the text or upload the PDF. The app identifies the applicable legal framework based on the signing date (Ley 27.551, Ley 27.737 or DNU 70/2023) and returns: a plain-language summary, a point-by-point compliance check, risky clauses ranked by severity (high / medium / low) and suggested questions to ask the landlord. Requires a registered account.
- **Condition evaluation** — free-text description of what you're being asked for (deposit, guarantee, adjustment index, term), compared against what's typical in your chosen area and apartment size, with specific advice on what to review and what to negotiate.
- **Price comparison by zone** — enter the monthly price, neighbourhood and number of rooms; the app positions it within the range of real contracts registered on the platform (below / within / above typical) with the data source shown.
- **Rent increase simulator** — projects rent month by month for the full contract duration using real BCRA coefficients (ICL, CER, UVA, Casa Propia). Shows total cumulative cost, total percentage increase and an evolution chart.
- **Analysis history** — contract analyses are saved per user and accessible from `/historial`.
- **Email/password authentication** — registration and login handled by better-auth, no external OAuth required.

---

## Tech stack

| Layer | Technology |
|---|---|
| **Framework** | Next.js 15 (App Router) |
| **Language** | TypeScript |
| **AI** | Vercel AI SDK (`ai` v7, `@ai-sdk/react` v4) |
| **Auth** | better-auth v1.6 |
| **ORM** | Drizzle ORM v0.45 |
| **Database** | PostgreSQL |
| **UI / Components** | Base UI (`@base-ui/react`), Lucide React, class-variance-authority |
| **External data** | BCRA public API — Monetary Statistics v4.0 (no API key required) |
| **Analytics** | Vercel Analytics |
| **Deploy** | Vercel |
| **Package manager** | pnpm |

---

## Folder structure

```
RentIA/
├── app/
│   ├── actions/
│   │   └── analyses.ts           # Server actions: save and read user analyses
│   ├── api/
│   │   ├── analyze-contract/     # POST — AI contract analysis
│   │   ├── coeficiente/          # GET  — BCRA adjustment coefficient between two dates
│   │   ├── indices/              # GET  — latest BCRA index values
│   │   └── auth/[...all]/        # Auth routes (better-auth)
│   ├── historial/                # Saved analyses history page
│   ├── sign-in/
│   ├── sign-up/
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx                  # Main page (tabs: review / evaluate / prices / simulator)
├── components/
│   ├── contract-analyzer.tsx     # AI contract review
│   ├── price-compare.tsx         # Price comparison by zone
│   ├── rent-simulator.tsx        # Month-by-month rent simulator
│   ├── analysis-history.tsx      # Analysis history
│   ├── auth-form.tsx             # Login / register form
│   ├── site-header.tsx           # Main navigation
│   ├── hero.tsx
│   ├── user-menu.tsx
│   └── ui/                       # Base components (button, card, input, select, tabs…)
├── lib/
│   ├── auth.ts                   # better-auth configuration
│   ├── auth-client.ts            # Auth client (browser)
│   ├── bcra.ts                   # BCRA API integration
│   ├── ley-alquileres.ts         # Argentine rental law framework by signing date
│   ├── rent-data.ts              # Reference prices by zone (MVP data)
│   ├── utils.ts
│   └── db/
│       ├── index.ts              # Database connection (Drizzle + pg pool)
│       └── schema.ts             # Tables: user, session, account, verification, analyses
├── public/
├── docs/
│   └── screenshots/              # README screenshots
├── package.json
├── pnpm-lock.yaml
├── next.config.mjs
└── tsconfig.json
```

---

## Getting started

### Prerequisites

- Node.js ≥ 18
- pnpm — `npm install -g pnpm`
- A running PostgreSQL instance

### Steps

1. **Clone the repository**

```bash
git clone https://github.com/angelini-agus/RentIA.git
cd RentIA
```

2. **Install dependencies**

```bash
pnpm install
```

3. **Set up environment variables**

Create a `.env.local` file in the root with the variables listed in the [next section](#environment-variables).

4. **Push the database schema**

```bash
pnpm drizzle-kit push
```

> Creates the tables required by better-auth (`user`, `session`, `account`, `verification`) and the `analyses` table for the contract history.

5. **Start the development server**

```bash
pnpm dev
```

App will be running at [http://localhost:3000](http://localhost:3000).

---

## Environment variables

Create a `.env.local` file in the project root. **Do not commit this file.**

| Variable | Description |
|---|---|
| `DATABASE_URL` | PostgreSQL connection string. Format: `postgresql://user:pass@host:5432/rentia` [TODO: confirm exact variable name used in `lib/db/index.ts`] |
| `BETTER_AUTH_SECRET` | Random secret for signing sessions. Minimum 32 characters. |
| `BETTER_AUTH_URL` | App base URL. Locally: `http://localhost:3000`. On Vercel, inferred automatically from `VERCEL_URL`. |
| `OPENAI_API_KEY` | API key for the AI provider used by the Vercel AI SDK. [TODO: confirm exact provider] |

> `VERCEL_URL` and `VERCEL_PROJECT_PRODUCTION_URL` are injected automatically by Vercel on deploy; no need to define them manually.

---

## API endpoints

| Method | Route | Description |
|---|---|---|
| `POST` | `/api/analyze-contract` | Receives contract text and returns structured analysis: legal framework, compliance check, risky clauses, suggested questions. |
| `GET` | `/api/coeficiente` | Calculates the cumulative adjustment coefficient between two dates for a BCRA index. Params: `indice` (`icl` \| `cer` \| `uva` \| `casaPropia`), `desde` (YYYY-MM-DD), `hasta` (YYYY-MM-DD, optional). |
| `GET` | `/api/indices` | Returns the latest available values for BCRA indices (ICL, CER, UVA, Casa Propia). Source: Monetary Statistics v4.0. |
| `*` | `/api/auth/[...all]` | Authentication routes managed by better-auth (login, register, session, sign-out). |

---

## Authors

Built as a team during a hackathon (8 hours of development).

| Name | Profile |
|---|---|
| Agustin Angelini | [linkedin.com/in/agustin-angelini](https://www.linkedin.com/in/agustin-angelini) · [github.com/angelini-agus](https://github.com/angelini-agus) | 
| Franco Cuscianna | [linkedin.com/in/francocus](https://www.linkedin.com/in/francocus/) · [github.com/francocus](https://github.com/francocus) |

