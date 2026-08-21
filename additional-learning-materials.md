# Additional learning materials

Extra official docs for when you want more than the ticket **Docs & tutorials**. Read the group for the sprint you are in; you will still use Python, React, and Docker later.

Each row sits in the sprint where you **first need it**.

## Sprint 1 — Foundation ([tickets](sprints/tickets/sprint-01-tickets.md))

| Topic | Material | Why you should study it |
| ----- | -------- | ----------------------- |
| Docker | [Docker Get Started](https://docs.docker.com/get-started/) | Containerize Postgres, the API, and the frontend the way Compose already does. |
| Web fundamentals | [MDN Learn Web Development](https://developer.mozilla.org/en-US/docs/Learn_web_development) | HTML, CSS, JS, and how the browser loads your Vite app before React takes over. |
| Python | [Python tutorial](https://docs.python.org/3/tutorial/) | The language of your FastAPI app, analytics functions, and tests. |
| FastAPI | [FastAPI tutorial](https://fastapi.tiangolo.com/tutorial/) | Routes, Pydantic validation, and `/docs` for the tracker API. |
| TypeScript | [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) | Types, props, and safer frontend modules for the tracker UI. |
| React | [React Learn](https://react.dev/learn) | Components, state, and effects you use on Sessions, Progress, and Insights. |
| React + TypeScript | [React: Using TypeScript](https://react.dev/learn/typescript) | Typed components and props once screens share UI pieces. |

## Sprint 2 — Auth, catalog, SQLAdmin ([tickets](sprints/tickets/sprint-02-tickets.md))

Postgres is in Compose from Sprint 1, but you design tables and the ORM here.

| Topic | Material | Why you should study it |
| ----- | -------- | ----------------------- |
| PostgreSQL | [PostgreSQL tutorial](https://www.postgresql.org/docs/current/tutorial.html) | SQL and relational basics before SQLAlchemy/Alembic hide them. |
| SQLAlchemy | [SQLAlchemy 2.0 tutorial](https://docs.sqlalchemy.org/en/20/tutorial/index.html) | Map `WorkoutSession`, measurements, and goals without writing every query by hand. |

## Sprint 3 — Sessions, clone, plans & calendar ([tickets](sprints/tickets/sprint-03-tickets.md))

Session/plan/calendar/clone and TanStack Query docs live in the [Sprint 3 tickets](sprints/tickets/sprint-03-tickets.md). Optional extras:

| Topic | Material | Why you should study it |
| ----- | -------- | ----------------------- |
| Date UI | [MDN `Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) | Calendar ranges and `session_at` display. |
| Server state | [TanStack Query docs](https://tanstack.com/query/latest/docs/framework/react/overview) | Caching sessions, plans, and calendar queries. |

## Sprint 4 — Analytics & progress ([tickets](sprints/tickets/sprint-04-tickets.md))

| Topic | Material | Why you should study it |
| ----- | -------- | ----------------------- |
| Python testing | [pytest getting started](https://docs.pytest.org/en/stable/getting-started.html) | Unit-test summary and streak math without clicking the UI (you will widen this in optional Sprint 6). |
| Data analysis | [pandas getting started](https://pandas.pydata.org/pandas-docs/stable/getting_started/) | Optional extra for notebooks, CSV experiments, or batch stats. Sprint 4 Must still uses pure Python in the API. |

## Sprint 5 — AI insights ([tickets](sprints/tickets/sprint-05-tickets.md))

| Topic | Material | Why you should study it |
| ----- | -------- | ----------------------- |
| Local AI | [Ollama quickstart](https://docs.ollama.com/quickstart) | Run a model on your machine and call it from Sprint 5 Insights. |
| Cloud AI option | [OpenAI developer quickstart](https://platform.openai.com/docs/quickstart) | Optional later if you replace the cloud stub. Not required, and no paid key for the course demo. |

## Sprint 6 — Testing, security & deployment (optional) ([tickets](sprints/tickets/sprint-06-tickets.md))

| Topic | Material | Why you should study it |
| ----- | -------- | ----------------------- |
| Testing frontend flows | [Playwright documentation](https://playwright.dev/docs/intro) | Optional browser smoke: login → create a session → open Progress. |
| Security | [OWASP Top 10](https://owasp.org/Top10/) | IDOR, auth, and injection risks you will document in `security.md` (ownership work starts in Sprints 2–3). |
| CI/CD | [GitHub Actions documentation](https://docs.github.com/en/actions) | Run documented tests on push/PR (also a Sprint 1 stretch if you start CI early). |
| Deployment | [Use Compose in production](https://docs.docker.com/compose/how-tos/production/) | Ship API + DB with Compose; matches `docs/deployment.md`, not a Next.js host. |
