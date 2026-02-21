# Requirements

Before setting up **DQuode**, make sure your environment meets the following:

- **Python** — Version >= 3.10 is required.

!!! info "Native-first Dependencies"

These are included in `dquode/requirements.txt`:

- **`uvicorn`** — ASGI server for running the app
- **`aiomysql`** — Async MySQL driver (required if using MySQL)
- **`motor`** — Async MongoDB driver (required if using Mongo)
- **`pymongo`** — MongoDB support
- **`celery`** — Background task scheduler
- **`redis`** — Optional (used as Celery broker); not needed for basic CRUD

!!! tip "Installation"

Install all dependencies in one step:

```bash
pip install -r requirements.txt
```
