# Deployment

This section covers running DQuode in production: environment, app server, optional Celery, and config.

## Environment

- Set **ENVIRONMENT=live** so the app runs in production mode: reload off, trace stripped from responses (when applicable).
- Set **SERVER_PORT** (or use default) for the port the ASGI server listens on.
- Configure **DB_TYPE**, **DB_HOST**, **DB_PORT**, **DB_NAME**, **DB_USER**, **DB_PASSWORD** (and **DB_USE_TLS** if needed) for the production database.

## Running the app

1. Install dependencies: `pip install -r requirements.txt`.
2. Start the ASGI server (e.g. **uvicorn** via `python start.py` or `uvicorn index:app --host 0.0.0.0 --port 8000`). Use a process manager (systemd, supervisord, or a container) so the app restarts on failure.
3. Put a reverse proxy (e.g. Nginx, Caddy) in front for TLS and static assets if needed.

## Celery (if used)

- Run a **Celery worker:** `celery -A <app> worker` (replace `<app>` with your app module).
- For scheduled tasks, run **Celery beat:** `celery -A <app> beat`.
- Configure broker and backend (e.g. Redis) via env or config.

## Config and secrets

- Keep **.env** out of version control; inject env vars or use a secrets manager in production.
- Use **getconfig** and **registers** for CORS, auth, and custom codes/messages appropriate for the deployed environment.
