# Scheduler

Background tasks use **Celery**. The framework provides setup and a way to run async work from the app.

## Setup

- **dreema.scheduler** (or equivalent) configures Celery: broker and backend from env (e.g. Redis or another broker).
- Run a **Celery worker** and optionally **Celery beat** for periodic tasks. Start them separately from the ASGI app (e.g. `celery -A app worker` and `celery -A app beat`).

## runAsyncJob

- **runAsyncJob(coroutine)** (or similar) — Schedules a coroutine to run in the background (e.g. by sending a task to Celery). Signature and usage depend on the dreema scheduler API.

## Beat schedule

- **configureBeatSchedule** (or equivalent) — Registers periodic tasks (cron or interval). Define the schedule in code or config and ensure the beat process is running.

For exact function names and env vars (broker URL, etc.), check the scheduler module in dreema.
