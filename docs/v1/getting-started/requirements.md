# Requirements

Before setting up **Dreema**, make sure your environment meets the following:

- **Python** — Version >= 3.10 is required.

!!! info "Native-first Dependencies"

These are included in `Dreema/requirements.txt`:

- **`uvicorn`** — ASGI server for running the app
- **`aiomysql`** — Async SQL driver (required for SQL backend; see [Supported database systems](supported-databases.md))
- **`motor`** — Async document DB driver (required for document backend)
- **`pymongo`** — Document backend support
- **`celery`** — Background task scheduler
- **`redis`** — Optional (used as Celery broker); not needed for basic CRUD

!!! tip "Installation"

Install all dependencies in one step:

```bash
pip install -r requirements.txt
```
