# Exercise Progress Tracker — Course introduction

Welcome. This is the **start of the course**: what you are building, which tools you will use, how the sprints work, and how you should work.

Keep this page open in the first week. Commands, ports, and troubleshooting live in the root [README.md](../README.md). Week-by-week work lives in the [sprint index](sprints/README.md) and each sprint’s **tickets**. Extra official docs live in [additional learning materials](additional-learning-materials.md).

---

## What this course is

You will design and implement a full-stack web application: the **Exercise Progress Tracker**. It is not a collection of disconnected lab exercises. Each sprint unlocks the next part of one product, the way you would open a gym in stages:

1. **The building** — a running backend, database, and frontend shell
2. **The membership desk** — accounts, login, and a shared activity catalog
3. **The training floor** — personal sessions (planned/actual), clone, plans, and calendar
4. **The scoreboard** — progress stats and charts
5. **The house coach** — AI-assisted feedback from _your_ history (local Ollama)

By the end of the **required** course (Sprints 1–5), a classmate should be able to clone your repo, start Compose, register, log and clone sessions, use the calendar, see charts, and request insights—without you standing next to them.

Sprint 6 is **optional**: tests, a security write-up, and deployment notes if you have time after Sprint 5.

---

## What you are building

Members of the tracker:

- Register and log in
- Log **sessions** of **exercises** with **planned** and **actual** measurements from a shared unit catalog
- **Clone** a session to reuse prescriptions (planned values kept; actuals cleared)
- Assign sessions to **workout plans** via `plan_id` (ordered by `session_at`; no plan↔session junction table)
- Use an in-app **calendar** (month/week) driven by `session_at`
- Set **period goals** keyed by unit type (for example minutes or kilometres)
- See **their own** progress (summaries, charts, streaks)
- Get AI-assisted feedback from **their** history via **Ollama** on your machine (Sprint 5)

The gym metaphor is there to keep the sequence in mind. You cannot issue memberships before the doors open, and you cannot hang a scoreboard before anyone has trained.

### What a finished demo looks like

A typical end-of-course walkthrough:

1. Copy `.env.example` → `.env` and run `docker compose up --build`
2. Open the frontend, register, and log in
3. Create a multi-exercise session with planned values (for example bench press sets, or a run with planned distance)
4. Assign it to a plan (`plan_id`); **clone** it onto a calendar date and fill actuals; confirm another user cannot open your sessions
5. Open Calendar (month/week), then Progress / Dashboard, and show stats that match the logged data
6. Start Ollama, request a weekly insight, then stop Ollama and show a friendly failure

That path is the product. Login, catalog, sessions, calendar, charts, and AI are **later chapters**—Sprint 1 only ships the building.

---

## Tools and technologies

You will work across four layers. Learn each tool when the sprint needs it; you will keep using Git, Python, React, PostgreSQL, and Docker for the rest of the course.

### The stack at a glance

| Layer             | Tools                                               | What they are for                                                     |
| ----------------- | --------------------------------------------------- | --------------------------------------------------------------------- |
| **Frontend**      | React, TypeScript, Vite, React Router, Tailwind CSS | Single-page UI: pages, routing, and styling                           |
| **Backend**       | Python, FastAPI, Uvicorn, Pydantic                  | HTTP API, validation, and interactive docs at `/docs`                 |
| **Data**          | PostgreSQL, SQLAlchemy, Alembic, SQLAdmin           | Persistent data, migrations, and a staff browser UI at `/admin`       |
| **Auth**          | bcrypt, JWT, CORS                                   | Register / login / “who am I?”, and locking the API to your UI origin |
| **Frontend data** | TanStack Query                                      | Fetch, cache, and update sessions and plans from the API              |
| **Analytics**     | Pure Python + pytest, Recharts                      | Testable stats, then charts and dashboard widgets                     |
| **AI**            | Ollama, `httpx`, an `AIProvider` interface          | Local insights from the member’s own history; cloud stays a stub      |
| **Ops**           | Git, Docker Compose, `.env` / `.env.example`        | One repo, one command to start `db` + `api` + `web`                   |

Versions, URLs, and how to run the example stack: [README.md](../README.md).

### Architecture you will keep

The project is a **monorepo**: `backend/`, `frontend/`, and `docs/` in one Git repository. One clone and one pull request can change API and UI together.

The backend is **layered**. Outer layers may call inward; inward layers must not import outward:

```text
Your browser (React / TypeScript)
        REST + JWT
            │
        FastAPI
            │
   +--------+--------+--------+
   │        │        │        │
Postgres  Stats    SQLAdmin  AI (Ollama → cloud stub later)
```

Inside the API:

```text
api/           → HTTP only (routers, status codes)
services/      → business rules (no FastAPI imports)
repositories/  → database queries
models/ + db/  → ORM entities and engine / session
```

Even a tiny `/health` route should follow this shape so auth, sessions, and insights do not invent a second style later.

### When each tool appears

| When                                 | You add                                                                                                                                                                          |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sprint 1** (weeks 1–2)             | Git monorepo, root `.gitignore`, Python venv, FastAPI + Uvicorn, OpenAPI `/docs`, pydantic-settings, PostgreSQL, Docker Compose, React + TypeScript + Vite, React Router, `.env` |
| **Sprint 2** (weeks 3–5)             | SQLAlchemy, Alembic, psycopg, SQLAdmin, bcrypt, JWT, CORS, Login/Register UI, `localStorage` token                                                                               |
| **Sprint 3** (weeks 6–8)             | Nested REST + Pydantic schemas, TanStack Query, session clone, calendar UI, ownership checks (IDOR)                                                                              |
| **Sprint 4** (weeks 9–11)            | Pure Python analytics, pytest, Recharts, period filters on Progress and Dashboard                                                                                                |
| **Sprint 5** (weeks 12–14)           | Ollama, `AIProvider` protocol, `httpx`, structured JSON insights, soft failure when the model is down                                                                            |
| **Sprint 6** (optional, weeks 15–17) | Broader API tests, Vitest, rate limiting, `docs/security.md`, `docs/deployment.md`; stretch: GitHub Actions, Playwright                                                          |

You do **not** need a paid cloud AI key. Sprint 5 runs a model on your machine. A cloud provider exists only as a stub so the API is not glued to one vendor.

---

## How the course is organized

**Sprints 1–5 are required** (~14 weeks). **Sprint 6 is optional** (+3 weeks). Work in order: each sprint’s “done when” is the next sprint’s prerequisite.

| Sprint       | Weeks | You unlock                                  | Tickets                                         | Report                                            |
| ------------ | ----- | ------------------------------------------- | ----------------------------------------------- | ------------------------------------------------- |
| 1            | 1–2   | Compose, `/health`, React shell, README     | [tickets](sprints/tickets/sprint-01-tickets.md) | [template](sprints/templates/sprint-01-report.md) |
| 2            | 3–5   | Catalog, SQLAdmin, JWT auth                 | [tickets](sprints/tickets/sprint-02-tickets.md) | [template](sprints/templates/sprint-02-report.md) |
| 3            | 6–8   | Sessions, clone, plans, calendar, ownership | [tickets](sprints/tickets/sprint-03-tickets.md) | [template](sprints/templates/sprint-03-report.md) |
| 4            | 9–11  | Analytics, Progress charts                  | [tickets](sprints/tickets/sprint-04-tickets.md) | [template](sprints/templates/sprint-04-report.md) |
| 5            | 12–14 | Ollama insights                             | [tickets](sprints/tickets/sprint-05-tickets.md) | [template](sprints/templates/sprint-05-report.md) |
| 6 (optional) | 15–17 | Tests, security, deployment docs            | [tickets](sprints/tickets/sprint-06-tickets.md) | [template](sprints/templates/sprint-06-report.md) |

```text
Core:     Sprint 1 → Sprint 2 → Sprint 3 → Sprint 4 → Sprint 5
Optional:                                              └→ Sprint 6 (if time)
```

Ticket fields, Definition of Done, and the target folder layout: [sprints/README.md](sprints/README.md).

### Tickets

Each sprint has an expanded backlog under `docs/sprints/tickets/`. Copy tickets into GitHub Issues, GitLab, Azure Boards, or your course board if that helps your team. Keep the IDs (`S1-03`, `S5-07`, …) in branches, commits, and PR titles.

| Field       | Meaning                                             |
| ----------- | --------------------------------------------------- |
| **Must**    | Needed to finish that sprint                        |
| **Should**  | Do unless you are blocked                           |
| **Stretch** | Nice to have if you have time (`S1-S1`, `S1-S2`, …) |

Do **Must** first, then **Should**, then **Stretch**. Sprint 6 Must items apply only if you take Sprint 6.

Each ticket includes **New here** (concept + docs), **Your task**, **Instructions**, **Stay in scope**, **Hints**, **Acceptance criteria**, **Suggested paths**, and **Commit**. Stay in scope is there so you do not build Sprint 3 on week 1.

### Sprint reports

At the end of each sprint, copy that sprint’s template from [templates/](sprints/templates/README.md) to `docs/reports/sprint-0N.md` and fill it. That is a **process log** for graders: what you did, how you demoed it, what you learned—not a second README and not pasted source. Add screenshots only where the template asks for a demonstration. Crop secrets.

---

## What you need before week 1

Install these on your machine:

- **Git**
- **[Docker Desktop](https://www.docker.com/products/docker-desktop/)** (or Docker Engine + Compose)
- A code editor (for example VS Code or Cursor)
- A Git hosting account for your team repo (GitHub, GitLab, or what the course specifies)

**Optional** if you run the API or UI on the host instead of in Compose: Python 3.12+ and Node.js 20+. Compose is the recommended path; you do not need a local Postgres install.

You will install **Ollama** in Sprint 5, not on day one.

---

## How to work

1. Open that sprint’s **tickets** (from the [sprint index](sprints/README.md)). Do **Must** first, then **Should**, then **Stretch** if you have time.
2. When a ticket is done, create a **new Git commit** for it. Put the ticket ID in the message (see **Commit** on the ticket), for example `S3-10: enforce session ownership`. Do not mix unrelated tickets in one commit.
3. Use a branch per week or ticket, for example `sprint-01/compose-api`. Open a PR at the end of the week with a short note: what to run and what to click.
4. Never commit `.env` or secrets. Keep `.env` gitignored; update `.env.example` when you add variables.
5. Keep backend layers honest: routers stay thin (HTTP only); services hold rules; repositories own queries. Services must not import FastAPI.
6. From Sprint 3 onward, **authorization** matters as much as login: User A must not open User B’s session, plan, or stats by guessing an ID.
7. At the end of the sprint, copy the report template to `docs/reports/sprint-0N.md` and fill it.

A sprint is done when a classmate can follow your README (or the sprint tickets’ “What success looks like” / demo steps) without secret knowledge, Must tickets are done or deferred in writing, and the report is filled.

---

## Domain rules (keep these)

These are locked for the product. Do not “simplify” them away:

- Members log **sessions** with exercise **items** and **measurements**. Measurements use `planned_value` and `actual_value`. Period **goals** are keyed by `unit_type_id`, not free-text units. Do not invent `unit_hint`.
- Activities are **individual exercises** (catalog + custom). There is **no** separate session-template table—reuse is `POST /sessions/{id}/clone` (planned kept, actuals cleared; clones are snapshots).
- `session_at` may be **null**. Plans use `workout_sessions.plan_id` (no `workout_plan_sessions` junction). Order plan members by `session_at` (nulls last).
- In-app **calendar** is driven by `session_at` (optional `plan_id` filter). Do not build Google Calendar sync as Must.
- Stats, calendar, and Insights are **user-scoped**: never show User A’s data to User B.
- SQLAdmin login (env username/password) is **separate** from JWT app-user auth.

Talk about sessions and measurements—not a flat workout row. Values live on measurements from the Sprint 2 catalog.

---

## This repository vs your work

**This repository is a Sprint 1 example** (runnable shell). The codebase may also preview some Sprint 2 pieces (ORM models, seed, `/admin`) so you can see where the stack is heading. Treat those as a peek ahead.

- **Sprint 1 Must scope** stops at the shell: Compose, `/health`, React router, README.
- Later sprints are **your** work from the tickets. Do not assume sessions, stats, or Insights are already implemented just because an example file exists.

---

## How to start

You need Docker Desktop (or Docker Engine + Compose).

```bash
cp .env.example .env
docker compose up --build
```

Typical URLs after Compose is up (confirm in the README if your team changed ports):

| Service      | URL                                                          |
| ------------ | ------------------------------------------------------------ |
| Frontend     | [http://localhost:5173](http://localhost:5173)               |
| API health   | [http://localhost:8000/health](http://localhost:8000/health) |
| OpenAPI docs | [http://localhost:8000/docs](http://localhost:8000/docs)     |

Full commands, SQLAdmin login, local-without-Docker steps, and troubleshooting: [README.md](../README.md).

Then open **[Sprint 1 tickets](sprints/tickets/sprint-01-tickets.md)** and pick up the Week 1 work, starting with the monorepo layout and root `.gitignore`.

---

## Where to look

| What                            | Where                                                                         |
| ------------------------------- | ----------------------------------------------------------------------------- |
| Course introduction (this page) | [docs/course-overview.md](course-overview.md)                                 |
| Finnish introduction (external) | [docs/kurssikuvaus.md](kurssikuvaus.md)                                       |
| Product / project summary       | [docs/course-project-overview.md](course-project-overview.md)                 |
| Run the stack                   | [README.md](../README.md)                                                     |
| Sprint index                    | [docs/sprints/README.md](sprints/README.md)                                   |
| Tickets                         | [docs/sprints/tickets/](sprints/tickets/)                                     |
| Sprint reports                  | [docs/sprints/templates/](sprints/templates/README.md) → fill `docs/reports/` |
| Extra reading                   | [docs/additional-learning-materials.md](additional-learning-materials.md)     |

If a ticket’s **Docs & tutorials** are not enough, use the extra reading for the sprint you are in. You will still use Python, React, and Docker in every later sprint.
