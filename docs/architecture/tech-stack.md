## 🧩 **All-In Tech Stack**

**Path:**
`/docs/architecture/tech-stack.md`

---

### 🧠 Core Overview

| Layer                      | Technology                                                 | Role                                             |
| -------------------------- | ---------------------------------------------------------- | ------------------------------------------------ |
| **Frontend + BFF**         | Next.js 14 (App Router + TypeScript)                       | UI, SSR, SSG, Edge Middleware, API Routes        |
| **Backend (APIs)**         | FastAPI (Python 3.10+)                                     | Core business logic & microservices              |
| **Database**               | Neon (PostgreSQL)                                          | Serverless DB with branching & autoscaling       |
| **Cache & Queue**          | Redis (Upstash / Render Redis)                             | Caching, rate-limiting, background queues        |
| **Deployment Infra**       | Vercel + Render / Railway                                  | Frontend + BFF on Vercel; Backend APIs on Render |
| **Monitoring & Analytics** | Sentry + Vercel Analytics                                  | Error tracking, performance, logs                |
| **Auth & Security**        | NextAuth.js + JWT + Zod + Pydantic                         | End-to-end validation & auth                     |
| **ORM / DB Layer**         | Prisma / Drizzle (TypeScript) + SQLAlchemy (async, Python) | Typed database queries on both sides             |
| **CI/CD & Ops**            | GitHub Actions + Vercel/Render Pipelines                   | Continuous integration & deploy                  |
| **Edge Runtime**           | Vercel Edge Middleware                                     | Fast auth checks & routing at the edge           |

---

### ⚛️ Frontend + BFF (Next.js 14 / TypeScript)

- **Rendering:** Server + Client Components
- **Routing:** App Router (`app/`) with colocation
- **API Layer:** API Routes as BFF (`/app/api/*`)
- **State Mgmt:** Zustand + TanStack Query
- **Validation:** Zod
- **Auth:** NextAuth.js (OAuth / Magic Link / Credentials)
- **Styling / UI:** Tailwind CSS + Radix UI + shadcn/ui + Framer Motion
- **Data Access:** `@neondatabase/serverless` / Prisma / Drizzle
- **Caching:** Redis (Upstash SDK)
- **Analytics / Logging:** Sentry SDK + Vercel Analytics

---

### ⚙️ Backend (FastAPI / Python)

- **Framework:** FastAPI (async)
- **Schema Validation:** Pydantic
- **ORM:** SQLAlchemy (async) + asyncpg
- **Auth:** JWT (bearer tokens, refresh flow)
- **Caching / Queue:** Redis (RQ / Celery or Dramatiq)
- **API Docs:** Built-in Swagger + ReDoc
- **Background Tasks:** Celery with Redis Broker
- **Testing:** pytest + httpx
- **Monitoring:** Sentry SDK (Python)
- **Logging:** Structured JSON logging (Uvicorn / Loguru)
- **Deployment:** Render / Railway / Fly.io (autoscale enabled)

---

### 🗃️ Database Layer (Neon PostgreSQL)

- **Provider:** Neon (Serverless Postgres)
- **Features:** Branching (dev/test), auto-suspend/resume, scaling
- **Migrations:** Prisma / Alembic via SQLAlchemy
- **Access:** Secure connection via SSL URL
- **Backups & Analytics:** Integrated branching snapshots

---

### ☁️ DevOps / Infrastructure

- **Frontend Deployment:** Vercel (Next.js + Edge Functions)
- **Backend Deployment:** Render / Railway (Docker FastAPI container)
- **CI/CD:** GitHub Actions → Vercel / Render
- **Env Management:** `.env.production`, `.env.local`
- **Containerization:** Docker + docker-compose (local dev)
- **Reverse Proxy:** Nginx (if self-hosted)

---

### 🔐 Security & Validation

- Strict Zod + Pydantic validation chain
- JWT encryption & rotation
- HTTPS everywhere (Vercel / Render auto-TLS)
- Rate limit via Redis middleware
- Input sanitization (XSS, CSRF protection via NextAuth / middleware)

---

### 📊 Observability & Monitoring

- **Error Tracking:** Sentry (front + back)
- **Performance:** Vercel Analytics + FastAPI middleware timers
- **Logging:** Structured (Frontend: Edge Logs; Backend: JSON Logs)
- **Metrics (Optional Enhancement):** Prometheus + Grafana stack

---

### 💪 Developer Experience

- **Linting / Formatting:** ESLint + Prettier + Black (Python)
- **Type Safety:** TypeScript + Pydantic models
- **Testing:** Vitest (front) + pytest (back)
- **Local Dev:** Docker Compose spins up Next.js + FastAPI + Neon branch + Redis
