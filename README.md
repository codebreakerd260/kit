# fullstack-app

Opinionated scaffold for a Next.js (App Router) + FastAPI full-stack application, with Neon (Postgres) + Redis for persistence and caching. This repo holds the frontend (`apps/web`) and backend (`services/api`) plus infrastructure and docs to help you get started quickly.

## Contents

- `apps/web` — Next.js 14 app (App Router + TypeScript)
- `services/api` — FastAPI backend (Python, async SQLAlchemy)
- `infrastructure` — docker-compose, deployment helpers
- `docs` — architecture notes and quickstart guides

## Quickstart (Windows PowerShell)

These steps assume you have Node.js, pnpm (or npm), Python 3.11+, Docker Desktop (or WSL2 + Docker), and Git installed.

Open PowerShell as Administrator when required.

1. Clone and bootstrap

```powershell
git clone <repo-url> fullstack-app; cd fullstack-app
```

2. Frontend (Next.js)

```powershell
cd apps/web
# with pnpm
pnpm install
pnpm dev
# or with npm
# npm install
# npm run dev
```

Visit http://localhost:3000

3. Backend (FastAPI)

```powershell
cd ..\..\services\api
python -m venv .venv
# PowerShell activate
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

Visit http://127.0.0.1:8000/health

4. Optional: run local Postgres + Redis with Docker Compose

```powershell
cd ..\..\infrastructure
docker compose up -d
```

5. Proxy check (from Next)
   Set `BACKEND_URL` in `apps/web/.env.local` to `http://127.0.0.1:8000`, then visit:

```
http://localhost:3000/api/proxy
```

You should see a JSON response like `{ "status": "ok" }`.

## Environment variables

Create a top-level `.env.example` or app-specific `.env.local` files. Minimal variables used across the repo:

```
DATABASE_URL=postgres://user:pass@localhost:5432/appdb
REDIS_URL=redis://localhost:6379
JWT_SECRET=some-long-secret
BACKEND_URL=http://127.0.0.1:8000
NEXT_PUBLIC_API_URL=http://localhost:3000/api
SENTRY_DSN=
```

## Development workflow

- Frontend: `pnpm dev` (or `npm run dev`) in `apps/web`
- Backend: Activate venv and `uvicorn app.main:app --reload --port 8000` in `services/api`
- Local infra: `docker compose up -d` in `infrastructure`

## Tests

- Frontend: add tests under `apps/web` and run via Vitest or your chosen test runner
- Backend: pytest in `services/api`

## Docs and further reading

- Architecture overview: `docs/architecture/project_structure.md`
- Tech stack: `docs/architecture/tech-stack.md`
- Walkthrough & quickstart: `docs/architecture/walkthrough.md` and `docs/quickstart/fullstack_quickstart.md`

## Contributing

Please read and follow the docs under `docs/` before opening issues or PRs. Add any environment-specific notes to `.env.example` and update docs for new features.

---
