# Walkthrough — step-by-step (easy to scan)

Nice — I’ll break this into short, actionable chunks so it’s easier to follow. Read the numbered list top → down, and use the checklist at the end to keep momentum.

---

## 1) Very short summary (one sentence)

User/browser → **Next.js (Vercel)** BFF and UI → **FastAPI (Render)** backend → **Neon (Postgres)** as single source of truth; **Redis** for cache/queues; Sentry + Vercel Analytics for observability; GitHub Actions → Vercel/Render for CI/CD.

---

## 2) How a typical request flows (three common cases)

**A. SSR page load**

1. Browser requests `/app/page` on Vercel.
2. Next.js Server Component runs server-side, calls internal BFF API (e.g. `/api/session`) to resolve user/data.
3. BFF API proxies/aggregates calls to FastAPI (auth check, DB read).
4. FastAPI queries Neon, maybe Redis, returns JSON → Next.js renders HTML → response to browser.

**B. Client-side data fetch (SPA navigation)**

1. Client uses TanStack Query / fetch to call `/api/posts` (Next API route).
2. Next API route (BFF) validates cookie/JWT, forwards request to FastAPI endpoints if needed, optionally enriches/caches results.
3. FastAPI returns data from Neon → BFF returns simplified JSON to client.

**C. Auth/signup (magic link/JWT)**

1. NextAuth (or custom route) hits FastAPI to create user and issue JWT.
2. FastAPI returns user + tokens (set HttpOnly cookie or returns refresh token).
3. BFF/Edge middleware reads cookie for SSR and attaches user to requests.

---

## 3) Minimal local dev setup (quick commands + structure)

**Suggested repo layout**

```
/apps/web                # Next.js (app router)
  /app
  /components
  /lib
  middleware.ts
  package.json

/services/api            # FastAPI
  app/
    main.py
    routes/
    core/
    models/
  requirements.txt
  Dockerfile

/infrastructure
  docker-compose.yml
/docs
  architecture/tech-stack.md
```

**Quick scaffolds**

- Next.js:

  - `pnpm create next-app@latest apps/web --ts` _(or `npx create-next-app@latest apps/web --ts`)_

- FastAPI:

  - `python -m venv .venv && source .venv/bin/activate`
  - `pip install fastapi "uvicorn[standard]" sqlalchemy asyncpg pydantic`
  - create `services/api/app/main.py` with `app = FastAPI()` and `uvicorn main:app --reload`

**Docker (very short examples)**

- `services/api/Dockerfile` (sketch)

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn","app.main:app","--host","0.0.0.0","--port","$PORT"]
```

- `apps/web/Dockerfile` (sketch) — use Node 20 image and build Next.

**Env vars (example names)**

```
DATABASE_URL=postgres://...
NEON_URL=...
REDIS_URL=redis://...
JWT_SECRET=...
NEXT_PUBLIC_API_URL=https://your-vercel-app.vercel.app/api
SENTRY_DSN=...
```

---

## 4) Deployment & CI/CD (simple flow)

1. Push to GitHub.
2. **Vercel**: link `apps/web` repo — deploys Next.js automatically (App Router, Edge Middleware, API routes).
3. **Render / Railway / Fly**: connect `services/api` — build Docker or python buildpack, set `DATABASE_URL`, `REDIS_URL`, `SENTRY_DSN`.
4. Set secrets in both services (same env var names).
5. (Optional) GitHub Actions: run tests → on success trigger Vercel/Render deployments (but Vercel auto-deploy on push is simplest).

---

## 5) Observability & security (must-do when “all-in”)

- **Sentry**: initialize in Next and FastAPI; capture exceptions + performance traces.
- **Logging**: structured logs (JSON): use `pino`/`bunyan` on Node, `Loguru` or `structlog` on Python.
- **Rate limiting**: middleware in Next.js Edge or in FastAPI using Redis.
- **DB pool & serverless caution**: use async DB drivers and connection pooling appropriate for serverless DB (avoid one connection per request).
- **Auth**: HttpOnly cookies for session JWTs or refresh-token + access-token pattern; rotate refresh tokens.
- **Validation**: Zod (front) → Pydantic (back) for matching schemas.

---

## 6) Short prioritized implementation checklist (do this in order)

1. Scaffold repo + two apps (`apps/web`, `services/api`) and push to GitHub.
2. Create a local `docker-compose.yml` for Next + FastAPI + Postgres (or use a local Postgres image).
3. Implement a simple “hello world” API in FastAPI and a Next.js page that fetches it through `/api/proxy`.
4. Add DB models + migrations (Alembic or Prisma/Drizzle) and test simple CRUD.
5. Integrate auth: NextAuth or a simple JWT login + set HttpOnly cookie.
6. Add Redis (Upstash for managed, or local redis for dev) and implement one cached endpoint.
7. Add Sentry and basic structured logging.
8. Configure Vercel + Render and deploy; test end-to-end.

---

## 7) Common gotchas & quick fixes

- **CORS vs BFF:** use BFF pattern (Next API routes) so client never calls backend directly — avoids many CORS headaches.
- **Cookie domain & secure flag:** in dev use `localhost`, but production needs `domain` and `secure`.
- **Serverless DB connections:** serverless Postgres (Neon) may drop idle connections — use pooling/connection-manager libraries.
- **Auth token expiry:** implement refresh token flow (users’ sessions shouldn’t break on short-lived access tokens).
- **Edge vs Server code:** Edge runtime disallows some Node APIs — place only edge-safe code in `middleware.ts` or `edge` functions.

---

## 8) Tiny code snippets (copy-paste ready)

**Next API BFF proxy (app/api/proxy/route.ts)**

```ts
import { NextResponse } from "next/server";

export async function GET() {
  const res = await fetch(`${process.env.BACKEND_URL}/health`);
  const json = await res.json();
  return NextResponse.json(json);
}
```

**FastAPI minimal app (services/api/app/main.py)**

```py
from fastapi import FastAPI

app = FastAPI()

@app.get("/health")
async def health():
    return {"status":"ok"}
```

---

## 9) Next small wins (what to do in the next 2–4 hours)

- Initialize repo and scaffold both apps.
- Wire a single `/health` endpoint and fetch it from Next (end-to-end request).
- Commit + push → let Vercel/Render build to verify env / build steps.
