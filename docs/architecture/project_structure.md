# 🧭 Project Structure Overview
**Path:** `/docs/architecture/project-structure.md`

A reference layout for your **Next.js + FastAPI + Neon + Redis** full-stack app, aligned with the quickstart.

---

## 🏗️ Root Layout
```
fullstack-app/
├─ apps/                     # Frontend apps (Next.js, etc.)
│  └─ web/                   # Main Next.js application
│     ├─ app/                # App Router pages, routes & API endpoints
│     │  ├─ api/             # API routes (BFF, auth, proxy)
│     │  │  ├─ auth/         # NextAuth routes
│     │  │  ├─ proxy/        # Proxy routes to backend
│     │  │  └─ ...
│     │  ├─ layout.tsx       # Root layout
│     │  ├─ page.tsx         # Main entry page
│     │  └─ ...
│     ├─ components/         # Reusable UI components (Radix + shadcn)
│     ├─ lib/                # Utils (fetchers, db clients, helpers)
│     ├─ styles/             # Global CSS / Tailwind config
│     ├─ middleware.ts       # Edge middleware (auth, rate limit, etc.)
│     ├─ next.config.mjs     # Next.js config
│     ├─ package.json
│     └─ tsconfig.json
│
├─ services/                 # Backend microservices
│  └─ api/                   # FastAPI service
│     ├─ app/
│     │  ├─ main.py          # Entry point
│     │  ├─ routes/          # All route files (user, post, auth, etc.)
│     │  ├─ core/            # Core logic (config, dependencies)
│     │  ├─ models/          # SQLAlchemy models
│     │  ├─ schemas/         # Pydantic schemas
│     │  ├─ db/              # DB session, engine, migrations
│     │  └─ utils/           # Helpers, common utilities
│     ├─ requirements.txt
│     ├─ Dockerfile
│     ├─ alembic.ini         # Alembic migrations
│     └─ tests/              # pytest unit/integration tests
│
├─ infrastructure/           # Infrastructure configs
│  ├─ docker-compose.yml     # Local dev DB & Redis
│  ├─ vercel.json            # Frontend deploy config (optional)
│  └─ render.yaml            # Backend deploy config (optional)
│
├─ docs/                     # Internal documentation
│  ├─ quickstart/            # Setup guides
│  │  └─ fullstack-setup.md  # The quickstart doc
│  └─ architecture/          # Architecture references
│     └─ project-structure.md
│
├─ .github/                  # CI/CD pipelines
│  └─ workflows/
│     └─ deploy.yml
│
├─ .env.example              # Example env vars
├─ README.md                 # Overview and contribution guide
└─ LICENSE                   # License file
```

---

## ⚙️ Config Hierarchy
| File | Purpose |
|------|----------|
| `.env` | Base environment variables for local dev |
| `apps/web/.env.local` | Frontend-specific env vars |
| `services/api/.env` | Backend env vars (DB, Redis, JWT) |
| `.github/workflows/deploy.yml` | CI/CD pipeline configuration |

---

## 🧩 Key Conventions
- **Separation of concerns:** `apps` for UI, `services` for APIs, `infrastructure` for infra.
- **App Router (Next.js 14):** server + client components under `/app/`.
- **Async everywhere:** FastAPI uses async DB + asyncpg; Next.js fetches via async BFF routes.
- **Infra-first mindset:** Compose all services (DB, Redis) before app boot.
- **Docs-first culture:** Every major layer has a short markdown file under `/docs`.

---

## 🧠 Tips for Expansion
| Layer | Direction |
|-------|------------|
| Frontend | Add analytics dashboards, edge functions, and role-based routes |
| Backend | Add Celery or Dramatiq worker for async jobs |
| Database | Migrate to Neon production branches |
| Observability | Add Prometheus + Grafana for live metrics |
| Security | Harden CORS, headers, JWT rotation, and rate limiting |

---

✅ **Next Step:** Run through the [Fullstack Quickstart](/docs/quickstart/fullstack-setup.md) to set up each layer and deploy to Vercel + Render.

