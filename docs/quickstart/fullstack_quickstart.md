# 🚀 Full-Stack Quickstart
**Path:** `/docs/quickstart/fullstack-setup.md`

---

## 🧱 1. Create the base structure

```bash
mkdir fullstack-app && cd fullstack-app

# Frontend (Next.js + TypeScript)
pnpm create next-app@latest apps/web --ts

# Backend (FastAPI)
mkdir -p services/api/app && cd services/api
python -m venv .venv && source .venv/bin/activate
pip install fastapi "uvicorn[standard]" sqlalchemy asyncpg pydantic[dotenv]
deactivate
cd ../../
```

Project tree (after init):
```
fullstack-app/
├─ apps/
│  └─ web/
├─ services/
│  └─ api/
│     └─ app/
├─ infrastructure/
└─ docs/
```

---

## ⚙️ 2. Add sample code

### **FastAPI – `/services/api/app/main.py`**
```python
from fastapi import FastAPI
app = FastAPI()

@app.get("/health")
async def health():
    return {"status": "ok"}
```

Run locally:
```bash
cd services/api
source .venv/bin/activate
uvicorn app.main:app --reload --port 8000
```
→ Visit http://127.0.0.1:8000/health ✅

---

### **Next.js – `/apps/web/app/api/proxy/route.ts`**
```ts
import { NextResponse } from "next/server";

export async function GET() {
  const res = await fetch(`${process.env.BACKEND_URL}/health`);
  const json = await res.json();
  return NextResponse.json(json);
}
```

Set env (in `/apps/web/.env.local`):
```
BACKEND_URL=http://127.0.0.1:8000
```

Start Next:
```bash
cd apps/web
pnpm dev
```
→ Visit http://localhost:3000/api/proxy ✅  
You should see `{ "status": "ok" }`

---

## 🗃️ 3. Add Database + Redis (local docker)

Create `/infrastructure/docker-compose.yml`
```yaml
version: "3.9"
services:
  db:
    image: postgres:16
    container_name: postgres_db
    restart: unless-stopped
    environment:
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: dev
      POSTGRES_DB: appdb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7
    container_name: redis_cache
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

Start infrastructure:
```bash
cd infrastructure
docker compose up -d
```

---

## 🧩 4. Integrate ORM & Validation

**Next.js**
```bash
cd apps/web
pnpm add zod @neondatabase/serverless drizzle-orm
```

**FastAPI**
```bash
cd services/api
source .venv/bin/activate
pip install sqlalchemy alembic
deactivate
```

---

## 🔐 5. Authentication (NextAuth + JWT)

In Next.js:
```bash
pnpm add next-auth
```

Add `/apps/web/app/api/auth/[...nextauth]/route.ts`
```ts
import NextAuth from "next-auth";
import Credentials from "next-auth/providers/credentials";

const handler = NextAuth({
  providers: [
    Credentials({
      name: "Credentials",
      credentials: { username: {}, password: {} },
      async authorize(credentials) {
        if (credentials?.username === "demo") return { id: 1, name: "Demo User" };
        return null;
      },
    }),
  ],
  session: { strategy: "jwt" },
});

export { handler as GET, handler as POST };
```

---

## 📊 6. Observability (Sentry + Analytics)

**Frontend**
```bash
pnpm add @sentry/nextjs
```
Run:
```bash
pnpm sentry wizard -i nextjs
```

**Backend**
```bash
cd services/api
source .venv/bin/activate
pip install sentry-sdk
deactivate
```

Add to `/services/api/app/main.py`:
```python
import sentry_sdk
sentry_sdk.init(dsn="YOUR_SENTRY_DSN", traces_sample_rate=1.0)
```

---

## ☁️ 7. Deployment Setup

### **Frontend → Vercel**
```bash
vercel link
vercel --prod
```
Set env in Vercel Dashboard:
```
BACKEND_URL=https://your-render-app.onrender.com
SENTRY_DSN=...
```

### **Backend → Render**
1. Create a new **Web Service** → connect GitHub → select `services/api`.
2. Build command:  
   ```bash
   pip install -r requirements.txt && uvicorn app.main:app --host 0.0.0.0 --port 10000
   ```
3. Set environment:
   ```
   DATABASE_URL=...
   REDIS_URL=...
   SENTRY_DSN=...
   ```

---

## 🧰 8. CI/CD (GitHub Actions)

`.github/workflows/deploy.yml`
```yaml
name: Deploy Fullstack

on:
  push:
    branches: [ main ]

jobs:
  test-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: cd apps/web && pnpm install && pnpm lint && pnpm build
      - uses: actions/setup-python@v5
        with: { python-version: "3.11" }
      - run: cd services/api && pip install -r requirements.txt && pytest
```
*(Vercel & Render auto-deploy on push, so CI only validates before deploy.)*

---

## ✅ 9. Verify end-to-end
- Visit your **Vercel frontend** → page renders SSR content.
- `/api/proxy` should fetch live from **Render FastAPI**.
- Data persists in **Neon** or local Postgres.
- Logs/errors visible in **Sentry**.

---

## 🎯 10. You’re production-ready
Next steps:
- add role-based auth, background jobs, analytics dashboards  
- harden security headers (CSP, HSTS)  
- migrate DB to Neon production branch  
- enable Vercel Edge Middleware for JWT validation

