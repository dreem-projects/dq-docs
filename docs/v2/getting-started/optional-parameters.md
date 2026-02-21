# Start the server

## No .env setup needed

No `.env` file is required for the first run. The server starts on the **default port 8888** and you can hit routes that don’t use the database immediately.

## How it works

`start.py` runs the ASGI app with **uvicorn**:
On startup you should see something like:

```text
======= Starting server =======
==> Redis: ready
==> Database: connected
==> Routes: 5 cached
```

These messages come from the ASGI lifespan in `index.py`: `AppContext.init()` connects the database (when configured) and Redis and caches routes. If you haven’t set up a database yet, startup may still succeed for routes that don’t use it; use `.env` when you need database connection.

## Optional parameters in terminal

`start.py` runs the ASGI app with **uvicorn** and supports configuration via **environment variables** (e.g. `.env`) and **command-line arguments** in `key=value` form.

### Server parameters

| Parameter     | How to set                                        | Default                              | Description                                                                           |
| ------------- | ------------------------------------------------- | ------------------------------------ | ------------------------------------------------------------------------------------- |
| **port**      | `.env`: `SERVER_PORT=8888`                        | `8888`                               | Port the server listens on.                                                           |
| **host**      | Hardcoded in `start.py`                           | `0.0.0.0`                            | Bind address (all interfaces). Change in `start.py` to restrict (e.g. `127.0.0.1`).   |
| **reload**    | `.env`: `ENVIRONMENT=live` or `ENVIRONMENT=local` | `True` (when `ENVIRONMENT` ≠ `live`) | Hot reload: **on** when `ENVIRONMENT` is not `live`, **off** when `ENVIRONMENT=live`. |
| **workers**   | Hardcoded in `start.py`                           | `4`                                  | Number of uvicorn worker processes. Change in `start.py` if needed.                   |
| **log_level** | CLI: `python start.py log_level=info`             | `debug`                              | Logging level. Any `key=value` arg is parsed; `log_level` is passed to uvicorn.       |

**Examples**
