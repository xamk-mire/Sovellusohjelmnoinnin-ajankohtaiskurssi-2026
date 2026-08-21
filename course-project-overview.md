# Exercise Progress Tracker — Project summary

**Exercise Progress Tracker** is a full-stack web application for logging **training sessions**, viewing personal progress on a **calendar**, and receiving AI-assisted feedback from *your own* history. It is the course project: one product built in stages (Sprints 1–5 required; Sprint 6 optional), not a set of disconnected labs.

How the course runs (tickets, reports, first commands): [course-overview.md](course-overview.md). Week-by-week guides: [sprints/README.md](sprints/README.md).

## What the product does

Members register and log in, then keep a **logbook**—not a flat list of “workouts.” A **session** has a name, optional time (`session_at` may be null for an unscheduled / program-definition session), status, optional notes and intensity, optional **`plan_id`**, and one or more **items** (exercises). Each item has **measurements** with **`planned_value`** and **`actual_value`** (both nullable), keyed by unit types from a shared catalog—for example Running → duration + distance; Bench press → reps + weight per set. There is no free-text `unit_hint`.

**Activities are individual exercises** (bench press, hammer curl, running), not a single coarse “Gym” blob. The seeded catalog uses exercise-level system types plus custom types members can create.

Reuse is **session clone**, not a separate template table: `POST /sessions/{id}/clone` deep-copies items and **keeps planned values**, clears **actual** values, and sets a new `session_at` (and optional name). Optional `source_session_id` is provenance only—editing the source later must **not** change existing clones.

Members assign sessions to **workout plans** via **`workout_sessions.plan_id`** (at most one plan per session). There is **no** `workout_plan_sessions` junction and no junction `sort_order` / weekday placement. Plan members are listed **`ORDER BY session_at ASC NULLS LAST`**. Plans may carry optional `start_date` / `length_weeks` as metadata only.

The **calendar** (month + week) shows dated sessions (`session_at` in range), optionally filtered by `plan_id`. Performing a day again is clone-onto-date. **Period goals** are keyed by **`unit_type_id`** (week/month targets for analytics/AI)—not the same as planned set values on a session.

From that history the app shows **user-scoped** summaries, time series, and streaks, then (Sprint 5) structured **insights** (`summary`, `strengths`, `recommendations`, `caveats`). User A must never see User B’s sessions, plans, calendar, stats, or insights.

## Stack (locked)

| Layer | Technology |
| ----- | ---------- |
| Frontend | React, TypeScript, Vite, React Router, Tailwind CSS; TanStack Query (from Sprint 3); Recharts (Sprint 4) |
| Backend | Python **FastAPI** (Uvicorn, Pydantic), layered `api` → `services` → `repositories` → `models`/`db` |
| Database | PostgreSQL, SQLAlchemy, Alembic; **SQLAdmin** at `/admin` (staff UI, separate from app JWT login) |
| Auth | bcrypt password hashes, JWT Bearer tokens, CORS locked to the SPA origin |
| AI | **Ollama** on the student’s machine; an `AIProvider` interface plus a **cloud stub** (no paid API key required) |
| Ops | Git monorepo (`backend/`, `frontend/`, `docs/`), Docker Compose (`db`, `api`, `web`), `.env` / `.env.example` |

The backend is **FastAPI only**. Next.js API routes, Express, and a separate Node or Python “analytics microservice” are not part of this design. Analytics live in the same FastAPI app as testable pure Python functions.

SQLAdmin uses env-based staff credentials. Application users use register / login / `me`. Those two logins stay separate.

## Analytics, then AI

Python computes the numbers the UI and the coach can trust: session counts, duration and distance from **measurement** slugs (prefer **actual** values when present), average intensity, per–activity breakdowns, progress series, and streaks (calendar-style metrics prefer sessions with a real `session_at` / completed work). Sprint 4 covers those functions with **pytest**. The API exposes `/stats/summary`, `/stats/progress`, and `/stats/streaks`, all scoped to the logged-in user. Recharts and Dashboard widgets display the same facts.

Sprint 5 does **not** ask the model to invent totals. The backend packs the member’s sessions, period goals, and Sprint 4 stats into a prompt and asks Ollama for structured notes. If Ollama is down or slow, logging, calendar, and charts still work. Rate limiting for `/ai/insights` belongs in optional Sprint 6.

Insights are general training comments with **caveats**, not medical diagnosis or professional health advice.

## Security, testing, and deployment

Personal training data needs real authorization, not only a login screen. Protected routes require a valid JWT. From Sprint 3, every get/update/delete of a session or plan (and calendar, stats, and insights) checks **ownership**—changing an ID in the URL must not leak another user’s locker (IDOR). Secrets stay in the environment; `.env` is gitignored.

**Testing:** Sprint 4 requires unit tests for analytics. Broader API tests (auth, ownership, clone, stats, mocked AI), light frontend tests (Vitest), and optional Playwright smoke tests are **Sprint 6** if you have time after the core demo.

**Deployment:** the course path is **Docker Compose** for API + PostgreSQL (+ frontend), documented in optional `docs/deployment.md`. Vercel / Netlify / Next.js hosts are not the target. GitHub Actions CI is a stretch, not a Sprint 1–5 Must.

## Out of scope (do not build these as Must)

Mobile apps, social feeds, wearables, websockets, pgvector / RAG chat over history, parsing natural-language notes into sessions, **third-party calendar sync** (Google Calendar, invites)—the **in-app** month/week calendar is in scope for Sprint 3—cloud LLM billing, swapping the backend for Express or Next.js, and a separate **session template** bounded context (`session_templates` / `is_template`). Keep the cloud AI provider as a stub; mention it in deployment docs only as future config.

## Why this project

The tracker is a portfolio-sized app that still fits a 14-week core: Git and Compose, a layered FastAPI API, relational design and migrations, JWT auth, session/plan/calendar CRUD with ownership and clone-as-snapshot, Python analytics you can test without the browser, charts, and local AI behind a swap-friendly interface. Optional Sprint 6 adds test breadth, a security write-up (including the `localStorage` JWT XSS trade-off chosen for easier debugging), and a deploy runbook.

A complete core demo is: Compose up → register → create a session with planned values → assign `plan_id` → clone onto a date → fill actuals → show Calendar → show Progress → request an Ollama insight → show a friendly failure when Ollama is stopped.
